2026-09-19 22:12

Status:

Tags: [[Ansible]]

# Templates y Jinja2 en Ansible

## ¿Qué es un template?

Un **template** es un archivo de configuración **con huecos** que Ansible rellena con variables antes de copiarlo al servidor. Es la diferencia entre tener configs "semi-manuales" y configs generadas dinámicamente.

El 90% del tiempo se usa con el módulo `template` (basado en **Jinja2**).

## El módulo `template`

```yaml
- name: Generar config de nginx desde una plantilla
  template:
    src: nginx.conf.j2        # el hueco (plantilla Jinja2)
    dest: /etc/nginx/nginx.conf
    owner: root
    group: root
    mode: '0644'
  notify: Reiniciar nginx
```

El archivo de plantilla debe existir en `templates/` (junto al playbook o al rol).

## Anatomía de una plantilla Jinja2 (.j2)

```jinja2
# nginx.conf.j2
server {
    listen {{ puerto }};
    server_name {{ dominio }};
    root {{ ruta_raiz }};

    {% if usar_ssl %}
    listen 443 ssl;
    ssl_certificate {{ ruta_cert }};
    ssl_certificate_key {{ ruta_key }};
    {% endif %}

    location / {
        {% for ip in allow_list %}
        allow {{ ip }};
        {% endfor %}
        deny all;
    }
}
```

Dos tipos de "huecos":

|Sintaxis|Qué hace|
|---|---|
|`{{ variable }}`|**Expresión**: imprime el valor|
|`{% %}`|**Declaración**: lógica (if, for, set)|
|`{# comentario #}`|Comentario (no se imprime)|

## Súper útil: bucles dentro de la plantilla

```jinja2
# app.conf.j2
{% for usuario in usuarios %}
# Usuario: {{ usuario.name }}
{{ usuario.name }}:{{ usuario.uid }}:{{ usuario.grupo }}
{% endfor %}
```

Con datos:

```yaml
vars:
  usuarios:
    - { name: alice, uid: 2001, grupo: dev }
    - { name: bob,   uid: 2002, grupo: dev }
```

Resultado → 2 líneas generadas.

## Condicionales dentro de la plantilla

```jinja2
{% if ambiente == "produccion" %}
worker_processes auto;
{% else %}
worker_processes 2;
{% endif %}
```

También `{% elif %}` y `{% else %}`.

## Filtros dentro de plantillas (mini-resumen)

Esto se amplía en [[Filtros Jinja2 en Ansible]]:

```jinja2
{{ cadena | upper }}                   <!-- a MAYÚSCULAS -->
{{ cadena | default('sin definir') }}  <!-- con fallback -->
{{ lista | join(', ') }}               <!-- convierte a cadena -->
{{ numero | int + 1 }}                 <!-- suma -->
{{ dict | to_json }}                   <!-- a JSON -->
```

## Templates con `lookup` (parámetros dinámicos)

Puedes mezclar variables del "mundo real" dentro de la plantilla:

```jinja2
{{ lookup('env', 'MY_TOKEN') }}
```

(Con precaución: secretos van con Vault, no en env).

## Modo trimm y whitespace

Por defecto Jinja2 quita el whitespace que rodea a `{% %}`. Si necesitas control fino, en Ansible: `#` al final de la etiqueta recorta, `+` conserva:

```jinja2
{% for x in lista -%}   <!-- recorta el final -->
x
{%- endfor %}
```

No te obsesiones: primero genera, luego afina el espacio.

## Verificar antes de romper

- Render local: `ansible -m template` no es directo; mejor usa `debug` con `lookup('template', ...)`.
- En host: `--check --diff` te muestra qué cambiaría.
- Aprende jugando: `ansible localhost -m debug -a "msg={{ 'hola' | upper }}"`.

## Buenas prácticas

1. Nombre de plantilla con extensión `.j2` (convención, no obligatorio).
2. Guarda plantillas en `templates/` (de playbook o rol).
3. Usa `include_tasks`/`import_tasks` para plantillas de varias piezas.
4. `ansible_managed` (comentario estándar en la plantilla) ayuda a saber que el archivo es gestionado.
5. No `sudo` de más: renderiza con owner/mode correctos.

## Puntos clave

- Template = Jinja2 + variables de Ansible + filtros.
- `{{ }}` imprime, `{% %}` controla, `{# #}` comenta.
- El módulo `template` copia y renderiza (a diferencia de `copy` que copia plano). Ver [[Módulos esenciales en Ansible]].
- Es LA herramienta para configs que cambian por host/entorno.

## Siguientes pasos

- Los trucos de transformación → [[Filtros Jinja2 en Ansible]]
- Variables que alimentan las plantillas → [[Variables y Hechos en Ansible]]
- Plantillas reutilizables en roles → [[Roles en Ansible]]
- Loop para generar muchas → [[Loops en Ansible]]

# References