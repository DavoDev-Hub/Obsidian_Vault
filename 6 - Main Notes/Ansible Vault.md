2026-09-19 22:44

Status:

Tags: [[Ansible]]

# Ansible Vault

## ¿Qué problema resuelve?

Los playbooks suelen vivir en **repositorios Git compartidos** (o al menos en tu disco). Pero algunas variables son sensibles: contraseñas de BD, tokens de API, llaves SSH, certificados. **Nunca deben estar en texto plano.**

**Ansible Vault** cifra archivos (o variables) completos con una contraseña. El playbook **se redacta con cleartext normal**, pero el archivo de secretos es ilegible salvo que tengas la contraseña.

## Cómo funciona por dentro

- Algoritmo: **AES-256** (configurable: `--cipher-type AES256`)
- El archivo cifrado empieza por la cabecera `$ANSIBLE_VAULT;1.1;AES256`
- Ansible descifra solo **en memoria** al ejecutar, nunca guarda texto plano en el managed host.
- Cada archivo puede tener contraseñas **distintas** o todas iguales.

## Comandos básicos

```bash
# Crear un archivo cifrado nuevo (abre editor)
ansible-vault create secretos.yml

# Editar (vuelve a abrir el editor)
ansible-vault edit secretos.yml

# Cifrar un archivo ya existente
ansible-vault encrypt secretos.yml

# Ver el contenido descifrado (sin tocar el archivo)
ansible-vault view secretos.yml

# Quitar el cifrado
ansible-vault decrypt secretos.yml

# Cambiar la contraseña
ansible-vault rekey secretos.yml
```

## Encriptar una variable suelta (más fino)

En vez de cifrar un archivo entero, cifra solo el valor que importa:

```bash
ansible-vault encrypt_string 'db_password123' --name 'db_password'
```

El output es un YAML listo para pegar:

```yaml
db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          663864...
```

Puedes poner ese bloque dentro de `group_vars/`, `vars_files`, o tu inventario.

## Usarlo en playbooks

La forma más limpia: separar secretos en un archivo que apuntas con `vars_files`:

```yaml
vars_files:
  - secrets.yml          # cifrado con Vault
```

Playbook normal + archivo cifrado. Al ejecutar, pides la contraseña:

```bash
# Pregunta la contraseña interactivamente
ansible-playbook playbook.yml --ask-vault-pass

# O desde archivo (no lo commitees)
ansible-playbook playbook.yml --vault-password-file ~/.vault_pass

# O con ansible.cfg (seguro a mano)
```

## Y los inventarios

También puedes cifrar inventarios enteros:

```bash
ansible-vault encrypt inventario/prod/hosts.yml
```

Pero cuidado: el inventario contiene normal variables. Mejor patrón: **inventario plano + secretos en `group_vars` cifrados.**

## Contraseña desde archivo (seguro y no-interactivo)

Crea un archivo con la contraseña, protege permisos, y apúntalo:

```bash
echo 'MiSecretoSúperLargo' > ~/.vault_pass
chmod 600 ~/.vault_pass

# ansible.cfg
# [defaults]
# vault_password_file = ~/.vault_pass
```

> En CI/CD la contraseña suele venir de un **secret manager** (GitLab CI, GitHub Actions, Vault HashiCorp) y se escribe a un archivo temporal antes de `ansible-playbook`.

## Repaso: ¿qué contiene tu archivo cifrado?

Ejemplo completo:

```yaml
# secrets.yml (texto plano durante edición)
db_user: itdespliegues
db_password: p4ssw0rd!seguro
api_token: tok_abc123
```

Cifrado → todo el archivo se vuelve ilegible, pero el playbook lo consume de la misma manera (`{{ db_password }}`).

## Buenas prácticas

1. **Nunca commits secretos en texto plano** en git.
2. Separa `vars_files: - secrets.yml` con Vault; define defaults en variables normales.
3. No mezcles secretos con "config normal" en el mismo archivo, o tendrás que cifrar demasiado.
4. La contraseña de Vault NO la pongas en el repositorio; úsala vía secret manager en CI.
5. Diferentes `ansible-vault` instances por entorno con distintas contraseñas (dev, prod).
6. `ansible-vault view` para revisar manualmente sin editar nada.
7. En backups, copia también la contraseña (o pierdes el acceso).

## Puntos clave

- Ansible Vault = cifrado AES-256 de archivos o variables.
- `{{ }}` sigue funcionando igual sobre variables cifradas.
- `--ask-vault-pass` / `--vault-password-file`.
- Secretos siempre fuera del playbook principal.

## Siguientes pasos

- Variables sensibles "sueltas" → [[Variables y Hechos en Ansible]]
- Arquitectura de secrets en equipos → [[Seguridad en Ansible]]
- Referencias de configuración → [[Configuración de Ansible (ansible.cfg)]]

# References