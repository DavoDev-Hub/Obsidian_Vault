2026-09-19 21:45

Status:

Tags: [[Ansible]]

# Variables y Hechos en Ansible

## Variables: dónde se definen y cómo se usan

Las variables son la memoria de tu playbook: valores que pueden cambiar según el host, el grupo o el entorno, sin tocar la lógica.

### Fuentes de variables (y su precedencia, de menor a mayor)

1. Hechos (facts) — ver abajo.
2. `vars` del inventario (INI/YAML) y `host_vars`/`group_vars`.
3. `vars_files` / `include_vars`.
4. `vars` de roles (defaults tienen la MENOR prioridad).
5. `vars:` del play.
6. `vars` pasadas con `--extra-vars` (`-e`) → **gana a todo**.

Regla mental: **lo más "local/inmediato" manda**. `-e` > play `vars` > group_vars > facts.

### Definir y usar

```yaml
vars:
  app_name: miweb
  app_port: 8080
  usuarios: [davo, ana]

tasks:
  - name: Pintar variable
    debug:
      msg: "La app {{ app_name }} corre en {{ app_port }}"
```

Siempre `{{ }}` (Jinja2). Detalle en [[Templates y Jinja2 en Ansible]].

### `vars_files` para limpiar

```yaml
vars_files:
  - vars/app.yml
  - vars/secrets.yml
```

## Hechos (facts): datos del servidor

Cuando un play corre, Ansible por defecto consulta cada nodo y guarda datos reales del sistema en `ansible_facts`:

```yaml
- name: Ver una línea del hecho (IP)
  debug:
    msg: "{{ ansible_default_ipv4.address }}"

- name: Familia del SO (Debian / RedHat)
  debug:
    msg: "{{ ansible_facts['os_family'] }}"
```

Para ver todo:

```bash
ansible servidor -m setup      # dump completo de hechos
```

### Facts más usados

|Fact|Qué contiene|
|---|---|
|`ansible_hostname`|Nombre del host|
|`ansible_default_ipv4.address`|IP privada|
|`ansible_os_family`|Debian / RedHat / Windows / Suse|
|`ansible_distribution`|Ubuntu, CentOS, Rocky...|
|`ansible_distribution_version`|22.04, 9.9...|
|`ansible_architecture`|x86_64, arm64...|
|`ansible_memtotal_mb`|RAM total|
|`ansible_processor_vcpus`|Nº de vCPUs|

## `gather_facts` y `setup`

- Playbooks: `gather_facts: false` si no los usas (más rápido).
- Ad-hoc: `ansible -m setup` manual.
- Custom facts: puedes crear archivos `/etc/ansible/facts.d/*.fact` (JSON/INI) en el nodo; se leen como `ansible_local`.

## Variables "mágicas" (propias de Ansible)

Siempre disponibles, sin definirlas:

|Variable|Qué es|
|---|---|
|`inventory_hostname`|Nombre del host tal como en el inventario|
|`inventory_hostname_short`|Primera parte (antes del primer punto)|
|`group_names`|Lista de grupos del host actual|
|`groups`|Todos los grupos del inventario|
|`hostvars[host]`|Variables de OTRO host (multihost)|
|`ansible_play_hosts` / `ansible_play_batch`|Hosts del play / batch actual|

```yaml
- name: Ver vars del host vecino
  debug:
    msg: "{{ hostvars['db1'].ansible_host }}"
```

## `register`: capturar la salida de una tarea

El resultado de cualquier tarea (stdout, rc, changed...) se guarda en una variable:

```yaml
- name: Ejecutar y registrar
  command: /sbin/getent passwd
  register: passwd

- name: Filtrar stdout
  debug:
    msg: "{{ passwd.stdout_lines }}"
```

Combina con `when` → [[Condicionales en Ansible]].

## Precedencia resumida (memoria rápida)

|Más alta|→|Más baja|
|---|---|---|
|`-e` extra vars|`vars:` play|`vars_files`|`host_vars`|`group_vars`|inventario|
|`set_fact` / `register` (en tiempo real)||facts|roles defaults|

## Buenas prácticas

1. `-e` para secretos y despliegues puntuales; group_vars para lo estable.
2. No pongas secretos en `vars_files` de git → usa [[Ansible Vault]].
3. `vars_files` para separar "config" de "secretos".
4. Emplea `hostvars` con cuidado al escalar (no abusar).
5. Ve lo que recoges con `setup` antes de inventar un fact.

## Siguientes pasos

- Reaccionar a hechos → [[Condicionales en Ansible]]
- Repetir sobre ellos → [[Loops en Ansible]]
- Multi-entorno  \u2192 [[Ansible en m\u00faltiples entornos]]

# References
