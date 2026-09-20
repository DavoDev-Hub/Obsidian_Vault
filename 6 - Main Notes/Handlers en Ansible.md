2026-09-19 21:52

Status:

Tags: [[Ansible]]

# Handlers en Ansible - Acciones que solo se ejecutan "si algo cambió"

## El problema que resuelven

Imagina que tu tarea "copiar config de nginx" se ejecutó y el archivo **cambió** (por ejemplo, cambió el puerto). El servicio debe **reiniciarse** para aplicar el cambio... pero no quieres reiniciar nginx cada vez que corres el playbook si nada cambió (sería no idempotente y rompería conexiones).

**Handlers** = "una acción que solo se dispara si una tarea que la **notifica** terminó con `changed`". Reinicias solo cuando hace falta.

## Anatomía

```yaml
handlers:
  - name: Reiniciar nginx
    service:
      name: nginx
      state: restarted
```

En la tarea la *notificas*:

```yaml
tasks:
  - name: Copiar config
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: Reiniciar nginx
```

Flujo real:

```text
tarea "Copiar config" 
   │ resultado: changed  →  notifica "Reiniciar nginx"
   │ resultado: ok       →  NO notifica (no hay cambio)
   ▼
fin del play  →  corre el handler "Reiniciar nginx" una sola vez
```

## Reglas de oro

1. El handler corre **al final del play**, aunque lo notificaste en la tarea 1.
2. Aunque 3 tareas notifiquen el mismo handler, **corre 1 sola vez**.
3. Solo se dispara si el `notify` viene de una tarea con `changed`.
4. `name` del handler debe coincidir **exactamente** con el `notify`.

## Múltiples tareas → un handler

```yaml
tasks:
  - name: Config 1
    template: ...
    notify: Recargar servicios
  - name: Config 2
    copy: ...
    notify: Recargar servicios

handlers:
  - name: Recargar servicios
    service:
      name: nginx
      state: reloaded     # reload no corta conexiones
```

## Un handler con loop (`listen`)

Con `listen` un handler responde a varios "temas" sin enumerarlos:

```yaml
handlers:
  - name: Reiniciar pila
    listen: "reiniciar-pila"
    service:
      name: "{{ item }}"
      state: restarted
    loop:
      - nginx
      - gunicorn

tasks:
  - name: Cambiar app
    template: ...
    notify: "reiniciar-pila"     # no hace falta saber el nombre exacto
```

## `flush_handlers`: forzar la ejecución YA

Por defecto los handlers esperan al final. Con `meta: flush_handlers` los ejecutas en el momento:

```yaml
tasks:
  - name: Cambiar config de DB
    template: ...
    notify: Reiniciar mysql

  - name: Aplicar handlers YA (necesito mysql arriba)
    meta: flush_handlers

  - name: Cargar datos (requiere mysql reiniciado)
    shell: mysql < /tmp/schema.sql
```

## Errores comunes

- **El handler no es una tarea** → no lo pongas en `tasks`, va en `handlers:`.
- **No lo ejecuta si cambia otro archivo que no notifica** → tienes que `notify` explícitamente.
- `notify` mal escrito (nombre distinto) → Ansible lo ignora en silencio. Revisa el log `RUNNING HANDLER`.
- Si el play falla antes de llegar al final, los handlers pueden **no** correr (a menos que uses `force_handlers: true` o `meta: flush_handlers`).

## `changed_when` y handlers (caso common con shell)

Si tu módulo `shell` no reporta `changed` automáticamente, el handler nunca se dispara. Solución:

```yaml
- name: Regenerar cache
  shell: /usr/local/bin/regen-cache
  notify: Reiniciar app
  changed_when: "'cambios' in regen_result.stdout"
```

## Puntos clave

1. Handler = tarea diferida que corre al final, 1 vez, si hubo `changed`.
2. `notify` + `handlers:` es el patrón recarga-reinicio idempotente.
3. `listen` agrupa muchas notificaciones en un handler; `flush_handlers` los adelanta.
4. Ideal para: reiniciar servicios tras `template`/`copy`, recargar firewalls, limpiar cachés.

## Siguientes pasos

- La pieza de "¿cambió?" → [[Condicionales en Ansible]]
- Qué hace un handler en un **rol** → [[Roles en Ansible]]
- `service` vs `systemd` → [[Módulos esenciales en Ansible]]

# References