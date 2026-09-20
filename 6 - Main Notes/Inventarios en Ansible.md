2026-09-19 20:10

Status:

Tags: [[Ansible]]

# Inventarios en Ansible

## ¿Qué es el inventario?

El **inventario** es la lista de los servidores que Ansible puede administrar, junto con sus grupos, variables y credenciales de conexión. Es la "fuente de verdad" de qué existe en tu infraestructura.

Por defecto Ansible busca `/etc/ansible/hosts`, pero en proyectos reales siempre se pasa uno propio con `-i`.

## Formato INI (clásico)

```ini
[webservers]
web1.example.com ansible_user=devpi
web2.example.com
web3.example.com ansible_host=192.168.1.30

[databases]
db1 ansible_host=db.internal.lan ansible_port=2222

[all:vars]
ansible_user=admin
ansible_python_interpreter=/usr/bin/python3

[webservers:vars]
http_port=8080
```

Reglas del INI:
- `[grupo]` abre un grupo.
- `[all:vars]` define variables para todos los hosts.
- `[webservers:vars]` define variables para un grupo.
- `[grupo:children]` permite agrupar grupos.
- Las variables se ponen como `clave=valor` junto al host.

## Formato YAML (preferido)

Cada inventario YAML es un diccionario con `all → hosts / children / vars`:

```yaml
all:
  hosts:
    mail.example.com:
  children:
    webservers:
      hosts:
        web1.example.com:
          http_port: 8080
        web2.example.com:
      vars:
        nginx_version: "1.24.0"
    databases:
      hosts:
        db1:
          ansible_host: 10.10.10.5
          ansible_port: 2222
    prod:
      children:
        webservers:
        databases:
      vars:
        environment: production
  vars:
    ansible_user: admin
    ansible_python_interpreter: /usr/bin/python3
```

Puntos clave del YAML:
- `children` anida grupos (grupo de grupos).
- `vars` puede ir a nivel host, grupo, o del `all`.
- Todo lo que se ve, se puede leer sin ambigüedad: es YAML puro.

## Group vars y host vars: separar inventario de variables

Aunque puedes meter variables dentro del inventario, la práctica profesional es separarlas en carpetas:

```text
inventory/
├── production/
│   ├── hosts.yml          ← solo inventario (hosts y grupos)
│   ├── group_vars/
│   │   ├── webservers.yml ← variables de <webservers>
│   │   └── all.yml        ← variables para todos
│   └── host_vars/
│       └── web1.yml       ← variables solo de <web1>
└── staging/
    └── ...
```

- `group_vars/<grupo>.yml` aplica a todos los hosts de ese grupo.
- `host_vars/<host>.yml` aplica a un host concreto.
- Esta separación es la base de la nota [[Estructura de un proyecto Ansible]].

## Variables de conexión más usadas en el inventario

|Variable|Qué define|
|---|---|
|`ansible_host`|IP o hostname real (cuando el nombre del inventario es un alias)|
|`ansible_port`|Puerto SSH (por defecto 22)|
|`ansible_user`|Usuario SSH|
|`ansible_ssh_private_key_file`|Ruta de la llave privada|
|`ansible_connection`|Plugin de conexión (`ssh`, `winrm`, `local`, `network_cli`...)|
|`ansible_python_interpreter`|Ruta del Python en el nodo|
|`ansible_become` / `ansible_become_method`|Escalado a root|
|`ansible_password`|Contraseña (secreto: usa [[Ansible Vault]])|

## Hosts patterns: cómo seleccionar servidores

Los patrones se usan tanto en ad-hoc como en playbooks (`hosts:`).

|Patrón|Qué selecciona|
|---|---|
|`all`|Todos los hosts|
|`webservers`|Todos los del grupo `webservers`|
|`web*.example.com`|Wildcards por nombre|
|`webservers:databases`|Unión: webservers **+** databases|
|`webservers:!web3`|Excluye `web3` (usa `!`)|
|`webservers:&staging`|Intersección: solo los que están en ambos|
|`~(web|db)\d+\.lan`|Expresión regular (prefijo `~`)|
|`db[01:50]`|Rangos numéricos → db01...db50|
|`web[a:f]`|Rangos de letras|
|`localhost`|El propio control node|

### Ejemplo sobre la unión con excepción
```text
# Todos los webservers excepto web3
ansible 'webservers:!web3' -m ping
```

## Comandos útiles para explorar el inventario

```bash
# Muestra todo el inventario expandido (variables incluidas)
ansible-inventory -i inventory/production/hosts.yml --list

# Vista resumida de hosts y grupos
ansible-inventory -i inventory/production/hosts.yml --graph

# Qué hosts caen bajo un patrón
ansible -i inventory/production/hosts.yml 'webservers:&prod' --list-hosts
```

## Inventario dinámico (resumen)

Cuando tus servidores se crean/destruyen en la nube, un inventario estático no sirve. Los **inventory plugins** consultan al proveedor en caliente:

```bash
# AWS
ansible-inventory -i aws_ec2.yml --list
```

El archivo `aws_ec2.yml` activa el plugin y define filtros (regiones, tags). Todo esto se cubre en [[Ansible en múltiples entornos]].

## Puntos clave

1. El inventario agrupa servidores; el YAML es el formato recomendado.
2. `group_vars/` y `host_vars/` separan datos de topología.
3. Conocer los **host patterns** te ahorra tiempo: son el "SQL" de Ansible.
4. Verifica siempre con `ansible-inventory --list` o `-i ... --list-hosts` antes de actuar sobre muchos servidores.
5. Las contraseñas de inventario van cifradas con Vault.

## Siguientes pasos

- Ejecuta órdenes sueltas contra el inventario → [[Comandos ad-hoc en Ansible]]
- Usa grupos y variables dentro de playbooks → [[Variables y Hechos en Ansible]]
- Define los patrones en un playbook → [[Playbooks en Ansible]]

# References