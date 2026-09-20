2026-09-19 19:55

Status:

Tags: [[Ansible]]

# Arquitectura de Ansible

## El panorama completo

Ansible está compuesta por piezas que se conectan en cadena. Esta es la vista general:

```text
        ┌─────────────────────────────────────────────────────────┐
        │                      CONTROL NODE                       │
        │                                                         │
        │   [Inventario] → [Playbook] → [Módulos/Collections]     │
        │                     │                                   │
        └─────────────────────┼───────────────────────────────────┘
                              │  SSH (puerto 22) — modelo PUSH
        ┌─────────────────────┼───────────────────────────────────┐
        ▼                     ▼                    ▼               ▼
   Managed node          Managed node        Managed node     Managed node
   (Python 3)            (Python 3)          (Python 3)       (Python 3)
```

## El flujo de una ejecución, paso a paso

Cuando ejecutas `ansible-playbook playbook.yml`, esto es lo que pasa:

1. **Lee el inventario**: Ansible sabe qué hosts existen, en qué grupos están y qué variables tienen.
2. **Carga el playbook**: interpreta el YAML: cuántos plays, contra qué hosts, qué tareas.
3. **Resuelve variables y facts**: conecta (SSH) y recolecta *facts* de cada servidor (SO, RAM, IP, etc.), salvo que lo desactives.
4. **Ejecuta cada módulo en orden**: por cada tarea, sube una pieza de Python al servidor, la ejecuta y recoge el resultado en **JSON**.
5. **Reporta**:
   - `ok` → ya estaba en el estado deseado (idempotencia).
   - `changed` → hizo cambios para llegar al estado deseado.
   - `failed` → algo salió mal.
   - `skipped` → una condición (`when`) lo saltó.

## Componentes de la arquitectura

### 1. Control node
La máquina que tiene Python y Ansible. **Toda la lógica vive aquí.** No necesita bases de datos, brokers ni daemons corriendo en segundo plano: es totalmente *"stateless"* (sin estado). Puedes apagar el control node y los servidores siguen funcionando; solo no recibirán nuevas órdenes.

El control node:
- Se conecta a los nodos por SSH.
- Envía módulos (código Python) en vez de ejecutar comandos a ciegas.
- Soporta **ejecución en paralelo** (forks) a cientos de servidores.

### 2. Managed nodes
Los servidores que administras. Requisitos: SSH activo y Python ≥ 3. No necesitan nada más: ni agente, ni puertos extra abiertos aparte del 22.

### 3. Inventario
La "lista de direcciones". Define hosts, grupos, y variables. Existe en versiones:
- **Estático**: archivo INI o YAML.
- **Dinámico**: plugins que consultan proveedores cloud (AWS, GCP) o herramientas (VMware, etc.). Detalle en [[Ansible en múltiples entornos]].

### 4. Módulos
Pequeños programas autocontenidos que Ansible ejecuta en el nodo. Son el corazón de Ansible: la mayoría son **idempotentes**, es decir, revisan el estado actual antes de actuar y solo cambian si es necesario.

Ejemplos: `apt` (instala paquetes), `copy` (copia archivos), `service` (arranca/para servicios), `user` (gestiona usuarios), `shell` (ejecuta comandos).

### 5. Playbooks
Archivos YAML que describen el *estado final deseado*. Contienen plays, tasks, vars, handlers. Es la "partitura" del director.

### 6. Collections
Paquetes que agrupan módulos, plugins, roles y docs sobre un tema o proveedor. Ejemplo: `ansible.builtin` (módulos core), `community.general`, `amazon.aws`, `cisco.ios`. Las versions de módulos se referencian por su *Fully Qualified Collection Name*: `ansible.builtin.copy`, `community.general.ufw`. Detalle en [[Ansible Galaxy]].

## Modelo push vs modelo pull

|Modelo|Quién manda|Ejemplos|Ventaja|Desventaja|
|---|---|---|---|---|
|**Push**|El control node empuja órdenes|**Ansible**|Simple, control inmediato, sin agentes|Necesitas red/alcanzabilidad al nodo|
|**Pull**|El nodo pregunta al servidor central|Puppet, Chef|Funciona en redes restringidas|Requiere agente + servidor central|

Existe `ansible-pull` para situaciones pull (nodo hace git pull de los playbooks y se autoejecuta), pero el flujo estándar es push.

## Cómo se conecta: los connection plugins

No todo es SSH. Ansible abstrae la conexión en *connection plugins*:

|Plugin|Cuándo usar|Puerto por defecto|
|---|---|---|
|`ssh`|Servidores Linux/Unix (el estándar)|22|
|`local`|Ejecutar contra la propia máquina o vía wrappers|—|
|`winrm`|Servidores Windows (PowerShell)|5985/5986|
|`network_cli`|Dispositivos de red (Cisco, Arista...)|22|
|`community.docker.*`|Contenedores Docker|—|
|`kubernetes.core`|Clusters Kubernetes|API server|

La conexión se define a nivel de host en el inventario (`ansible_connection=winrm`, etc.).

## Módulos: el detalle de idempotencia

Cuando un módulo corre, responde en JSON con:

```json
{
  "changed": false,
  "msg": "El paquete nginx ya está instalado"
}
```

Ansible usa estos campos para decidir el siguiente paso:
- `changed: true` dispara los **handlers** notificados.
- `failed` para en seco (o pasa al `rescue`).

## Puntos clave

1. **Sin daemons ni agentes**: el control node es la única pieza con Ansible.
2. El pipeline completo es: `inventario → playbook → módulo → SSH → Python → JSON → reporte`.
3. Los módulos son idempotentes: revisan antes de actuar.
4. La salida de cada tarea es JSON→ interpretable (facts, register, condiciones).
5. Los *connection plugins* abstraen Linux, Windows, red y cloud bajo la misma interfaz.
6. Los *forks* (por defecto 5) controlan cuántos nodos se procesan en paralelo.

## Relación con otras notas

- Instálalo y pruébalo → [[Instalación de Ansible]]
- Agrupa tus hosts → [[Inventarios en Ansible]]
- Escribe tu primera receta → [[Playbooks en Ansible]]
- Las piezas de acción → [[Módulos esenciales en Ansible]]
- Cómo puede conectarse a Windows/red/cloud → [[Ansible en múltiples entornos]]

# References