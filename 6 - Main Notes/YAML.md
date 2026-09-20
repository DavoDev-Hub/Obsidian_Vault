2026-09-19 20:40

Status:

Tags: [[Ansible]]

# YAML

## ¿Qué es YAML?

YAML (**Y**AML **A**in't **M**arkup **L**anguage) es el formato en el que escribes todo en Ansible: playbooks, inventarios, variables. Es un formato de **datos legible por humanos** que usa la indentación en vez de llaves o paréntesis como JSON.

Si sabes leer y escribir YAML, ya sabes escribir en "idioma Ansible".

## Las 3 reglas sagradas

1. **La indentación es significado** — usa **2 espacios** por nivel (nunca tabs). Ansible lanzará un error si mezclas tabs.
2. **`clave: valor`** — los mapas (diccionarios).
3. **`- elemento`** — las listas (arrays).

## El chip "mapa vs lista"

Piensa en JSON mientras lees YAML:

```yaml
# Esto es un OBJETO / diccionario (clave → valor)
persona:
  nombre: Davo
  edad: 21
```

```json
{ "persona": { "nombre": "Davo", "edad": 21 } }
```

```yaml
# Esto es una LISTA (varios elementos)
frutas:
  - manzana
  - plátano
  - pera
```

```json
{ "frutas": ["manzana", "plátano", "pera"] }
```

## Tipos de datos en YAML

```yaml
# Strings (texto)
nombre: Davo
apellido: "Ramírez Basurto"   # comillas si hay caracteres especiales

# Números
edad: 21
precio: 12.50

# Booleanos (true/false, yes/no, on/off)
activo: true
es_admin: no

# Fechas
fecha: 2026-09-19

# Lista de strings
servicios:
  - nginx
  - mysql

# Diccionario anidado (la estructura de siempre en Ansible)
servidor:
  ip: 10.0.0.5
  puerto: 22
  usuarios:
    - name: devpi
      grupo: sudo
    - name: guest
      grupo: users
```

### String de varias líneas

```yaml
# Bloque literal: conserva saltos de línea |
script: |
  echo "Hola"
  echo "Mundo"

# Bloque plegado: une líneas con espacio >
descripcion: >
  Esto es un texto largo
  que se convierte
  en una sola línea.
```

### Anidación profunda: el ejemplo "real de Ansible"

```yaml
---
- name: Configurar servidores web
  hosts: webservers
  become: true
  tasks:
    - name: Instalar nginx
      apt:
        name: nginx
        state: present
    - name: Crear carpeta
      file:
        path: /var/www/web
        state: directory
```

Observa cómo cada nivel añade **2 espacios**: `tasks` → `- name` → `apt` → `name`. Eso es todo.

## Cosas que te van a morder

### 1. Valores que parecen números o booleanos
```yaml
version: 1.24.0        # YAML lo ve como string (dos puntos) — ok
puerto: 8080           # número (int)
branch: 1.4            # ojo: esto es un FLOAT, no "1.4"
correo: user@gmail.com # hay un ":" así que necesita comillas
timeout: 3.0           # float
```

Si no quieres que YAML lo convierta, usa comillas:
```yaml
branch: "1.4"
timeout: "3.0"
```

### 2. El `:` dentro de un valor
```yaml
msg: ERROR: fallo    # el ":" complica → pon comillas
msg: "ERROR: fallo"
```

### 3. Comillas y el nº de versión especial
Ansible 2.x **no convierte** `yes`, `no`, `on`, `off` a booleanos (se tratan como strings desde 2.0), pero por claridad usa `true`/`false`.

### 4. Caracteres especiales al inicio
- `- ` al inicio indica lista.
- `*` y `&` son "anclas" (reutilización avanzada):

```yaml
defaults: &dflt
  puerto: 80
  estado: present

web1: *dflt      # hereda los valores de dflt
```

### 5. El `---` al inicio
Indica el inicio del documento YAML. Ansible no lo exige en playbooks pero es convención y buena práctica.

## YAML que verás en inventarios (recuerda la nota)

```yaml
all:
  children:
    webservers:
      hosts:
        web1.example.com:
```
Eso es 100% YAML puro: un diccionario anidado. Detalle en [[Inventarios en Ansible]].

## De JSON a YAML y viceversa

Muchas respuestas de módulos y APIs vienen en JSON. Saber traducir mentalmente te salvará:

|JSON|YAML|
|---|---|
|`{ }`|indentación + `clave: valor`|
|`[ a, b ]`|`- a` líneas|
|`"string"`|string plano (o con comillas)|
|`true/false`|`true/false`|

Ansible tiene filtros para conversión: `to_json` y `to_yaml` (ver [[Filtros Jinja2 en Ansible]]).

## Validador: comprueba tu YAML sin ejecutar nada

```bash
ansible-playbook playbook.yml --syntax-check
```

Si el YAML está mal, Ansible te dice archivo y línea. Para YAML suelto (variables, inventario), hay validadores online o `python3 -c "import yaml; yaml.safe_load(open('x.yml'))"`.

## Puntos clave

1. Indentación de **2 espacios**, nada de tabs.
2. Mapas (`clave: valor`) y listas (`- elemento`) se combinan en todo.
3. Las comillas protegen valores con `:` o que parecen número.
4. `---` al inicio del playbook (convención).
5. `ansible-playbook --syntax-check` es tu validador gratuito.

## Siguientes pasos

- Ponle YAML a un playbook → [[Playbooks en Ansible]]
- Definir variables en YAML → [[Variables y Hechos en Ansible]]
- Ver YAML en inventarios → [[Inventarios en Ansible]]

# References