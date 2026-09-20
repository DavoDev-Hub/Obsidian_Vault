2026-09-19 23:25

Status: Terminada

Tags: [[Ansible]]

# Tags en Ansible

## ¿Qué son los tags?

Los **tags** te permiten **ejecutar solo una parte** del playbook/rol. No reescriben nada: son **etiquetas de ejecución selectiva**. Sin tags, un play corre "todo"; con tags, filtras qué tareas corren: `ansible-playbook plays.yml --tags configuracion`.

## Por qué importan

En entornos reales NO quieres re-aplicar todo cada vez:

- Tuning manual → quieres solo las tareas de "instalación".
- Cambio de un servicio → tocar nada más que "nginx" y sus handlers.
- Info (debug) → correr solo tareas `info`.
- Rollout masivo → separar "preparar", "desplegar", "validar", "limpiar".

## Asignar tags a tareas

```yaml
- name: Instalar Nginx
  apt:
    name: nginx
    state: present
  tags: instalacion

- name: Configurar Nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  tags:
    - configuracion
    - nginx

- name: Notificar
  debug:
    msg: "Fin de instalación"
  tags: info
```

## Tags en plays, roles, includes e imports

```yaml
- hosts: webservers
  tags:
    - deploy
  roles:
    - role: nginx
      tags: [configuracion, nginx]
    - role: app
      tags: [deploy]
```

- Tag aplicado a nivel **play** → lo heredan todas las tareas.
- Tag en **rol** → solo ese rol (o parte vía `vars` en roles).
- `import_tasks` / `include_tasks`: los tags se **propagan** según dónde los pongas (ver [[Loops en Ansible]] y [[Módulos esenciales en Ansible]]).

## Filtrado en ejecución (el verdadero poder)

```bash
# Solo tareas etiquetadas 'configuracion'
ansible-playbook site.yml --tags configuracion

# Todo EXCEPTO las de 'backups'
ansible-playbook site.yml --skip-tags backups

# Etiquetas ayudan a orquestar fases (ver
# [[Opciones avanzadas de ejecución en Ansible]])
```

### `--list-tags` y `--list-tasks`

```bash
ansible-playbook site.yml --list-tags     # qué etiquetas existen
ansible-playbook site.yml --list-tasks     # qué tareas y con qué tags
```

Típico en CI/debug: confirmar que la etiqueta existe antes de lanzar.

## Tags "siempre" y "nunca"

```yaml
- name: Log obligatorio (corre siempre, incluso con --tags)
  debug:
    msg: "marca de tiempo única"
  tags: siempre      # 'always'
```

- `always` → corre aunque filtres por otro tag.
- `never` → solo corre si lo pides explícitamente (bueno para tareas destructivas o de limpieza).

## Buenas prácticas

1. **Vocabulario fijo** de tags por proyecto (`instalacion`, `configuracion`, `deploy`, `rollback`, `limpieza`, `info`).
2. Poner tags **mínimos, consistentes**, no uno por tarea o se vuelve ruido.
3. `--tags` y `--skip-tags` en CI/cron para **pipeline por fases**.
4. Tags en **handlers y roles** para segmentar grandes despliegues (ver [[Handlers en Ansible]]).
5. Documenta el vocabulario en el índice [[Estructura de un proyecto Ansible]].

## Resumen rápido

|Comando|Efecto|
|---|---|
|`--tags x`|solo tareas tag `x` + `always`|
|`--skip-tags x`|todo menos `x`|
|`--list-tags`|muestra etiquetas|
|`--list-tasks`|muestra tareas + tags|
|`tags: always`|nunca se filtra|
|`tags: never`|solo explícito|

## Siguientes pasos

- Combinar filtrado por tags con errores → [[Manejo de errores en Ansible]]
- Orquestar fases grandes → [[Opciones avanzadas de ejecución en Ansible]]
- Práctica dirigida → [[Ejercicios Practicar Ansible]]

# References