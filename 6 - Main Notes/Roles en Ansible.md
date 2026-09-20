2026-09-19 22:28

Status:

Tags: [[Ansible]]

# Roles en Ansible

## El problema que resuelven

Un playbook crece y crece: 300 tareas en un solo archivo es un caos que nadie puede mantener. Los **roles** empaquetan lógica, variables, plantillas, handlers y archivos en una **estructura de carpeta fija y reutilizable**. Son las "liberías" de Ansible.

Un rol es, en esencia, **un playbook independiente y reutilizable** con una convención de carpetas que Ansible lee automáticamente.

## Estructura canónica de un rol

```
roles/
└── nginx/
    ├── defaults/        # variables por defecto (baja prioridad, TODO rol debe tenerlas)
    │   └── main.yml
    ├── vars/            # variables del rol (dentro, no para romper; prioridad alta)
    │   └── main.yml
    ├── tasks/           # las tareas (el corazón)
    │   └── main.yml
    ├── handlers/        # handlers del rol
    │   └── main.yml
    ├── templates/       # archivos .j2 (template module)
    │   └── nginx.conf.j2
    ├── files/           # archivos binarios/planos (copy module)
    │   └── index.html
    ├── meta/            # dependencias, autor, versión, galaxy
    │   └── main.yml
    ├── tests/           # inventario + playbook de prueba
    │   ├── inventory
    │   └── test.yml
    └── README.md        # documentación
```

## Crear un rol

```bash
# Esqueleto completo
ansible-galaxy init nginx
```

## El contenido típico

### `tasks/main.yml` — el corazón

```yaml
---
- name: Instalar nginx
  apt:
    name: nginx
    state: present
  notify: Reiniciar nginx
  when: ansible_os_family == "Debian"

- name: Copiar config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Reiniciar nginx

- name: Copiar index
  copy:
    src: index.html
    dest: /var/www/html/index.html
```

### `defaults/main.yml` — la configuración que el usuario puede cambiar

```yaml
---
nginx_port: 80
nginx_worker_processes: 4
nginx_conf_path: /etc/nginx/nginx.conf
```

**Regla de oro en roles: pon TODO configurable en `defaults` y NO en `vars`.** Eso permite que cualquier play override las configure sin tocarte el rol.

### `handlers/main.yml`

```yaml
---
- name: Reiniciar nginx
  service:
    name: nginx
    state: restarted
```

### `meta/main.yml`

```yaml
---
galaxy_info:
  author: davo
  description: Instala y configura nginx
  license: MIT
  min_ansible_version: "2.9"
  platforms:
    - name: Ubuntu
      versions: [focal, jammy]
dependencies: []
```

## Usar un rol desde un playbook

```yaml
---
- name: Configurar servidores
  hosts: webservers
  become: true
  roles:
    - nginx                 # ruta roles/nginx
    - { role: mysql, vars: { version: "8.0" }, tags: [db] }
```

## Precedencia en roles (importante: defaults == flexibilidad)

Las variables se resuelven así (de menor a mayor prioridad):

1. `defaults/main.yml` del rol
2. Inventario
3. `vars` del play
4. `vars/main.yml` del rol
5. `vars_files`
6. `-e` (gana)

→ Por eso los roles venden "configuración mutable gratis": pon default bajo, deja que el usuario la puede pisotear.

## Roles vs playbook: cuándo

|Situación|Usa|
|---|---|
|1-2 tareas ad-hoc|ad-hoc / playbook plano|
|Tareas para un tema repetible|**Rol**|
|Distribuir a la comunidad / reutilizar|Rol + Galaxia (ver [[Ansible Galaxy]])|
|Orquestar varios roles en orden|Playbook con `roles:`|

## Dependencias entre roles (`meta/main.yml`)

```yaml
---
dependencies:
  - role: common        # corre primero
  - role: java
```

Ansible los ejecuta en orden antes de las tareas del rol.

## Buenas prácticas con roles

1. `ansible-galaxy init <rol>` para el esqueleto.
2. Todo variable → `defaults/`, secretos → Vault.
3. `include_role` (dinámico) vs `import_role` (estático) → ver control de ejecución.
4. Roles cortos y específicos ("nginx", "java", "deploy") > roles gigantes.
5. `tags` en roles para segmentar ([[Tags en Ansible]]).

## Puntos clave

- Rol = estructura de carpetas estándar + convención.
- `defaults` = configuración sobreescribible; `vars` = interna.
- Reutilizable en proyectos y con Galaxia.
- Permite playbooks de 5 líneas limpias.

## Siguientes pasos

- Consigue roles hechos → [[Ansible Galaxy]]
- Organízalo todo → [[Estructura de un proyecto Ansible]]
- Directorios de grupo → [[Inventarios en Ansible]]

# References