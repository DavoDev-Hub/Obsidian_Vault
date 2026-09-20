2026-09-19 22:36

Status:

Tags: [[Ansible]]

# Ansible Galaxy

## ¿Qué es?

**Ansible Galaxy** es el repositorio público de **roles** y **collections** de Ansible. Es el "npm/PyPI/Maven" de la automatización: si alquien ya automatizó algo, seguramente está aquí, probado y versionado.

URL: `galaxy.ansible.com`

Dos "tipos de contenido" que conviene no confundir:

- **Role**: tu estructura de carpetas [[Roles en Ansible]] versionada y publicada.
- **Collection**: un paquete más grande que agrupa **módulos, roles, plugins y documentación** (ej. `ansible.builtin`, `community.general`, `cisco.ios`).

> La colección es el **formato de empaquetado moderno**. Ansible core trae `ansible.builtin` incluida; el resto las instalas según necesidades.

## Buscar

```bash
# Desde el CLI (no navegador)
ansible-galaxy search nginx --author dave
ansible-galaxy info geerlingguy.nginx
```

Desde la web: escribe el rol y filtra por "stars" / "downloads / "mis collections".

## Instalar roles

```bash
# Por nombre autor.rol (FQCN de roles)
ansible-galaxy install geerlingguy.nginx

# A un directorio concreto
ansible-galaxy install -p roles geerlingguy.nginx

# Con version específica
ansible-galaxy install geerlingguy.nginx,3.9.1

# Listar lo instalado
ansible-galaxy list

# Desinstalar
ansible-galaxy remove geerlingguy.nginx
```

## Instalar collections

```bash
# La colección + sus dependencias
ansible-galaxy collection install community.general

# Versión concreta
ansible-galaxy collection install community.general:5.8.0

# Verificar qué version hay
ansible-galaxy collection list
```

## `requirements.yml` — declarar dependencias del proyecto

En vez de instalar de memoria, versiona en un archivo y usa:

```yaml
# requirements.yml
collections:
  - name: community.general
    version: ">=7.0.0"
  - name: cisco.ios
    version: ">=5.0.0"

roles:
  - name: geerlingguy.nginx
    version: "3.9.1"
  - name: geerlingguy.postgresql
```

```bash
ansible-galaxy install -r requirements.yml
ansible-galaxy collection install -r requirements.yml
```

Esto debe estar en tu repositorio → repetible en CI y para todo el equipo.

## Collections: qué contiene típicamente

Una collection es un árbol:

```
ansible_collections/
└── community/
    └── general/
        ├── plugins/
        │   ├── modules/
        │   ├── lookup/
        │   └── filter/
        ├── roles/
        ├── docs/
        └── meta/
```

Cuando en un playbook ves `community.general.ufw`, `cisco.ios.ios_command`, `amazon.aws.ec2_instance` → estás usando collections (FQCN).

## Galaxy con "modo colección" desde el comienzo (recomendación)

Con `collections_path` configurado, los FQCN apuntan a collections instaladas. Configure:

```ini
[defaults]
collections_path = ~/.ansible/collections:/usr/share/ansible/collections
```

## Crear y publicar tu propio rol

```bash
# 1. Esqueleto
ansible-galaxy init mi-rol

# 2. Rellénalo (tasks, defaults, meta...)

# 3. Autentícate en Galaxy (token desde la web)
ansible-galaxy login

# 4. Empaqueta y publica
ansible-galaxy publish
```

## Buenas prácticas Galaxy

1. Define dependencias en `requirements.yml` y commitealo.
2. Pines de versión fija en `requirements.yml` (no `latest` en prod).
3. Prefiere **collections** sobre roles sueltos en equipos grandes.
4. Roles públicos: revisa estrellas + análisis (no instales cualquier cosa con secretos).
5. Documenta de dónde viene cada rol para mantener su propio `README`.
6. Para producción: considerar un **mirror/Airgap** (Artifactory como Galaxy privado → `ansible-galaxy` con `--ignore-errors`).

## Puntos clave

- Galaxy = repositorio de roles + collections.
- `ansible-galaxy install` para roles; `collection install` para collections.
- `requirements.yml` = manifest de dependencias (como package.json).
- FQCN (`autor.coleccion.modulo`) es el estándar moderno.
- Pública lo que reutilices → colaborará la comunidad.

## Siguientes pasos

- La estructura de carpetas mundo real → [[Estructura de un proyecto Ansible]]
- Tu colección dentro del flujo → [[Roles en Ansible]]
- Pulir el entorno de trabajo → [[Configuración de Ansible (ansible.cfg)]]

# References