2026-07-08 23:05

Status:

Tags: [[3 - Tags/Python]] [[Flask]]


# Características principales de Flask

Flask es un microframework utilizado para el desarrollo de aplicaciones web en Python, aunque el término de microframework no te confunda ya que, lo puedes emplear para construir toda clase de aplicaciones web, tanto pequeñas como grandes. Algunas de sus características principales son:

1. Lo mínimo para funcionar, Flask es un framework con una curva de aprendizaje bastante baja, al ser un microframework y traer lo mínimo para poder funcionar resulta bastante fácil de entender, crear nuestro primer hola mundo e ir escalando poco a poco.
    
2. Antigüedad, Flask es un framework con mucho tiempo en el mercado, constantemente va recibiendo actualizaciones para emplear las últimas versiones de Python y dependencias en general e incorporar nuevas características.
    
3. Extensiones: Flask ofrece una amplia variedad de extensiones que permiten agregar funcionalidades adicionales, como validación de formularios, integración con bases de datos, motor de plantillas, entre otros.
    
4. Soporte de la comunidad y buena documentación, al tener una duda sobre como implementar alguna funcionalidad, con una simple búsqueda, seguramente podrás encontrar más información sobre cómo implementar dicha funcionalidad, al tener una comunidad activa y una muy buena documentación.

--- 
#### 1. ¿Qué es Flask?

Flask es un **framework** (conjunto de herramientas ya armadas) para crear aplicaciones web en Python. En vez de escribir desde cero cómo recibir peticiones HTTP, cómo mandar respuestas, etc., Flask ya trae todo eso resuelto — tú solo defines **qué pasa** cuando alguien visita cierta URL.

```python
from flask import Flask
app = Flask(__name__)
```
- `Flask(__name__)` crea la aplicación. `__name__` le dice a Flask en qué archivo está corriendo (para que sepa dónde buscar templates, archivos estáticos, etc.).
- `app` es tu aplicación completa — todo lo demás se construye sobre esta variable.

#### 2. Rutas (`@app.route`)
Ya viste el concepto de decorador, ahora aplicado específicamente a rutas:

```python
@app.route('/')
def home():
    return 'Página principal'
```
Esto significa: cuando alguien visite la URL raíz (`/`), Flask ejecuta `home()` y regresa lo que la función retorne como respuesta HTTP.

#### 3. Parámetros de ruta (variables en la URL)
Puedes capturar partes de la URL como variables, usando `< >`:
```python
@app.route('/saludar/<hi>')
def saludar(hi):
    return f'¡Hola, {hi}!'
```
Si visitas `/saludar/Davo`, Flask toma `"Davo"` y lo pasa como el parámetro `hi` a tu función.

**Tipado de parámetros:** puedes indicarle a Flask qué tipo de dato esperas, para que valide automáticamente:

```python
@taskRoute.route('/<int:id>')
def show(id:int):
    return "Show " + str(id)
```
Aquí `<int:id>` le dice a Flask: "esto debe ser un número entero". Si alguien visita `/abc` (no numérico), Flask automáticamente regresa un error 404, sin que tú tengas que validarlo manualmente. Esto ya lo usaste en tu segundo ejercicio (`tasks/controllers.py`), muy bien aplicado.

#### 4. Templates con Jinja2

En vez de regresar HTML como texto plano dentro de tu función Python (lo cual se vuelve un desastre rápido), Flask te permite separar el HTML en archivos `.html` dentro de una carpeta `templates/`, y usar una plantilla especial llamada **Jinja2** para insertar variables dinámicas.

```python
from flask import render_template

@app.route('/saludo/<n>')
def saludo(n):
    return render_template('view.html', name=n)
```

Y en `view.html`:
```html
<h1>Hola, {{ name }}</h1>
```
`{{ name }}` es sintaxis de Jinja2 — le dice: "aquí pon el valor de la variable `name` que te pasé desde Python". Esto es exactamente lo que hiciste en tu ejercicio con `mi_primer_template`.


#### 5. Archivos estáticos (imágenes, CSS, JS)
Los archivos que no cambian (imágenes, hojas de estilo, JavaScript) van en una carpeta `static/`, y se referencian con `url_for('static', filename='...')`:
```python
url_for('static', filename='img/flask-logo.jpg')
```

Esto genera automáticamente la ruta correcta (típicamente `/static/img/flask-logo.jpg`), sin que tengas que escribirla a mano — útil porque si Flask cambia cómo organiza las rutas internamente, tu código sigue funcionando.






# References

