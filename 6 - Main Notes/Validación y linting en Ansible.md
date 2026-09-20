2026-09-19 23:28

Status:

Tags: [[Ansible]]

# Validación y linting en Ansible

## Por qué: "funciona en mi máquina" no vuela

Un playbook con 3 módulos mal escritos y 4 typos de variables se descubre **en producción** si no lo validas antes. Ansible te da 3 niveles de red de seguridad:

1. **Sintaxis/parseo** → el play ni carga si está mal formado.
2. **Linting (estático)** → estilo, pitfalls, patrones malos (ansible-lint).
3. **Dry-run real** → ejecución simulada contra el inventario (check mode).

## Nivel 1: `--syntax-check` (rápido y obligatorio)

```bash
ansible-playbook playbook.yml --syntax-check
```

- No contacta con los hosts (solo parsea).
- Falla si: YAML roto, módulo inexistente, `when` o `with_` mal, variable con `[[]]` rota, etc.
- Hazlo **antes de** cualquier ejecución; acoplar a CI: `git push` → syntax-check todas las plays.

> Nota: NO detecta errores *semánticos* (fact mal escrito, host que no existe). Para eso → dry-run.

## Nivel 2: `ansible-lint` (estático + mejores prácticas)

Paquete aparte (no viene con core):

```bash
pip install ansible-lint
ansible-lint playbook.yml roles/ -p
# o directo:  ansible-lint .
```

Qué detecta:
- **Línea larga / formato** (como un linter de código).
- `become` abusado sin necesidad.
- Tareas sin `name`.
- Uso de command/shell en vez de módulo dedicado.
- Handlers no usados, tags duplicados/inconsistentes.
- Roles sin estructura obligatoria de `tasks/main.yml`.

`ansible-lint` se configura en `.ansible-lint` / `skip_list` / `exclude_paths`:

```ini
exclude_paths:
  - .git/
  - vendor_roles/
skip_list:
  - name[missing]
  - fqcn-builtins
```

> En CI: corre `ansible-lint` y añádelo como test. Roles galaxia suelen venir con los suyos.

## Nivel 3: Check mode / dry-run (`--check`)

```bash
ansible-playbook playbook.yml --check
```

Simula qué cambiaría sin aplicarlo. Top para CI y para "qué hará el deploy".

- Módulos declarativos (file, package, template) soportan check ⇒ reportan `changed` sin tocar nada.
- Módulos no declarativos (`shell`, `command`, `docker_container`) **no** predicen → usan `creates`/`register` (ver [[Módulos esenciales en Ansible]] y [[Manejo de errores en Ansible]]).
- En check: `when` a veces no evalúa intentos reales — complementa con `--diff`.

### `--diff` (muestra cambios)

```bash
ansible-playbook playbook.yml --check --diff
```

Si el play edita archivos/templates → enseña el diff que haría. CSO para template/handlers (ver [[Templates y Jinja2 en Ansible]]).

## Buenas prácticas en conjunto

|Paso|Comando|Cuándo|
|---|---|---|
|Parseo|`--syntax-check`|antes de cada demo/CI|
|Estilo/pitfalls|`ansible-lint`|dev/CI siempre|
|Dry-run|`--check --diff`|pre-deploy en entorno de prueba|
|Simulación de limpieza|`--check --diff` + `--tags`|probar un cambio quirúrgico|

> Ciclo típico en CI: `ansible-lint` → `--syntax-check` → `--check --diff` en staging → deploy real.

## Puntos clave

1. **syntax-check**: barato, hazlo primero.
2. **ansible-lint**: estilo + pitfalls + mejores prácticas.
3. **check/diff**: simula el cambio sin tocar el sistema.
4. Los 3 juntos = "validé antes de romper nada".
5. En equipos: CI los dispara en cada PR.

## Siguientes pasos

- Escribe limpio y reutilizable → [[Roles en Ansible]]
- Errores que lanzas al validar → [[Manejo de errores en Ansible]]
- Condicionales para validar pre-flight → [[Condicionales en Ansible]]
- Aplica en CI con opciones avanzadas → [[Opciones avanzadas de ejecución en Ansible]]

# References