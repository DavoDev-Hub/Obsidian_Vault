2026-09-19 22:20

Status:

Tags: [[Ansible]]

# Filtros Jinja2 en Ansible

## ¿Qué es un filtro?

Un **filtro** transforma un valor: cambia mayúsculas, junta listas, formatea JSON, pone un default. Se escribe con el pipe `|` dentro de una expresión `{{ }}`.

```jinja2
{{ variable | filtro }}
{{ variable | filtro1 | filtro2 }}   <!-- se encadenan: el 1º alimenta al 2º -->
```

Los filtros son la "navaja" para dar forma a variables dentro de templates y tareas.

## Los filtros del día a día

### Strings

```jinja2
{{ "hola" | upper }}            <!-- HOLA -->
{{ "HOLA" | lower }}            <!-- hola -->
{{ " hola " | trim }}           <!-- hola -->
{{ "hola mundo" | replace(" ", "_") }}  <!-- hola_mundo -->
{{ "abc" | length }}            <!-- 3 -->
```

### Defaults y mandatory

```jinja2
{{ puerto | default(80) }}                    <!-- si no existe, usa 80 -->
{{ dominio | default('example.com', true) }}  <!-- también si es vacío '' -->
{{ token | mandatory }}                       <!-- FALLA si no está definido -->
```

`default` + `true` respeta el caso de string vacío. `mandatory` = puerta de error para valores obligatorios.

### Listas

```jinja2
{{ lista | join(', ') }}          <!-- 1, 2, 3 -->
{{ lista | length }}              <!-- count -->
{{ lista | first }}               <!-- primer elemento -->
{{ lista | last }}                <!-- último -->
{{ lista | unique }}              <!-- duplicados fuera -->
{{ lista | sort }}                <!-- ordena -->
{{ lista | sort(reverse=true) }}  <!-- descendente -->
{{ lista | min }} / {{ lista | max }}
{{ lista | flatten }}             <!-- aplana listas anidadas -->
```

### Convertir tipos

```jinja2
{{ "123" | int }}                 <!-- entero 123 -->
{{ "3.5" | float }}               <!-- 3.5 -->
{{ "si" | bool }}                 <!-- true -->
{{ variable | string }}
{{ {"a": 1} | to_json }}          <!-- {"a": 1} -->
{{ dict | to_yaml }}              <!-- YAML -->
{{ dict | to_nice_json }}         <!-- JSON legible indentado -->
```

### Filtros de mapeo (muy profesionales)

```jinja2
{{ usuarios | map(attribute='nombre') | join(', ') }}   <!-- saca todos los .nombre -->
{{ paquetes | select('equalto', 'nginx') | list }}       <!-- filtra por valor -->
{{ hechos | map(attribute='version') | list }}
```

`map(attribute=...)` escribe oro cuando tienes listas de objetos.

### Otros útiles

```jinja2
{{ ruta | basename }}       <!-- /etc/nginx.conf -> nginx.conf -->
{{ ruta | dirname }}        <!-- /etc/nginx.conf -> /etc -->
{{ nombre | regex_replace('^(.*)\\.yml$', '\\1.j2') }}  <!-- regex -->
{{ cadena | b64encode }}    <!-- base64 -->
{{ numero | ternary('alto','bajo') }}   <!-- si numero verdadero → alto, si no bajo -->
```

## Filtros junto a `when` (condicionales)

```yaml
- name: Revisar que la versión sea mayor o igual
  debug:
    msg: "Versión OK"
  when: version | int >= 2024
```

```yaml
- name: Normalizar familia antes de decide
  debug:
    msg: "SO: {{ ansible_facts['os_family'] | lower }}"
```

## ¿Cómo encuentro un filtro cuando no lo recuerdo?

```bash
ansible-doc -t filter    # no estándar
```

Mejor: recuerda la **agrupación** — hay filtros de:

1. Strings → `upper, lower, replace, trim`
2. Listas → `join, first, last, sort, unique`
3. Números → `int, float, abs`
4. Tipos → `to_json, to_yaml`
5. Validación → `default, mandatory`
6. Colecciones → `map, select, flatten`

Si conoces el grupo, el filtro aparece en tu memoria (y mantenlo listo en la búsqueda de Obsidian).

## Combinar filtros en expresiones grandes

```jinja2
# Toma el primer nodo del grupo y cámbialo a mayúsculas
{{ (groups['db'] | first) | upper }}

# Lista de paquetes ordenada y sin duplicados, separada por coma
{{ paquetes | unique | sort | join(', ') }}
```

## Buenas prácticas

1. Encadena con límites: 1-2 filtros por expresión si puedes.
2. `default` en variables que puedan faltar (evita errores).
3. Usa `mandatory` en las que no pueden faltar (falla claro).
4. `to_nice_json` para depurar estructura de dicts en `debug`.
5. Filtros `map`/`select` solo cuando la lista es "realmente grande" o compleja.

## Puntos clave

- Filtro = transformación sobre un valor dentro de `{{ }}`.
- Se encadenan: `| filtro | filtro`.
- `default`, `join`, `map(attribute)`, `to_*` son los campeones.
- Amplían Jinja2 → [[Templates y Jinja2 en Ansible]].
- La sintaxis `{{ }}` es la misma de plantillas y `when`.

## Siguientes pasos

- Meta paquetes inclusivos → [[Templates y Jinja2 en Ansible]]
- Condicionales poderosas con filtros → [[Condicionales en Ansible]]
- Loops con `map` sobre resultados → [[Loops en Ansible]]

# References