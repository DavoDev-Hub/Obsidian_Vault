2026-09-19 22:50

Status:

Tags: [[Ansible]]

# Configuración de Ansible (ansible.cfg)

## ¿Para qué un ansible.cfg?

En vez de repetir flags en cada comando, Ansible lee un archivo de configuración con los valores por defecto: inventario, usuario SSH, timeouts, escalado, verbosidad... Es tu "preferencias del proyecto".

## Prioridad de búsqueda (de mayor a menor)

1. `ANSIBLE_CONFIG` (variable de entorno) — máxima
2. `ansible.cfg` en el directorio actual
3. `~/.ansible.cfg` (home del usuario)
4. `/etc/ansible/ansible.cfg` (sistema)
5. Valores por defecto de Ansible

> Regla: **lo que se ve primero, gana**. El `ansible.cfg` al lado de tu playbook es lo más común.

## Crear un ansible.cfg

```ini
[defaults]
inventory = inventory/staging/hosts.yml
remote_user = deploy
become = True
forks = 10
retry_files_enabled = False
gather_facts = False

[privilege_escalation]
become_method = sudo
become_user = root
```

## Las secciones más usadas

### `[defaults]`

|Opción|Qué hace|Ejemplo|
|---|---|---|
|`inventory`|Inventario por defecto|`inventory = inventory/hosts.yml`|
|`remote_user`|Usuario SSH por defecto|`remote_user = deploy`|
|`forks`|Nº de hosts en paralelo|`forks = 20`|
|`gather_facts`|Recoge facts al inicio|`gather_facts = False`|
|`host_key_checking`|Verifica fingerprint SSH|`host_key_checking = False` (solo lab)|
|`timeout`|Timeout conexión|`timeout = 30`|
|`stdout_callback`|Formato de salida|`stdout_callback = yaml`|
|`retry_files_enabled`|Crear archivos .retry|`retry_files_enabled = False`|
|`callback_whitelist`|Callbacks adicionales|`callback_whitelist = timer, profile_tasks`|
|`roles_path`|Dónde buscar roles|`roles_path = roles`|
|`collections_path`|Dónde buscar collections|`collections_path = ~/.ansible/collections`|
|`ansible_managed`|Marca en archivos template|`ansible_managed = Config gestionada por Ansible ({{ ansible_host }})`|

### `[privilege_escalation]`

```ini
[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

### `[ssh_connection]`

```ini
[ssh_connection]
ssh_args = -C -o ControlMaster=auto -o ControlPersist=60s
pipelining = True
```

- `ControlMaster/ControlPersist` reusa una conexión SSH → mucho más rápido en playbooks largos.
- `pipelining = True` evita crear ficheros temporales → más rápido y menos footprint.

> Una conexión SSH reutilizada (ControlMaster) + pipelining es la combinación de velocidad por excelencia.

### `[colors]` (tu salida bonita)

```ini
[colors]
ok = green
changed = yellow
failed = red
```

### `[vault]`

```ini
[vault]
vault_password_file = ~/.vault_pass        # archivo con la contraseña Vault
```

Ver [[Ansible Vault]].

## `ansible.cfg` por entorno (multi-entorno)

Puedes tener configs distintas por entorno y apuntar con env:

```bash
ANSIBLE_CONFIG=./ansible.cfg ansible-playbook ...
```

Y usar distintos `inventory` dentro del archivo según el entorno. Se amplía en [[Ansible en múltiples entornos]].

## Variables de entorno que la complementan

- `ANSIBLE_CONFIG` → apunta al archivo.
- `ANSIBLE_INVENTORY` → inventario.
- `ANSIBLE_REMOTE_USER`, `ANSIBLE_BECOME`...
- `ANSIBLE_VAULT_PASSWORD_FILE`.

Prioridad: **variable de entorno > ansible.cfg**. Si algo "no hace caso al .cfg", revisa si un env lo está pisando.

## Ver la configuración efectiva

```bash
ansible-config list        # valores por defecto de todas las opciones
ansible-config dump        # valores activos tras aplicar todos los .cfg
ansible-config view        # muestra los archivos de config encontrados
```

## Buenas prácticas

1. **`inventory` y `remote_user` en el .cfg** = menos flags repetidos.
2. `retry_files_enabled = False` evita basura de `.retry`.
3. `pipelining + ControlMaster` = playbooks notoriamente más rápidos.
4. Nunca subas `vault_password_file` con el password real; apunta a un archivo con permisos 0600.
5. En equipos: versiona `ansible.cfg` en el repo (config compartida), no passwords.

## Puntos clave

- Un solo `ansible.cfg` = menos flags y errores de "olvidé el inventario".
- Prioridad: env > cwd > home > /etc.
- `ansible-config dump` te dice qué estás usando de verdad.
- Es la base de proyectos reproducibles → [[Estructura de un proyecto Ansible]].

# References