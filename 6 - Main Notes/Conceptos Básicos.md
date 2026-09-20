2026-09-19 19:22

Status:

Tags: [[Ansible]]

# Conceptos Básicos

## ¿Qué es Ansible?

Ansible es una **herramienta de automatización de TI** de Red Hat, open source y escrita en Python. Con ella describes cómo debe verse tu infraestructura (servidores, aplicaciones, servicios) y Ansible se encarga de **llevarla a ese estado**.

Se usa para:
- **Aprovisionar**: crear y preparar servidores, usuarios, paquetes.
- **Configurar**: instalar y configurar software (nginx, MySQL, aplicaciones...).
- **Desplegar**: subir versiones de aplicaciones a servidores.
- **Orquestar**: coordinar tareas entre muchos servidores a la vez (por ejemplo, actualizar 50 servidores y reiniciarlos en orden).

## La analogía del director de orquesta

Imagina un concierto:

- El **director** es el *control node* (tu máquina desde la que mandas órdenes).
- Los **músicos** son los *managed nodes* (los servidores que administras).
- La **partitura** es el *playbook* (la receta YAML que describes).
- Y lo importante: cada músico **ya sabe tocar su instrumento**, no le pasas las notas una a una. En Ansible, cada servidor ya sabe hacer tareas como "install", "copy" o "restart" — gracias a los **módulos**. Tú solo dices *qué* quieres, no *cómo*.

## Modelo agente-libre y push

A diferencia de herramientas como Puppet o Chef (que necesitan un **agente** instalado en cada servidor), Ansible **no instala nada en los servidores que administra**:

- Se conecta a ellos por **SSH** (puerto 22).
- Solo necesita que tengan **Python** instalado.
- Ejecuta pequeñas piezas de código llamadas **módulos** y recoge el resultado.

Es un modelo **push**: el control node "empuja" las órdenes hacia los servidores. Comparación rápida:

|Ansible|Puppet / Chef|
|---|---|
|Agente libre|Requiere agente instalado|
|Modelo push (tú mandas)|Modelo pull (el agente consulta)|
|Se aprende en horas|Curva de aprendizaje alta|
|Imperativo *declarativo* + módulos|Lenguaje propio (DSL)|

## Declarativo e idempotente: las dos palabras mágicas

- **Declarativo**: tú describes el *resultado final*, no los pasos. No dices "corre este comando y luego este otro". Dices: *"el paquete nginx debe estar instalado"* y *"el servicio nginx debe estar corriendo"*.

- **Idempotente**: puedes ejecutar el mismo playbook mil veces y el resultado es el mismo, **sin efectos secundarios**. La segunda vez, Ansible detecta que ya todo se cumple y responde `ok` en vez de `changed`. No vuelve a instalar lo ya instalado, no reinicia lo que ya está corriendo.

> "Si algo ya está en el estado correcto, Ansible no hace nada."

## Componentes clave (los verás en todas las notas)

1. **Control node**: la máquina donde tienes instalado Ansible (Linux, macOS o WSL).
2. **Managed nodes**: los servidores que administras.
3. **Inventario**: el archivo que lista los servidores y los agrupa.
4. **Módulos**: la biblioteca de "acciones" listas para usar (instalar, copiar, reiniciar...).
5. **Playbooks**: archivos YAML con la receta a ejecutar.
6. **Roles**: playbooks empaquetados y reutilizables.
7. **Ansible Vault**: para cifrar contraseñas y secretos.
8. **Ansible Galaxy**: el repositorio público de roles y collections.

## Puntos clave

1. No necesitas programar: el lenguaje es **YAML**, legible por humanos.
2. **Idempotencia** = puedes correr todo varias veces sin romper nada.
3. Sin agentes = más fácil de empezar y de mantener.
4. Conecta por **SSH**, así que el primer paso es tener llaves SSH configuradas.
5. Lo que aprendes en una máquina aplica a **1 o 1000** servidores por igual.
6. Ansible es la base del negocio de **Red Hat / Red Hat Insights**, y se considera el estándar de facto de la automatización de configuración.

## Siguientes pasos lógicos

- Cómo se instala → [[Instalación de Ansible]]
- Cómo funciona por dentro → [[Arquitectura de Ansible]]
- Cómo agrupas tus servidores → [[Inventarios en Ansible]]
- Primeros comandos → [[Comandos ad-hoc en Ansible]]
- Tu primera receta → [[Playbooks en Ansible]]

# References