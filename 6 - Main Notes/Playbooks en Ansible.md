2026-09-19 21:27

Status:

Tags: [[Ansible]]

# Playbooks en Ansible

## ¿Qué es un playbook?

Un **playbook** es el corazón de Ansible: un archivo **YAML** que define jugadas (*plays*) contra tus servidores. Es "la orquesta" del controlador: describe quién ejecuta qué módulo, con qué variables, en qué orden y bajo qué condiciones.

Todos los comandos → [[Comandos ad-hoc en Ansible]] que viste, versionados y reutilizables, se escriben como playbook.

## Anatomía de un playbook

Un playbook puede contener **uno o varios plays** (`- name: ...`). Cada play es:

```yaml
---
- name: Configurar servidores web
  hosts: webservers          # a quién (patrón de hosts, a veces un inventario)
  become: true               # ¿necesito root?
  gather_facts: true         # ¿recojo facts del SO? (ver nota facts)

  vars:                      # variables para este play
    puerto_nginx: 8080

  tasks:                     # las tareas a ejecutar, EN ORDEN
    - name: Instalar nginx
      apt:
        name: nginx
        state: present

    - name: Habilitar en el arranque
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:                  # "si una tarea lo notifica, haz esto"
    - name: Reiniciar nginx
      service:
        name: nginx
        state: restarted
```

## Los campos más comunes de un play

|Campo|Qué hace|
|---|---|
|`hosts`|A qué grupo real: `webservers`, `all`, `localhost`... |
|`become`|Escalar privilegios (`sudo`) en las tareas|
|`become_user`|A qué usuario escalar (default `root`)|
|`gather_facts`|Recolectar datos del SO (`true` default)|
|`vars`|Variables del play|
|`vars_files`|Cargar variables desde uno o varios archivos YAML|
|`tasks`|La lista de tareas (módulo + argumentos)|
|`handlers`|Tareas que se disparan solo con un `notify`|
|`roles`|Roles a aplicar (ver [[Roles en Ansible]])|
|`pre_tasks` / `post_tasks`|Tareas antes/después de los roles|

## Tareas: la unidad atómica

Cada tarea llama a **un módulo**:

```yaml
tasks:
  - name: Instalar paquetes
    package:                       # módulo (multi-OS)
      name:
        - git
        - curl
      state: present

  - name: Copiar un archivo
    copy:
      src: index.html
      dest: /var/www/index.html
      owner: www-data
      mode: '0644'
```

Reglas de estilo:

- **Siempre pon `name:`** (si no, el log dirá `TASK: apt` y no entenderás nada).
- Los módulos son **idempotentes**: puedes ejecutar el playbook mil veces y solo cambia la primera.
- Si varios módulos dependen entre sí, anídelos **en orden** dentro de `tasks` (nada de "delegar al que vino después").

## Un primer playbook real completo

```yaml
---
- name: Desplegar un sitio estático con nginx
  hosts: webservers
  become: true
  vars:
    web_root: /var/www/mipagina
    site_name: ejemplo.local

  tasks:
    - name: Actualizar índice de paquetes
      apt:
        update_cache: yes

    - name: Instalar nginx y utilidades
      apt:
        name: [nginx, curl]
        state: present

    - name: Crear directorio de la web
      file:
        path: "{{ web_root }}"
        state: directory
        mode: '0755'
        owner: www-data
        group: www-data

    - name: Copiar la página
      copy:
        src: files/index.html
        dest: "{{ web_root }}/index.html"

    - name: Crear config del sitio
      template:
        src: site.conf.j2
        dest: "/etc/nginx/sites-available/{{ site_name }}"

    - name: Habilitar el sitio
      file:
        src: "/etc/nginx/sites-available/{{ site_name }}"
        dest: "/etc/nginx/sites-enabled/{{ site_name }}"
        state: link

    - name: Arrancar nginx
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:
    - name: Recargar nginx
      service:
        name: nginx
        state: reloaded
```

Cada pieza de aquí te la explican:

- La sintaxis YAML → [[YAML]]
- Los módulos → [[Módulos esenciales en Ansible]]
- Las variables → [[Variables y Hechos en Ansible]]
- Los `notify`/`handlers` → [[Handlers en Ansible]]
- La plantilla `site.conf.j2` → [[Templates y Jinja2 en Ansible]]

## Ejecutar y depurar un playbook

```bash
# Comprobar sintaxis sin tocar nada
ansible-playbook playbook.yml --syntax-check

# Simular (check mode): ¿qué CAMBIARÍA?
ansible-playbook playbook.yml --check

# Check + diff (muestra cambios de archivos)
ansible-playbook playbook.yml --check --diff

# Verbosidad para depurar (cuanto más -v, más)
ansible-playbook playbook.yml -v -vv -vvv

# Limitar a un servidor concreto del grupo
ansible-playbook playbook.yml --limit web1

# Solo ciertas tareas por su nombre (tags)
ansible-playbook playbook.yml --tags instalar
```

## Despliegue en varios "acts": múltiples plays, un solo archivo

Un playbook puede orquestar varias fases sobre distintos grupos:

```yaml
---
- name: Fase 1 – Bases de datos
  hosts: databases
  tasks:
    - name: Instalar PostgreSQL
      apt:
        name: postgresql
        state: present

- name: Fase 2 – Servidores web
  hosts: webservers
  tasks:
    - name: Instalar nginx
      apt:
        name: nginx
        state: present
```

Ansible los corre **en el orden del archivo**: primero DBs, después webs. Eso es **orquestación en un solo archivo**.

## Tabla: play vs playbook vs role

No confundas los niveles:

|Concepto|Contenido|Ejemplo|
|---|---|---|
|**Playbook**|1+ arquitectura de *plays* en un archivo YAML|`desplegar.yml`|
|**Play**|Un `- name` con `hosts + tasks + vars`|la parte de webservers|
|**Role**|Estructura reutilizable de play (ver [[Roles en Ansible]])|`rol_nginx`|

## Buenas prácticas

1. `name` en **todas** las tasks y plays → logs legibles.
2. Un playbook debería ser **leíble como un plan**: "instalar → configurar → habilitar".
3. Separa "juego grande" en **roles** cuando repitas.
4. Siempre `--syntax-check` antes de `--check`, y `--check` antes de ejecutar en producción.
5. Con limitaciones por entorno: ver [[Ansible en múltiples entornos]].

## Puntos clave

1. YAML + plays + tasks + handlers = el 80% de lo que escribirás.
2. Idempotente por diseño: repetir no rompe nada.
3. `--check --diff` te da un ensayo general sin riesgo.
4. Multi-play = orquestación de varios entornos en un archivo.

## Siguientes pasos

- Detalle de variables dinámicas → [[Variables y Hechos en Ansible]]
- Controlar qué módulo usar → [[Módulos esenciales en Ansible]]
- Plantillas Jinja2 → [[Templates y Jinja2 en Ansible]]
- Reutilizar con roles → [[Roles en Ansible]]

# References