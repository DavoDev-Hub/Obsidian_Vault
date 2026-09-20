2026-09-19 23:18

Status:

Tags: [[Ansible]]

# Manejo de errores en Ansible

## El porqué: fallar es normal, fallar limpio es oro

En el mundo real los servidores fallan: un paquete no existe, un servicio no levanta, un usuario ya no está, un puerto está ocupado. Un playbook bien hecho **sabe que estas cosas pasan** y decide qué hacer por cada caso. No se trata de "no fallar", sino de **fallar de forma controlada y dar mensajes útiles**.

## Herramienta 1: `failed_when` — yo decido cuándo es fallo

Por defecto un módulo falla si devuelve `failed: true` o un código de retorno distinto de 0. Pero a veces la salida real del servicio es lo que define el fracaso.

```yaml
- name: Lanzar migración de base de datos
  shell: ./migrate.sh
  register: migracion
  failed_when:
    - migracion.rc != 0
    - "'ERROR' in migracion.stderr"
```

Aquí la tarea **no falla** aunque el script devuelva rc!=0 si es un "no había nada que migrar". Tú defines la regla de fracaso.

### Caso clásico: Chequear si algo "está vacío"

```yaml
- name: Buscar backups recientes
  find:
    paths: /var/backups
    patterns: "*.sql"
    age: "-3d"
  register: backups

- name: Fallar si no hay ningún backup en 3 días
  fail:
    msg: "¡No hay backups recientes! Abortando."
  when: backups.files | length == 0
```

El módulo `fail` **termina el play con un mensaje claro**. Es tu "assert manual".

## Herramienta 2: `ignore_errors` — el "no me importa"

```yaml
- name: Comprobar si hay puertos sueltos (que no falle)
  shell: ss -ltn
  register: puertos
  ignore_errors: true
```

⚠️ Con cuidado: si ignoras todo, después puedes usar `{{ puertos.rc }}` o `{{ puertos.failed }}` en un `when`. Es **delegación de responsabilidad**, no olvidarse.

Típico: `when: algun_proceso.rc == 1` (proceso NO ejecutándose → hago algo).

## Herramienta 3: `changed_when` — controlar el "cambió"

Es la hermana gemela. Reemplaza cuando el módulo no sabe reportar cambio correctamente.

```yaml
- name: Descargar y ejecutar script idempotente
  shell: ./install.sh --if-not-exists
  changed_when: "'instalado' in resultado.stdout"
```

## Herramienta 4 (LA GRANDE): `block` / `rescue` / `always`

Son el **try/catch de Ansible**:

```yaml
- name: Bloque con recuperación
  block:
    - name: Intentar conectar a DB
      command: mysql -h {{ db_host }} -e "SELECT 1"
  rescue:
    - name: DB caída — reiniciar y reintentar
      systemd:
        name: mysqld
        state: restarted
    - name: Reintento
      command: mysql -h {{ db_host }} -e "SELECT 1"
  always:
    - name: Log siempre
      debug:
        msg: "Se ejecutó el bloque (con o sin error)"
```

- `block` = lo que intentas.
- `rescue` = SE EJECUTA solo si algo en `block` falla.
- `always` = corre SIEMPRE, funcione o no block.

Esto es **la herramienta profesional para tolerancia a fallos real**.

### Y anidado funciona

```yaml
- block:
    - name: Probar con una API
      uri:
        url: "https://api.service.com"
  rescue:
    - block:
        - name: API caída, probar espejo
          uri:
            url: "https://mirror-api.service.com"
      rescue:
        - name: Ambas caídas
          fail:
            msg: "Ninguna API disponible"
```

## Herramienta 5: control global

|Opción (en play)|Qué hace|
|---|---|
|`any_errors_fatal: true`|Si un host falla, TODOS paran (p. ej. config maestro)|
|`force_handlers: true`|Los handlers corren AUNQUE otra tarea falle|
|`max_fail_percentage: 20`|Permite fallar un % sin abortar|
|`serial` (ver ejecución avanzada)|Limita lote a lote|
|`run_once` + `delegate_to`|Decides QUÉ host hace el trabajo clave|

### `meta: flush_handlers` y el reinicio "asegurado"

```yaml
- name: Cambiar config y asegurar reinicio
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/
  notify: Reiniciar nginx

- name: Ejecutar handlers YA (aunque algo falle después)
  meta: flush_handlers
```

## Herramienta 6: `assert` / validación anticipada

Antes de empezar a cambiar nada, comprueba los prerrequisitos:

```yaml
- name: Validación previa
  assert:
    that:
      - app_version is defined
      - db_host is defined
      - app_version is version("2.0", ">=")
    fail_msg: "Variables inválidas"
    success_msg: "Prerequisitos OK"
```

## Tabla resumen: cuándo usar cada herramienta

|Situación|Herramienta|
|---|---|
|Un comando falla pero quiero decidir con su salida|`failed_when`|
|Tarea opcional / no bloqueante|`ignore_errors` + `when` con `.rc`/`.failed`|
|Quiero un try/catch|`block` / `rescue` / `always`|
|Fallo de backup/datos = abortar todo|`fail` + `when`|
|Handlers pendientes aun con fallos|`force_handlers` + `meta: flush_handlers`|
|Validar variables al iniciar play|`assert`|
|Fallar todo si uno falla|`any_errors_fatal`|

## Buenas prácticas

1. `failed_when` y `changed_when` siempre sobre **register** (ver [[Variables y Hechos en Ansible]]).
2. `rescue` para reintentos; `always` para limpieza/logs.
3. Usa `any_errors_fatal` SOLO donde el fallo de host crítico debe cascadear.
4. Log hub/consola: `debug` con `msg` o `var` para que el operador vea qué pasó.
5. Nunca ignores errores "por si acaso" sin comentar el propósito.
6. Combina con [[Condicionales en Ansible]] (`.rc`, `is defined`) para decisiones finas.

## Plantilla de un play "tolerante"

```yaml
- hosts: app
  gather_facts: true
  any_errors_fatal: true
  tasks:
    - block:
        - name: Validar pre-requisitos
          assert:
            that:
              - ansible_os_family == 'Debian'
        - name: Instalar app
          apt:
            name: mi-app
            state: present
      rescue:
        - name: Notificar en fallo (webhook)
          uri:
            url: "{{ alert_webhook }}"
            method: POST
            body: "Fallo en {{ inventory_hostname }}"
      always:
        - name: Log operativo
          debug:
            msg: "Fin de ejecución para {{ inventory_hostname }}"
```

## Puntos clave

1. Ansible: `failed_when`, `ignore_errors`, `changed_when`, `block/rescue/always`, `fail`, `assert`.
2. `register` + `when` = la base de TODO el manejo fino.
3. El `rescue` reintenta / responde; el `always` garantiza el cierre.
4. Nada de "fallar random": decide la política de error por play.
5. Monitorea con logs y webhooks, no solo `failed: x`.

## Siguientes pasos

- Combinarlo con condiciones → [[Condicionales en Ansible]]
- Opciones de ejecución y orquestación → [[Opciones avanzadas de ejecución en Ansible]]
- Estructuras robustas → [[Estructura de un proyecto Ansible]]
- Validación con herramientas → [[Validación y linting en Ansible]]

# References