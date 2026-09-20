2026-09-19 23:29

Status:

Tags: [[Ansible]]

# Ejercicios Practicar Ansible

> Nota de práctica para trabajar los conceptos sobre un laboratorio local real.
> Asume que ya hiciste [[Instalación de Ansible]] y conoces [[Conceptos Básicos]].

## Cómo se resuelven (método de trabajo)

1. **Laboratorio**: 2-3 VM/containers `ubuntu` (o `ssh` a un Vagrant). Contraseña de pruebas o claves.
2. Para cada ejercicio: plantéate **mínimo 2 playbooks** o 1 play con `--tags`.
3. VERIFICA en los hosts el efecto real (no te creas el `changed`).
4. Escribe lo que aprendiste al lado (Zettelkasten).
5. Usa todo el tiempo: lint + syntax-check + check-diff (ver [[Validación y linting en Ansible]]).

## Bloque 1 — Fundamentos

- **E1. Inventario mínimo**: inventario con 3 hosts (web1, web2, db1) y grupos `webservers`, `databases`, un host_vars y un group_vars. Comprueba con `ansible-inventory --list`.
- **E2. Ad-hoc básico**: `ping`, `setup` (fact), `command`, `apt update`. Juega con `-b`, `-u`, `--become` y `-i`.
- **E3. Variables**: `vars`, `vars_files`, `group_vars/all`, mezcla con hechos (`ansible_hostname`). Imprime 5 con `debug`. (Ver [[Variables y Hechos en Ansible]].)
- **E4. Condicionales**: un play que instale un paquete SOLO si es RedHat vs Debian (`ansible_os_family`) y gestione un servicio según un flag. (Ver [[Condicionales en Ansible]].)
- **E5. Loops**: crea 3 usuarios con `loop` y lista desde `vars`; repite sobre un dict para crear archivos por usuario. (Ver [[Loops en Ansible]].)

## Bloque 2 — Estructura y reutilización

- **E6. Templates**: crea `nginx_vhost.conf.j2` con variables de servidor; resuelve hechos (hostname, IP) dentro del template. (Ver [[Templates y Jinja2 en Ansible]].)
- **E7. Filtros**: usa `default`, `upper`, `join`, `ipaddr` sobre variables. (Ver [[Filtros Jinja2 en Ansible]].)
- **E8. Handlers**: cambia una config, `notify` el restart, y comprueba que solo corre si cambió. (Ver [[Handlers en Ansible]].)
- **E9. Roles**: convierte tu play en 4-5 roles (nginx, app, db, monit) y usa `include_role` condicional. (Ver [[Roles en Ansible]].)
- **E10. Estructura de proyecto**: monta la layout de [[Estructura de un proyecto Ansible]] completa (inventory/, plays/, roles/, ansible.cfg) y corre `site.yml` con `-i` por entorno.

## Bloque 3 — Fiabilidad

- **E11. Manejo de errores**: un play con `block/rescue/always` que falle deliberadamente (paquete inexistente) y lo recobre re-intentando con log. (Ver [[Manejo de errores en Ansible]].)
- **E12. Validación**: `ansible-lint` + `--syntax-check` + `--check --diff` sobre todo el proyecto; corrige TODAS las advertencias. (Ver [[Validación y linting en Ansible]].)
- **E13. Tags**: segmenta el proyecto con `tags` y ejecuta solo una fase (solo diagnóstico / solo deploy). (Ver [[Tags en Ansible]].)

## Bloque 4 — Seguridad y contexto real

- **E14. Vault**: cifra un `group_vars/prod/secrets.yml` con vault; usa `--ask-vault-pass` y `ansible-vault view`. (Ver [[Ansible Vault]].)
- **E15. Seguridad**: aplica 3 recomendaciones de [[Seguridad en Ansible]] a un proyecto tuyo.
- **E16. Multi-entorno**: prepara dev/staging/prod con inventarios separados y `group_vars` por entorno. (Ver [[Ansible en múltiples entornos]].)
- **E17. Galaxy**: instala un rol de `community` con `requirements.yml` versionado. (Ver [[Ansible Galaxy]].)
- **E18. Avanzado**: monta un proyecto con estrategias `linear`/`free`, `serial`, `run_once`, `delegate_to`. (Ver [[Opciones avanzadas de ejecución en Ansible]].)
- **E19. Integración real**: un play que gestione 2 servicios + su copia a un host remoto, usando roles + handlers + vault + tags.

## Meta final (proyecto de cierre)

> **"Gestión de una flota PHP en 3 entornos"**: 4 roles (common, php, webserver, backup), inventario multi-entorno, secrets via vault, CI que corre lint + syntax + check-diff, y un `site.yml` con tags segmentados. Si E19 corre limpio y auditable → dominas el temario.

## Checklist de verificación

- [ ] Inventario listo y `ansible-inventory --list` sin errores.
- [ ] `ansible-lint` en verde.
- [ ] `--syntax-check` OK.
- [ ] `--check --diff` sin sorpresas en staging.
- [ ] Vault: `ansible-vault view` abre y `no_log` activo.
- [ ] Handlers y tags se comportan como esperabas.
- [ ] Escribiste qué aprendiste en notas tipo [[Variables y Hechos en Ansible]].

# References