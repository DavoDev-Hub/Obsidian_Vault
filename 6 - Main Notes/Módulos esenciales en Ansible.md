2026-09-19 21:10

Status:

Tags: [[Ansible]]

# Módulos esenciales en Ansible

## La biblioteca que te da "superpoderes"

Un **módulo** es la unidad de trabajo de Ansible: una mini-aplicación (escrita en Python, o en PowerShell para Windows) que el control node envía al servidor gestionado y que resuelve **una única tarea**. Los módulos son el 90% de lo que escribes; los playbooks solo ordenan módulos.

Los nombres se escriben `coleccion.nombre` (FQCN). Los de uso diario están en la colección `ansible.builtin`.

## Tabla maestra: los módulos que usarás siempre

|Módulo|Qué hace|Notas|
|---|---|---|
|`package`|Instala/actualiza paquetes sin saber el gestor|Elige apt/yum/dnf solo según el SO. Ideal para play multi-OS|
|`apt`|Gestión de paquetes Debian/Ubuntu|ver nota a fondo|
|`dnf` / `yum`|Gestión de paquetes RHEL/Fedora/CentOS||
|`pip`|Instala paquetes de Python|gestiona venv con pista de *virtualenv*|
|`copy`|Copia archivo **local → remoto**|Determina el "hash" e idempotente|
|`fetch`|Trae archivo **remoto → local**|lo contrario de copy|
|`template`|Renderiza plantilla Jinja2 → remoto|ver [[Templates y Jinja2 en Ansible]]|
|`file`|Crea/borra archivos, dirs, symlinks, permisos, owner|independiente del SO|
|`lineinfile`|Asegura que una **línea** exista/no exista en un archivo|Para config; cambia solo la línea|
|`blockinfile`|Asegura un **bloque marcado** `# BEGIN ANSIBLE MANAGED` en config|para secciones de confi|
|`replace`|Regex sobre un archivo completo|peligro: reemplaza todo match|
|`get_url`|Descarga una URL al host|https, checksum, force|
|`unarchive`|Descomprime tar/gzip/zip en el host|remote_src copia local primero o directo|
|`archive`|Comprime archivos/dirs en el host||
|`command`|Ejecuta un comando (sin shell)|default, no interpreta `>` `\|` `$`|
|`shell`|Ejecuta con `/bin/sh`|Usa si necesitas pipes/redirects|
|`raw`|Ejecuta **sin** Python (mínimos)|Bootstrap: instalar python primero|
|`script`|Ejecuta un **script local** en el remoto|no hay que instalar nada|
|`user` / `group`|Gestiona usuarios y grupos|create, ssh keys|
|`service`|Servicios (compatibilidad SysV)|mejor usar systemd si existe|
|`systemd`|Servicios systemd modernos|enabled, state, daemon_reload|
|`apt_repository` / `yum_repository`|Añade repos de paquetes|ppa, url, state|
|`cron`|Crea tareas programadas (cron)||
|`getent`|Consulta *nss databases* (grupos, passwd...)||
|`mount`|Gestiona puntos de montaje fstab||
|`firewalld`|Reglas de firewall (firewalld)|community.general|
|`ufw`|Firewall UFW Debian/Ubuntu|community.general|
|`selinux`|Configuración de SELinux||
|`ping`|Prueba de conexión (devuelve pong)||
|`setup`|Recoge facts del servidor||
|`debug`|Muestra mensaje/variable para depurar|msg, var|
|`assert`|Valida condiciones booleanas (fail si falsas)||
|`fail`|Falla el play a propósito; valida precondiciones||
|`wait_for`|Espera a que un puerto/archivo/patrón coexista||
|`set_fact`|Define variables en tiempo de ejecución||
|`uri`|Cliente HTTP (GET/POST/...) en el nodo||
|`add_host`|Añade hosts en ejecución (inventario dinámico)||
|`group_by`|Agrupa hosts por facts en ejecución|ej. por OS family|

## Los grupos de uso por escenario

### Instalación de software (depende del SO)
```yaml
- name: Instalar nginx (Debian/Ubuntu)
  apt:
    name: nginx
    state: present        # present | latest | absent

- name: Instalar nginx (Rocky/Fedora)
  dnf:
    name: nginx
    state: present
```
**Multi-OS general**: usa `package` y deja que Ansible resuelva el gestor según la familia del SO (`ansible_os_family`):
```yaml
- name: Instalar nginx en cualquier SO
  package:
    name: nginx
    state: present
```
En [[Variables y Hechos en Ansible]] ves cómo ramificar condicionalmente (`when: ansible_os_family == 'Debian'`).

### Archivos y configuración
```yaml
- name: Copiar configuración del sitio
  copy:
    src: nginx/conf/sitio.conf
    dest: /etc/nginx/conf.d/sitio.conf
    owner: root
    group: root
    mode: '0644'

- name: Asegurar línea de puerto en el archivo de config
  lineinfile:
    path: /etc/nginx/nginx.conf
    regexp: '^listen '
    line: '    listen 8080;'

- name: Bloque marcado gestionado por Ansible
  blockinfile:
    path: /etc/nginx/nginx.conf
    marker: "# {mark} MANEJADO POR ANSIBLE ZONE"
    block: |
      server {
        listen 9090;
      }
    insertafter: '^http {'
```

### Servicios (arrancar/parar/reiniciar)
```yaml
- name: Habilitar y arrancar nginx
  systemd:
    name: nginx
    state: started        # started | stopped | restarted | reloaded
    enabled: true
```
> diff entre `state=restarted` y `state=reloaded`: restarted mata y levanta; reloaded aplica nueva conf sin cortar servicio ([handlers] hace esto).

### Usuarios y grupos
```yaml
- name: Crear usuario de despliegue
  user:
    name: deploy
    groups: www-data
    append: true
    shell: /bin/bash
    create_home: true

- name: Añadir su llave pública
  authorized_key:
    user: deploy
    state: present
    key: "{{ lookup('file', 'llaves/deploy.pub') }}"
```

### Descargas y descomprimir
```yaml
- name: Descargar JDK
  get_url:
    url: "https://releases.example.com/jdk.tar.gz"
    dest: /opt
    mode: '0755'
    checksum: sha256:abc123...

- name: Descomprimir
  unarchive:
    src: /opt/jdk.tar.gz
    dest: /opt/jdk
    remote_src: true
    creates: /opt/jdk/bin/java
```

### Chequear y esperar
```yaml
- name: Esperar a que el servicio esté en el puerto 8080
  wait_for:
    port: 8080
    host: 127.0.0.1
    delay: 3
    timeout: 120

- name: Comprobar que hay variables y no fallar
  assert:
    that:
      - app_user is defined
      - app_dir is defined
```

## Cómo aprender un módulo sin adivinar

```bash
ansible-doc copy         # documentación completa (didactic)
ansible-doc -s copy      # solo la firma en una línea
ansible-doc -l | grep -i http   # buscar módulos por palabra
```

Todo lo que ves aquí (options, examples) sale de `ansible-doc`.

## Puntos clave

1. Cada módulo es **una tarea**: idempotente y devuelve JSON.
2. `state=present/absent/started/stopped` es el "input" por defecto más usado.
3. La familia de SO decide el gestor de paquetes (`package` te lo simplifica).
4. `notify` + `handlers` con `service restarted` = la configuración "que se aplica sola".
5. `ansible-doc` es tu segunda memoria.

## Siguientes pasos

- Ordenar esos módulos: no memorices, **orquestalos** → [[Playbooks en Ansible]] / [[Roles en Ansible]]
- Parametrizarlos: valores variables → [[Variables y Hechos en Ansible]]

# References