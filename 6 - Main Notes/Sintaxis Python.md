2026-07-09 21:55

Status:

Tags: [[3 - Tags/Python]]


# Sintaxis Python

#### 1. Variables y tipos de datos básicos
En Python no declaras el tipo de la variable, Python lo infiere solo:

```python
nombre = "Davo"        # str (texto)
edad = 21               # int (entero)
altura = 1.75            # float (decimal)
es_estudiante = True     # bool (verdadero/falso)
```
No necesitas poner `int nombre` como en otros lenguajes (Java, C#) — simplemente asignas y Python entiende.

#### 2. Listas y diccionarios
**Lista** — una colección ordenada de elementos, se accede por posición (empezando en 0):
```python
frutas = ["manzana", "plátano", "pera"]
print(frutas[0])   # "manzana"
```

**Diccionario** — pares de clave-valor, se accede por la clave (no por posición):
```python
persona = {"nombre": "Davo", "edad": 21}
print(persona["nombre"])   # "Davo"
```

Esto es súper importante para Flask, porque cuando alguien te mande datos en un POST (por ejemplo, un formulario o un JSON), Flask te los va a dar como diccionario.

#### 3. Funciones y parámetros con valor por defecto
Una función se define con `def`:
```python
def saludar(nombre):
    return f"Hola, {nombre}"
```
Puedes darle un **valor por defecto** a un parámetro, para que sea opcional al llamar la función:
```python
def saludar(nombre="Davo"):
    return f"Hola, {nombre}"

saludar()          # "Hola, Davo" (usa el default)
saludar("Ana")     # "Hola, Ana" (usa lo que le pasaste)
```
Esto es exactamente lo que ya usaste en tu ejercicio:
```python
def saludar(hi = 'Davo', lang = 'es'):
```
Aquí `hi` y `lang` tienen valores por defecto, así que si alguien visita `/saludar` sin especificar nada, usa `'Davo'` y `'es'` automáticamente.

### 4. F-strings (formateo de texto)

Una f-string te deja meter variables directo dentro de un string, poniendo `f` antes de las comillas y la variable entre `{ }`:

```python
nombre = "Davo"
edad = 21
mensaje = f"Me llamo {nombre} y tengo {edad} años"
```

Antes de que existieran los f-strings, se hacía con `%s` (que es justo lo que viste en tu ejercicio: `'''...%s...''' % name`) — es una forma más vieja de hacer lo mismo, pero los f-strings son la forma moderna y más legible.

#### 5. Decoradores (`@algo`) — el concepto más importante para Flask

Esto es lo que más cuesta entender al inicio, así que vamos despacio.

**¿Qué es un decorador?** Es una función que "envuelve" a otra función, para agregarle comportamiento extra, sin modificar el código de la función original.

Analogía: imagina que tienes una función normal, y el decorador es como ponerle una capa extra por encima — como poner glaseado sobre un pastel. El pastel (tu función) sigue siendo el mismo por dentro, pero ahora tiene algo extra por fuera.

En Flask, cuando escribes:

```python
@app.route('/hello')
def hello_world():
    return 'Hello, World'
```
El decorador `@app.route('/hello')` le dice a Flask: **"cuando alguien visite la URL `/hello`, ejecuta esta función de aquí abajo"**. Tú no tienes que escribir el código que conecta la URL con la función — Flask ya lo hace por ti, gracias al decorador.












# References


