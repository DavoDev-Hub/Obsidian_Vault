2026-09-19 21:32

Status:

Tags: [[Ansible]]

# Condicionales en Ansible

## ¿Para qué sirven?

Los playbooks corren contra N servidores; no todos necesitan lo mismo. Los **condicionales** con `when:` deciden, servidor a servidor y tarea a tarea, **si ejecutar** o no.

La clave: tienes datos del servidor (facts) y datos tuyos (variables). El condicional evalúa esas piezas en tiempo de ejecución.

## `when:` básico: la estrella

```yaml
- name: Instalar paquete solo en Debian
  apt:
    name: htop
    state: present
  when: ansible_facts['os_family'] == 'Debian'

- name: Instalar paquete solo en RedHat
  dnf:
    name: htop
    state: present
  when: ansible_facts['os_family'] == 'RedHat'
```

Es lo primero que verás en cualquier playbook "multi-distro".

## Comparadores disponibles

|Operador|Ejemplo|Significado|
|---|---|---|
|`==`|`when: a == 'x'`|igual|
|`!=`|`when: a != 'x'`|distinto|
|`>` `<` `>=` `<=`|`when: a > 5`|comparación numérica|
|`in`|`when: 'x' in lista`|contenido en lista|
|`not in`|`when: 'x' not in lista`|no contenido|
|`is defined`|`when: var is defined`|existe la variable|
|`is not defined`|`when: var is not defined`|no existe|
|`is truthy` / `is falsy`|`when: flag is truthy`|booleano "casi"|
|`is version(...)`|`when: v is version('22.04', '>=')`|comparar versiones|
|`is match(...)`|`when: nombre is match('web.*')`|regex|

### `and` / `or` / `not` — lógica combinada

```yaml
- name: Tarea que exige dos condiciones
  apt:
    name: nginx
    state: present
  when:
    - ansible_facts['os_family'] == 'Debian'
    - ansible_facts['distribution_version'] is version('22.04', '>=')
```

> En una **lista** de condiciones se evalúa "AND" (todas). Para "OR" úsalo en una sola línea: `when: a or b or c`. Se mezcla con `( )`: `when: (a and b) or c`.

## Ejemplos reales y muy típicos

### 1. Según el sistema operativo (branching)

```yaml
- name: Instalar usando el gestor correcto
  package:
    name: git
    state: present
  when: ansible_facts['distribution'] in ['Ubuntu', 'Debian', 'CentOS', 'Rocky']
```

### 2. Según `register` (el resultado de otra tarea)

```yaml
- name: Comprobar si el servicio está activo
  command: systemctl is-active nginx
  register: nginx_state

- name: Mensaje según el estado
  debug:
    msg: "nginx activo: {{ nginx_state.stdout }}"

- name: Reiniciar si NO está activo
  service:
    name: nginx
    state: restarted
  when: nginx_state.rc != 0
```

### 3. Según variable/variable dada por `-e`

```yaml
- name: Desplegar con balanceo si me lo piden
  include_role:
    name: haproxy
  when: balancear | default(false)
```

### 4. Según contenido de una lista/group

```yaml
- name: Solo hosts del grupo `prod`
  copy:
    src: prod.conf
    dest: /etc/app/
  when: "'prod' in group_names"
```

## Condicionales sobre facts "molestos": el caso de los booleans

En YAML `true`/`false` es booleano. Pero si viene de `register` puede ser string `"True"` / `"yes"` / `"1"`. Solución: `is truthy` o convertir con filtros:

```yaml
- name: Convertir salida a booleano y decidir
  command: "echo yes"        # muchos outputs vienen como string
  changed_when: false
  register: flag

- name: Ahora sí, condiciono al bool real
  debug:
    msg: "Cumplido"
  when: flag.stdout | bool
```

## `when` + loop: condiciones por elemento

```yaml
- name: Instalar paquetes permitidos
  apt:
    name: "{{ item }}"
    state: present
  loop: "{{ todos_los_paquetes }}"
  when: item in paquetes_excluidos | default([])
```

`item` está disponible dentro del `when` en un loop.

## `when` en handlers y roles

- También puedes poner `when` en handler y roles → se respeta al dispararse.
- En roles, `when` evalúa con las variables del rol.

## `assert` (pipe-validador de precondiciones)

```yaml
- name: Verificar precondiciones antes de desplegar
  assert:
    that:
      - ansible_facts['os_family'] == 'Debian'
      - app_user is defined
      - libreria is defined
    fail_msg: "Falta pré-requisito. Revisa variables."
```

Si alguna `that:` es falsa → la tarea falla con tu `fail_msg`. Perfecto para "puerta de entrada" de playbooks.

## Puntos clave

1. `when:` es la forma de decidir; siempre devuelve bool.
2. `facts` son tus datos del servidor; combine facts + `{{ }}` para puertas complejas.
3. Lista de condiciones = AND; `or` en línea.
4. `is version`, `is match`, `is defined` son tus filtros lógicos.
5. `assert` para validar antes de actuar.
6. Siempre usa comillas y `| default(...)` para no romper por variable ausente.

## Siguientes pasos

- Combinar con datos dinámicos → [[Variables y Hechos en Ansible]]
- Repetir + decidir → [[Loops en Ansible]]
- Validar pre-flight → [[Validación y linting en Ansible]]

# References