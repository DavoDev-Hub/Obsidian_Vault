2026-09-19 22:44

Status:

Tags: [[Ansible]]

# Ansible en múltiples entornos

## El problema real: "funciona en mi máquina"

El playbook que instalas en desarrollo no puede ejecutarse idéntico en producción: cambia **dónde** apunta, **qué** versiones, **qué** secretos, **cuántos** servidores. Los buenos proyectos Ansible lo resuelven con **separación de datos por entorno**, no con "copiar y pegar el playbook".

## La estrategia: un solo código, múltiples entornos de datos

```text
proyecto/
├── ansible.cfg
├── requirements.yml
├── inventory/
│   ├── production/
│   │   ├── hosts.yml
│   │   ├── group_vars/all.yml
│   │   ├── group_vars/webservers.yml
│   │   └── host_vars/web1.yml
│   └── staging/
│       ├── hosts.yml
│       └── group_vars/all.yml
├── plays/
└── roles/
```

El **código** (roles, playbooks) es el mismo. Lo que cambia es el **inventario + variables por entorno** que seleccionas con `-i`.

## Seleccionar entorno con `-i`

```bash
# Sobre staging
ansible-playbook plays/deploy.yml -i inventory/staging/hosts.yml

# Sobre producción (seleccionas explícitamente)
ansible-playbook plays/deploy.yml -i inventory/production/hosts.yml
```

> Nunca mezcles entornos en un solo inventario permanente; es la fuente de errores "reinicié prod creyendo que era test".

## `group_vars`/`host_vars` por entorno (el patrón clave)

Con folders por entorno, `group_vars` y `host_vars` se resuelven **relativos al inventario** que cargas. Ejemplo de lo que cambia:

`inventory/staging/group_vars/webservers.yml`:
```yaml
app_version: "2.4.1-beta"
app_debug: true
db_host: "db-staging.internal"
api_base_url: "https://staging.example.com"
```

`inventory/production/group_vars/webservers.yml`:
```yaml
app_version: "2.4.1"
app_debug: false
db_host: "db-prod.internal"
api_base_url: "https://example.com"
```

El mismo rol [[Roles en Ansible]] (`deploy_app`) usa `{{ app_version }}`, `{{ db_host }}`... y cada entorno trae sus datos. **Cero ramas "paralelas" del código.**

## Secretos por entorno → Vault

Los secretos también se separan y cifran con [[Ansible Vault]]:

```text
inventory/production/group_vars/
├── webservers.yml          # normal (sin secretos)
└── webservers.vault.yml    # CIFRADO (database_password, api_key...)
```

Así un dev con acceso a básica config NO ve las credenciales de producción; el playbook de prod pide el vault password al desplegar.

```bash
ansible-playbook plays/deploy.yml -i inventory/production/hosts.yml --ask-vault-pass
```

## `ansible.cfg` también puede ser por entorno

Puedes tener configs distintas (p.ej. `forks`, strict) en cada entorno apuntando con `ANSIBLE_CONFIG`:

```bash
ANSIBLE_CONFIG=./ansible.cfg ansible-playbook ...
```

Detalle completo en [[Configuración de Ansible (ansible.cfg)]].

## Puntos clave

1. **Un solo código** de playbooks/roles; el entorno vive en el inventario + variables.
2. `-i` selecciona entorno; nunca playbooks "prod.yml" vs "staging.yml" duplicados.
3. `group_vars`/`host_vars` por carpeta de entorno = datos aislados.
4. Secretos separados y cifrados con Vault por entorno.
5. Mismo rol, datos distintos → configuración limpia y reproducible.

## Siguientes pasos

- La "plantilla" de carpetas → [[Estructura de un proyecto Ansible]]
- Secretos por entorno → [[Ansible Vault]]
- Roles instalables → [[Ansible Galaxy]]

# References