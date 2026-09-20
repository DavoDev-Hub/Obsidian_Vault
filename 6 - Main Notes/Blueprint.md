2026-07-09 22:53

Status:

Tags: [[3 - Tags/Python]] [[Flask]]


# Blueprint

#### 1. El problema que resuelve Blueprint
Imagina que tu app crece: tienes rutas para usuarios, rutas para productos, rutas para tareas, etc. Si pones todo en un solo archivo `app.py`, se vuelve gigante e imposible de mantener.

**Blueprint** te permite dividir tu aplicación en módulos — cada uno con sus propias rutas — y luego "pegarlos" todos juntos en la app principal.

#### 2. Cómo se crea un Blueprint

```python
from flask import Blueprint

taskRoute = Blueprint('task', __name__, url_prefix='/task')
```

- `'task'` — el nombre interno del blueprint (Flask lo usa para identificarlo).
- `__name__` — igual que en `Flask(__name__)`, le dice dónde está ubicado este módulo.
- `url_prefix='/task'` — **muy importante**: todas las rutas definidas en este blueprint automáticamente llevan `/task` al inicio.

Entonces, cuando defines:
```python
@taskRoute.route('/')
def index():
    return "Index"
```
La URL real termina siendo `/task/`, no solo `/` — porque el `url_prefix` se agrega automáticamente al inicio.

#### 3. Registrar el Blueprint en la app principal
Crear el blueprint no es suficiente — tienes que decirle a tu app principal que lo use:

```python
from flask import Flask
from my_app.tasks.controllers import taskRoute

app = Flask(__name__)
app.register_blueprint(taskRoute)
```
`register_blueprint()` es el paso que realmente "conecta" esas rutas a la aplicación. Si olvidas este paso, tus rutas del blueprint simplemente no funcionan, aunque estén bien escritas.


#### 4. Ejercicio,

```python
taskRoute = Blueprint('task', __name__, url_prefix='/task')

@taskRoute.route('/')
def index():
    return "Index"

@taskRoute.route('/<int:id>')
def show(id:int):
    return "Show "+ str(id)

@taskRoute.route('/delete/<int:id>')
def delete(id:int):
    return "Delete "+ str(id)

@taskRoute.route('/create', methods=('GET', 'POST'))
def create():
    return "Create"

@taskRoute.route('/update/<int:id>', methods=['GET','POST'])
def update(id:int):
    return "Update "+ str(id)
```

Traducido a URLs reales (recordando el `url_prefix='/task'`):

- `/task/` → `index()`
- `/task/5` → `show(5)`
- `/task/delete/5` → `delete(5)`
- `/task/create` → `create()` (acepta GET y POST)
- `/task/update/5` → `update(5)` (acepta GET y POST)


#### 5. `methods=['GET', 'POST']` — ¿qué hace esto?
Por defecto, una ruta **solo** responde a peticiones **GET** (visitar la URL normalmente, como cuando escribes algo en el navegador). Si quieres que también acepte **POST** (por ejemplo, cuando alguien manda un formulario o datos), tienes que decírselo explícitamente con `methods`.

#### 6. Configuración con clases (`config.py`)

```python
class Config(object):
    pass

class ProdConfig(Config):
    pass

class DevConfig(Config):
    DEBUG = True
```

Esto es un patrón común: tener una configuración base (`Config`), y clases que **heredan** de ella para distintos entornos (desarrollo, producción). `DevConfig` hereda de `Config` y le agrega `DEBUG = True`.


# References

