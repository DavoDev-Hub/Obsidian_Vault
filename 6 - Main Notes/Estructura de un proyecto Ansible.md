2026-09-19 23:10

Status:

Tags: [[Ansible]]

# Estructura de un proyecto Ansible

## ¿Por qué una estructura en serio?

Un proyecto Ansible que crece sin orden se convierte en un caos: playbooks de 300 líneas, variables repetidas, secretos en git. Los equipos profesionales (y tu "yo" de 6 meses) agradecen una **layout estándar** que comunica intención: inventarios separados de plays, roles autocontenidos, secretos con Vault.

## Estructura de referencia (la "plantilla de producción")

```text
proyecto-ansible/
├── ansible.cfg                    # config del proyecto (ver [[Configuración de Ansible (ansible.cfg)]])
├── requirements.yml               # roles + collections que el proyecto necesita
├── inventory/
│   ├── production/
│   │   ├── hosts.yml              # inventario (hosts y grupos)
│   │   └── group_vars/            # variables por grupo de PROD
│   │       ├── all.yml
│   │       └── webservers.yml
│   └── staging/
│       ├── hosts.yml
│       └── group_vars/
│           ├── all.yml
│           └── webservers.yml
├── plays/                         # o "playbooks/" — recetas de alto nivel
│   ├── site.yml                   # entrada principal: todo el sistema
│   ├── webservers.yml             # solo webs (con --tags o roles)
│   └── database.yml
├── roles/                         # lógica reutilizable ([[Roles en Ansible]])
│   ├── nginx/
│   ├── postgresql/
│   └── common/
├── libraries/                     # módulos personalizados (raro, pero existe)
├── module_utils/
├── filter_plugins/                # filtros Jinja2 propios ([[Filtros Jinja2 en Ansible]])
├── lookup_plugins/
├── files/                         # archivos "crudos" globales (raro)
├── templates/                     # plantillas globales (mejor en roles)
├── scripts/                       # scripts auxiliares del pipeline
└── vars/                          # variables de play directas (opcional)
```

La clave no es la foto literal: es la **separación de intereses**:

|Capa|Qué contiene|Por qué aparte|
|---|---|---|
|`inventory/`|Hosts + group_vars/host_vars|Es el "dónde". Cambia por entorno|
|`plays/`|Playbooks de alto nivel|Es el "qué hacer" reutilizable|
|`roles/`|Lógica parametrizada|Es el "cómo", aislado y testeable|
|`ansible.cfg`|Defaults del proyecto|Cero flags repetidos|
|`requirements.yml`|Dependencias|Reproducible al 100%|

## Qué va en cada nivel (pensado en 3 entornos)

```text
inventory/
├── production/
│   ├── hosts.yml
│   └── group_vars/{all,webservers}.yml
├── staging/
│   ├── hosts.yml
│   └── group_vars/{all,webservers}.yml
└── dev/
    ├── hosts.yml
    └── group_vars/{all,webservers}.yml
```

`plays/site.yml` corre igual contra los tres; solo cambia `-i inventory/<env>`.

```bash
ansible-playbook plays/site.yml -i inventory/production/hosts.yml
ansible-playbook plays/site.yml -i inventory/staging/hosts.yml
```

## Convenciones de nombres y buenas prácticas

1. **Playbooks imperativos, roles declarativos**: `plays/` son orquestación corta; toda la lógica vive en `roles/`.
2. Nombres en **snake_case** para archivos y carpetas (portable entre SO).
3. `group_vars/all.yml` solo para lo que aplica a TODO el entorno; el resto en `group_vars/<grupo>.yml`.
4. Secretos jamás en `group_vars` común: ve a [[Ansible Vault]].
5. **`ansible.cfg` versionado en el repo** → todo el equipo comparte defaults.
6. `.gitignore`: things like `*.retry`, `inventory/**/vault-pass*`, archivos temporales `*.tmp`.
7. Un `README.md` histórico: cómo correr, entorno, dependencias.
8. `requirements.yml` fijando versiones → CI y compañeros reproducen exactamente lo mismo (ver [[Ansible Galaxy]]).

## Ejemplo mínimo pero REAL para empezar

```text
mi-proyecto/
├── ansible.cfg
├── requirements.yml
├── inventory/staging/hosts.yml
├── plays/site.yml
└── roles/common/tasks/main.yml
```

## Puntos clave

1. La estructura no es para "cumplir", es para **sobrevivir creciendo**.
2. **Inventario = datos por entorno**, separado del código.
3. **Roles = lógica reutilizable**; plays = delgadas y legibles.
4. ansible.cfg + requirements.yml en el repo = equipo alineado.
5. Secretos: solo Vault.

## Siguientes pasos

- Define las capas con roles buenos → [[Roles en Ansible]]
- Distribuye y declara dependencias → [[Ansible Galaxy]]
- Variables por entorno → [[Variables y Hechos en Ansible]]
- Estructura multi-entorno completa → [[Ansible en múltiples entornos]]
- Practica con esta plantilla → [[Ejercicios Practicar Ansible]]

# References