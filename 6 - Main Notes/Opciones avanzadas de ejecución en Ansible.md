2026-09-19 22:50

Status:

Tags: [[Ansible]]

# Orquestación y Opciones Avanzadas de ejecución en Ansible

## El concepto de orquestación

**Orquestación** = coordinar varios pasos entre **múltiples servidores** como una sola operación. Ansible lo hace con playbooks de **varios plays** ([[Playbooks en Ansible]]) apuntando a grupos distintos en orden.

## Control avanzado del flujo

```yaml
---
- name: Play 1 - Bases de datos
  hosts: databases
  gather_facts: true
  strategy: free               # tareas no bloquean: cada host avanza a su ritmo
  tasks:
    - name: Backup
      command: pg_dump -Fc db > /backup/db.dump
      run_once: true           # se ejecuta UNA vez (no por cada host)
      delegate_to: db1
```

Las opciones que más se usan:

|Opción|Qué hace|
|---|---|
|`run_once: true`|Corre la tarea en un solo host del grupo (ej. inicializar clúster)|
|`delegate_to: <host>`|Ejecuta en OTRO host (no en el actual) — el "controlador de salto"|
|`serial: 5`|Procesa de a 5 hosts por tanda (rolling) — productivo en deploy|
|`forks: N`|Cuántas conexiones en paralelo al usar el CLI|
|`strategy: free` / `linear`|`linear` (default) todos en orden; `free` cada host avanza solo|
|`throttle: 3`|Máximo N hosts simultáneos para la tarea (rate-limit)|
|`max_fail_percentage`|Porcentaje de fallo que detiene el play|
|`any_errors_fatal`|Falla total ante el primer error (vs. continuar)|
|`force_handlers`|Ejecuta handlers aun si hay errores|
|`tags`|Filtra por etiquetas que defines tú|

## `delegate_to` y `run_once` — el patrón "hazlo una vez en otro lugar"

```yaml
- name: Preparar certificado (solo en un nodo, con la CA local)
  command: openssl req ... 
  run_once: true
  delegate_to: localhost
  register: cert_result
```

## `serial`: rolling updates clásicos

```yaml
- name: Actualizar web farm sin caída
  hosts: webservers
  serial: 20%                 # tanda del 20% de los hosts cada vez
  tasks:
    - name: Desplegar nueva versión
      copy:
        src: app-v2.tar.gz
        dest: /opt/app.tar.gz
      notify: Reiniciar servicio
```

`serial` acepta número o porcentaje, y listas: `serial: [1, 5, "100%"]` (1, luego 5, luego todos).

## Tags: dividir tu playbook en piezas

```yaml
tasks:
  - name: Instalar paquetes
    apt: ...
    tags: [instalacion]

  - name: Configurar app
    template: ...
    tags: [config, app]

  - name: Reiniciar
    service: ...
    tags: [run]
```

```bash
ansible-playbook playbook.yml --tags config,app
ansible-playbook playbook.yml --tags instalar --limit web1
ansible-playbook playbook.yml --skip-tags config
```

`tags: always` corre siempre salvo que hagas `--skip-tags always`.

## Check mode & diff: "qué pasaría sin tocar nada"

```bash
ansible-playbook playbook.yml --check --diff
```

- `--check` simula sin aplicar cambios (módulos "checks").
- `--diff` muestra qué línea cambiaría en archivos.
- Combinarlo es el ensayo general seguro.

## Buenas prácticas de ejecución

1. Orquesta en el orden real: DB → app → caché.
2. Usa `--limit` / `--tags` para depurar rápido y en un entorno pequeño.
3. `run_once + delegate_to: localhost` para pasos de solo una vez.
4. `serial` para desplegar sin downtime.
5. Revisa siempre `--syntax-check` + `--list-tasks` antes de ejecutar en prod.
6. Guarda logs con retenciones y salida JSON (`-o`) para auditoría.

## Siguientes pasos

- Manejar fallos con elegancia → [[Manejo de errores en Ansible]]
- Hacer que la tarea decida qué hacer → [[Condicionales en Ansible]]
- El entorno "multiplay" → [[Estructura de un proyecto Ansible]]

# References