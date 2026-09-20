2026-09-19 22:05

Status:

Tags: [[Ansible]]

# Loops en Ansible

## Por qué loops: el anti-Ctrl-C-Ctrl-V

Casi toda tarea real se repite: instalar 5 paquetes, crear 10 usuarios, copiar 3 archivos. Escribir tarea a tarea es insostenible. Los **loops** lo resuelven: escribes una tarea y ella gira sobre tu lista.

Regla mental: **si dos bloques de YAML son idénticos salvo un valor → conviértelo en loop.**

## El bucle estándar: `loop`

```yaml
- name: Instalar paquetes
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - curl
    - htop
    - unzip
```

Cada vuelta coloca el elemento actual en **`item`**.

## Loop sobre estructuras (listas de diccionarios)

```yaml
- name: Crear usuarios con atributos
  user:
    name: "{{ item.nombre }}"
    uid: "{{ item.uid | default(1000) }}"
    group: "{{ item.grupo }}"
    shell: "{{ item.shell }}"
  loop:
    - { nombre: alice, uid: 2001, grupo: dev,   shell: /bin/bash }
    - { nombre: bob,   uid: 2002, grupo: dev,   shell: /bin/zsh }
    - { nombre: carol, uid: 2003, grupo: ops,   shell: /bin/bash }
```

## Loop sobre diccionario (`with_dict`)

```yaml
- name: Crear archivos con contenido distinto
  copy:
    content: "{{ item.value }}"
    dest: "/etc/app/{{ item.key }}.conf"
  with_dict:
    db_host: "localhost"
    db_port: "5432"
```

Cada vuelta: `item.key` y `item.value`.

## `with_items` vs `loop` + `with_list`

- `with_items` aplana listas anidadas de un nivel.
- `loop` es el recomendado moderno (no aplana: para sublistas necesitas `flatten`).
- Usualmente verde: prefiere `loop` con `| flatten` y `loop_control`.

## `loop_control`: tomar el control

```yaml
- name: Hacer fila con etiqueta útil
  debug:
    msg: "Procesando {{ item.nombre }}"
  loop: "{{ aplicaciones }}"
  loop_control:
    label: "{{ item.nombre }}"      # qué mostrar en el log (no spameo del JSON completo)
    index_var: contador             # índice actual
    pause: 5                        # segundos entre iteraciones
```

## `with_sequence` (rangos)

```yaml
- name: Crear 10 archivos
  file:
    path: "/tmp/archivo{{ item }}.log"
    state: touch
  with_sequence: count=10
```

Útil para tests, stress, buckets numerados.

## Loops con `index_var` y `first`

```yaml
- name: Imprimir primer elemento
  debug:
    msg: "Primer paquete: {{ paquetes | first }}"
```

## Loop + registro de resultados

```yaml
- name: Comprobar varios endpoints
  uri:
    url: "{{ item }}"
  loop:
    - https://web1.example.com
    - https://web2.example.com
  register: resultados

- name: Resumen
  debug:
    msg: "{{ item.url }} -> {{ item.status }}"
  loop: "{{ resultados.results }}"
```

El resultado de una tarea con loop tiene `results` (lista), y cada uno tiene `.item`.

## Combinar loop + when

```yaml
- name: Instalar solo paquetes permitidos
  apt:
    name: "{{ item }}"
    state: present
  loop: "{{ todos }}"
  when: item in permitidos
```

## Errores frecuentes y cómo evitarlos

1. **Olvidar `item`** al referenciar el elemento.
2. `item.error` (tipo) → revisar que el diccionario tenga la clave usando `default`.
3. Mezclar `with_items` y `loop` de forma confusa — elige **uno solo**.
4. Loop con resultado: no acceder a `results.results[0].item` sin verificar — usa filtros `map`.
5. `pause` solo en `loop_control`, no es un parámetro del módulo.

## Puntos clave

- `loop` = estándar moderno; `with_*` = legado (aún funciona).
- Cada iteración trabaja con `item` (o `item.clave`).
- Estructuras YAML → 90% de tus datos de loop.
- `loop_control` para etiquetar, indexar y espaciar.
- Se combina brutal con `register` + `when` (ver [[Variables y Hechos en Ansible]] y [[Condicionales en Ansible]]).

## Siguientes pasos

- Templates con dinámica → [[Templates y Jinja2 en Ansible]]
- Loops dentro de roles → [[Roles en Ansible]]

# References