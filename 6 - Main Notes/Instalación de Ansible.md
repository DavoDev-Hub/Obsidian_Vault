2026-09-19 19:40

Status:

Tags: [[Ansible]]

# Instalación de Ansible

## Regla de oro: dónde se instala y dónde NO

- **Se instala SOLO en el control node** (tu máquina). Es lo único que necesita Ansible instalado.
- **NO se instala NADA en los servidores que administras** (managed nodes). Solo necesitan:
  - Python 3 (ya viene en casi todas las distros).
  - Acceso SSH.

## Control node: los requisitos

Como control node puedes usar:

|Sistema|¿Soportado?|
|---|---|
|Cualquier distribución Linux|Sí, la opción nativa ideal|
|macOS|Sí|
|**Windows**|**No como control nativo** — usa WSL2 con Linux|
|Contenedor (Docker)|Sí|

Esto se debe a que Ansible necesita herramientas estilo Unix (ejecución de procesos, SSH, bash) que Windows no ofrece de forma nativa.

## Instalación en Debian / Ubuntu (vía APT)

La forma más rápida, aunque en algunas distros el paquete oficial está desactualizado:

```bash
# 1. Actualiza los índices de paquetes
sudo apt update

# 2. Instala Ansible desde los repositorios de la distro
sudo apt install -y ansible

# 3. Comprueba la versión
ansible --version
```

Si `ansible --version` sale con una versión vieja (echa un ojo), usa la instalación con pip, que siempre da la última.

## Instalación con pip (la más recomendada, versión siempre actual)

Ansible se distribuye como paquete de Python, así que `pip` te da siempre la última versión estable:

```bash
# 1. Asegura pip y venv
sudo apt install -y python3-pip python3-venv

# 2. Crea un entorno virtual (opcional pero buena práctica)
mkdir -p ~/ansible && cd ~/ansible
python3 -m venv venv
source venv/bin/activate

# 3. Instala Ansible completo (incluye ansible-core + collections principales)
pip install ansible

# 4. Verifica
ansible --version
ansible-galaxy --version
```

> **Nota**: si quieres solo el núcleo (sin collections extra), puedes instalar `ansible-core`. Para empezar, instala `ansible` a secas, que trae todo el ecosistema listo para usar.

Red Hat mantiene la documentación oficial: puedes instalar la última versión desde el repo oficial de Ansible (PPA `ppa:ansible/ansible`) si prefieres apt siempre actualizado:

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

## Instalación en RHEL / Fedora / Rocky (vía dnf/yum)

```bash
# Fedora / RHEL 9 / Rocky 9
sudo dnf install -y ansible-core
```

> En sistemas corporate, Red Hat recomienda el **Red Hat Ansible Automation Platform** (suscripción), pero `ansible-core` open source funciona igual para aprender.

## Instalación en Windows

Ansible no corre nativo en Windows. El camino estándar:

1. Instala **WSL2** (Ubuntu) desde PowerShell: `wsl --install`
2. Dentro de WSL, sigue los pasos de "Instalación con pip".

Los archivos de tu vault/Windows son accesibles desde WSL vía `/mnt/c/...`.

## Instalación en macOS

```bash
# Opción fácil con pip instalado (via Brew)
brew install python@3.12
python3 -m pip install --user ansible
```

## Comprobar que todo funciona: la prueba del ping

Una vez instalado, pruébalo contra "localhost" (tu propia máquina actúa como managed node):

```bash
# Inventario inline: la coma al final es el truco para "solo localhost"
ansible localhost -i localhost, -m ping
```

Salida esperada:

```text
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

`pong` = Ansible se conectó, subió Python y ejecutó un módulo con éxito.

## Preparar los servidores que vas a administrar

Para administrar un servidor remoto hace falta:

1. **SSH habilitado** en el servidor (casi siempre viene con la distro).
2. **Python 3** (en Debian/Ubuntu puede faltar en instalaciones mínimas):

```bash
# En el servidor remoto
sudo apt install -y python3
```

3. **Llaves SSH** para conectar sin contraseña:

```bash
# En tu control node (si no tienes)
ssh-keygen -t ed25519

# Copia la llave pública al servidor
ssh-copy-id usuario@ip-del-servidor

# Prueba manual de acceso
ssh usuario@ip-del-servidor
```

4. Prueba que Ansible llega:

```bash
ansible servidor -i inventario -m ping -u usuario
```

## Puntos clave

1. Ansible se instala **solo una vez** (control node). Nada de agentes en los nodos.
2. En Windows usa **WSL2**, no forcejees con instalación nativa.
3. `pip install ansible` te da la versión más nueva.
4. El inventario define a qué hosts llegar; `localhost,` es el truco para probar solo.
5. La prueba de fuego es `ansible -m ping` → si responde `pong`, todo el pipeline funciona (SSH → Python → módulo).

## Siguientes pasos

- Aprende lo que pasa por debajo → [[Arquitectura de Ansible]]
- Tu inventario de servidores → [[Inventarios en Ansible]]
- Primeros comandos sueltos → [[Comandos ad-hoc en Ansible]]
- Tu primer playbook → [[Playbooks en Ansible]]
- Afinar la configuración → [[Configuración de Ansible (ansible.cfg)]]

# References