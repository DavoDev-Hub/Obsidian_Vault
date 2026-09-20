2026-09-19 20:25

Status:

Tags: [[Ansible]]

# Comandos ad-hoc en Ansible

## ¿Qué son?

Los **comandos ad-hoc** son órdenes sueltas que ejecutas contra uno o varios servidores sin escribir un playbook. Perfectos para:

- Pruebas rápidas.
- Tareas de un solo uso (reiniciar, comprobar, copiar un archivo).
- Explorar: ¿qué kernel corre en todos?

Cuando la tarea necesita repetirse, orden o variables, pásate a un [[Playbooks en Ansible]].

## Sintaxis

```bash
ansible <patrón-de-hosts> -i <inventario> -m <módulo> -a "<argumentos>" [opciones]
```

|Parte|Ejemplo|Qué es|
|---|---|---|
|`<patrón>`|`all`, `webservers`, `web*`|Qué hosts atacar (ver [[Inventarios en Ansible]])|
|`-i`|`-i inventory/hosts.yml`|Inventario (si no, usa el default)|
|`-m`|`-m ping`, `-m copy`|Módulo a ejecutar|
|`-a`|`-a "src=x dest=y"`|Argumentos del módulo|
|`-b`|`-b`|Escalar a root (become)|
|`-u`|`-u devpi`|Usuario SSH|
|`-K`|`-K`|Pedir contraseña sudo|
|`-C`|`-C`|Modo check (sin hacer cambios)|
|`-o`|`-o`|Salida comprimida en una línea|
|`-v / -vvv`|`-v`|Verbosidad (más `v` = más detalle)|

## Ejemplos de los más usados

### 1. Comprobar conectividad (`ping`)
```bash
ansible all -i hosts.yml -m ping
```
```
web1 | SUCCESS => { "changed": false, "ping": "pong" }
web2 | SUCCESS => { "changed": false, "ping": "pong" }
```

### 2. Ejecutar comandos (`command` es el módulo por defecto)
```bash
# Sin -m, usa "command"
ansible web -i hosts.yml -a "uptime"

# Si necesitas pipes/redirecciones o variables de bash, usa shell
ansible web -i hosts.yml -m shell -a "df -h | tail -5 && free -m"
```

### 3. Copiar un archivo de tu máquina al servidor (`copy`)
```bash
ansible db -i hosts.yml -b -m copy -a "src=./backup.sql dest=/var/tmp/backup.sql owner=root mode=0640"
```

### 4. Ver hechos / facts del sistema (`setup`)
```bash
# Solo el sistema operativo
ansible all -i hosts.yml -m setup -a "filter=ansible_os_family"

# Solo la distribución
ansible all -i hosts.yml -m setup -a "filter=ansible_distribution*"

# Sin filtro: es una metralleta de datos (IPs, RAM, discos, kernel...)
ansible all -i hosts.yml -m setup
```

### 5. Instalar paquetes (`apt` / `yum` / `dnf`)
```bash
# En Debian/Ubuntu
ansible web -i hosts.yml -b -m apt -a "name=nginx state=present update_cache=yes"
```
```bash
# En RHEL/Fedora
ansible web -i hosts.yml -b -m dnf -a "name=nginx state=present"
```

### 6. Gestionar servicios (`systemd` / `service`)
```bash
ansible web -i hosts.yml -b -m systemd -a "name=nginx state=restarted enabled=yes"
```

### 7. Crear usuarios (`user`)
```bash
ansible all -i hosts.yml -b -m user -a "name=deploy group=sudo append=yes create_home=yes"
```

### 8. Gestionar archivos (`file`)
```bash
# Crear un directorio
ansible web -i hosts.yml -b -m file -a "path=/var/www/curso state=directory mode=0755"

# Cambiar propietario
ansible web -i hosts.yml -b -m file -a "path=/var/tmp/backup.sql owner=root group=root mode=0640"
```

### 9. Descargar una URL (`get_url`)
```bash
ansible web -i hosts.yml -b -m get_url -a "url=https://repo/instalador.sh dest=/opt/instalador.sh mode=0755"
```

## Tabla de módulos típicos de ad-hoc

|Módulo|Uso clásico|
|---|---|
|`ping`|¿Conecta? ¿Python corre?|
|`command` / `shell`|Ejecutar comandos (shell = con pipes/redirects)|
|`setup`|Recoger facts del sistema|
|`copy`|Copiar archivos control → nodo|
|`file`|Crear/borrar archivos y carpetas, permisos|
|`get_url`|Descargar de internet al nodo|
|`apt` / `dnf` / `yum` / `package`|Instalar paquetes por distro (`package` = universal)|
|`service` / `systemd`|Arrancar/parar/reiniciar servicios|
|`user` / `group`|Crear/borrar usuarios y grupos|
|`reboot`|Reiniciar el servidor y esperar|

Repasa cada uno a fondo en [[Módulos esenciales en Ansible]].

## Truco: descubre los argumentos de cualquier módulo

```bash
ansible-doc apt
ansible-doc -l | head -50        # lista todos los módulos disponibles
ansible-doc -s copy              # solo la firma/resumen
```

`ansible-doc` es tu mejor amigo cuando usas un módulo nuevo.

## Modo check: adiós miedo a equivocarte

```bash
ansible web -i hosts.yml -b -m apt -a "name=nginx state=present" -C
```

Con `-C` Ansible **simula** la tarea y reporta si *cambiaría* algo, sin tocar el servidor. Úsalo siempre en tareas que modifiquen.

## Puntos clave

1. Ad-hoc = órdenes sueltas; playbook = recetas repetibles.
2. `-m` elige módulo y `-a` sus argumentos; sin `-m` usa `command`.
3. `-b` (become) es imprescindible para instalar/reiniciar.
4. Usa `-C` (check) antes de cosas que muevan el servidor.
5. `ansible-doc` te explica cualquier módulo sin salir de la terminal.

## Siguientes pasos

- Los bloques de construcción: módulos → [[Módulos esenciales en Ansible]]
- Lleva esto a recetas repetibles → [[Playbooks en Ansible]]
- Los hosts se seleccionan con patrones → [[Inventarios en Ansible]]

# References