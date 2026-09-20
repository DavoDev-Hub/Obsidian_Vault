2026-09-19 23:27

Status:

Tags: [[Ansible]]

# Seguridad en Ansible

## El problema real

Un gestionador de configuraciones es un **caballo de Troya legítimo**: si un atacante controla el controlador (o el inventario, o una variable), controla TODOS los servidores. Ansible no inventa seguridad: **te da las piezas** para que tú la construyas de forma consistente y auditable.

## Principio 1: Nada de secretos en claro

- Inventario y `group_vars`/`host_vars` **no llevan contraseñas** (solo referencias a vault o env vars).
- Contraseñas de sudo, claves de API, tokens, certificados privados → [[Ansible Vault]].
- Variables de entorno del host solo como *fallback* con `lookup('env', ...)`.

## Principio 2: Transporte y autenticación

|Capa|Recomendación|
|---|---|
|SSH|claves, **nunca** password inline; `ssh-keygen + ssh-copy-id`|
|Puerto|cambia/no publiques 22 en internet; firewall primero|
|Usuarios|`remote_user` dedicado (no root); sudo con NOPASSWD mínimo o `become` con vault|
|Controlador|maquina dedicada o contenedor con acceso restringido|
|Red|controlador en red interna / bastion; nunca en DMZ directa|

```ini
# ansible.cfg (ver [[Configuración de Ansible (ansible.cfg)]])
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=120s
```

> ControlMaster reutiliza conexión SSH = menos handshakes = menos superficie y más rápido.

## Principio 3: Inventario como superficie de ataque

El inventario dice QUIÉN es gestionado. Protégelo:
- grupo `all` nunca debe tener secretos en claro.
- Token de Cloud (AWS/GCP) vía vault o `env`.
- Un `group_vars/prod/all.yml` robado ≠ "acceso a las máquinas" si los secretos reales están cifrados.

## Principio 4: Privilegios escalonados

```yaml
- name: Cambio admin (requiere vault)
  become: true
  become_user: root
  vars:
    admin_pass: "{{ vault_admin_pass }}"   # del vault
  user:
    name: admin
    password: "{{ admin_pass | password_hash('sha512') }}"
```

- `become` **mínimo necesario**: usa `become_user` para bajar privilegios cuando la tarea no requiere root.
- Nunca uses `become: true` por defecto si la tarea no lo necesita.

## Principio 5: Validación y firmas (Supply Chain)

Ansible Galaxy instala roles de terceros → es software de terceros ejecutándose con tus privilegios:

```yaml
# requirements.yml  (ver [[Ansible Galaxy]])
collections:
  - name: community.general
    version: "9.0.0"            # PINEAR VERSIÓN
    source: https://galaxy.ansible.com
roles:
  - name: geerlingguy.nginx
    version: "3.4.0"
```

Recomendaciones:
- **Pin versiones** siempre (reproducibilidad + seguridad).
- Revisa el rol antes de usarlo (leer `tasks/main.yml`).
- Prefiere **collections oficiales/community reputadas**.
- En empresa: `ansible-galaxy collection install` desde un **mirror interno** y auditar SHA256.

## Principio 6: Menos superficie, más control

- `no_log: true` en tareas que impriman secretos/errores con datos sensibles.
- Evitar `debug` con `var` sobre variables que contengan passwords.
- Registros (`log`) de la ejecución: controlador centralizado (log server) con `callback`.
- Rotar secretos periodicamente: ver [[Ansible Vault]] y [[Ansible en múltiples entornos]].

### `no_log` ejemplo

```yaml
- name: Obtener token de API
  uri:
    url: https://api.ejemplo.com/token
    method: POST
  register: token_respuesta
  no_log: true
```

El módulo no imprime el cuerpo de la respuesta (p. ej. el token).

## Principio 7: Practica de threat-model

Antes de desplegar pregúntate:
1. ¿Qué pasa si roban el inventario? (nada útil si secretos = vault)
2. ¿Qué pasa si roban el controlador? (vault cifrado con passphrase NO en el repo)
3. ¿Puede una tarea escalar sin querer? (`become` mínimo)
4. ¿Está firmado/pineado lo que instalo? (requirements.yml)

## Puntos clave

1. **Secretos cifrados** (vault), nunca en claro.
2. **Transporte seguro** (SSH claves), usuario dedicado.
3. **Inventario = superficie**: protégelo igual que el controlador.
4. **Privilegios mínimos** con `become`.
5. **Supply chain**: versions pineadas + revisar roles.
6. **no_log** para datos sensibles.
7. **Auditable**: logs centralizados y rotación.

## Siguientes pasos

- Cifra los secretos → [[Ansible Vault]]
- Configura bien el controlador → [[Configuración de Ansible (ansible.cfg)]]
- Multi-entorno con seguridad por capas → [[Ansible en múltiples entornos]]
- Manejo de fallos de seguridad → [[Manejo de errores en Ansible]]
- Valida lo que escribes → [[Validación y linting en Ansible]]

# References