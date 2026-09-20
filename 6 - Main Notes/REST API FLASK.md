2026-07-09 23:17

Status:

Tags: [[Flask]] [[3 - Tags/Python]]


# REST API FLASK

#### 1. ¿Qué hace diferente una REST API de una app web normal?

Hasta ahora, tus rutas regresaban **texto** o **HTML** (para que un humano lo vea en el navegador). Una **REST API** regresa **datos** (normalmente en formato JSON) para que otro programa los consuma — por ejemplo, una app móvil, un frontend en React, o Postman.

La idea central de REST es: **una misma URL (recurso) puede responder distinto según el método HTTP que uses.**


#### 2. Los métodos HTTP y su propósito (CRUD otra vez, pero en web)

|Método|Propósito|Equivalente CRUD|
|---|---|---|
|**GET**|Obtener datos|Read|
|**POST**|Crear un nuevo dato|Create|
|**PUT**|Actualizar un dato existente|Update|
|**DELETE**|Eliminar un dato|Delete|

Fíjate en el patrón: en vez de tener URLs distintas como `/crear_tarea`, `/borrar_tarea`, en REST usas **la misma URL** (`/tasks` o `/tasks/5`) pero con **métodos distintos**:
```python
@app.route('/tasks', methods=['GET'])       # obtener todas las tareas
def get_tasks():
    ...

@app.route('/tasks', methods=['POST'])      # crear una tarea nueva
def create_task():
    ...

@app.route('/tasks/<int:id>', methods=['PUT'])     # actualizar tarea id
def update_task(id):
    ...

@app.route('/tasks/<int:id>', methods=['DELETE'])  # borrar tarea id
def delete_task(id):
    ...
```

#### 3. Recibir datos del cliente (`request`)

Cuando alguien te manda datos (por ejemplo, en un POST), Flask te los da a través del objeto `request`:

```python
from flask import request

@app.route('/tasks', methods=['POST'])
def create_task():
    data = request.get_json()   # convierte el JSON recibido en un diccionario de Python
    nombre = data['nombre']
    return f"Tarea creada: {nombre}"
```
`request.get_json()` toma el cuerpo JSON que te mandaron (por ejemplo, desde Postman) y lo convierte en un diccionario Python normal — recuerda el Bloque 0, donde vimos que accedes a un diccionario con `data['clave']`.


#### 4. Regresar JSON como respuesta (`jsonify`)

Cuando tu API regresa datos, normalmente los regresas en formato JSON, no como texto plano:

```python
from flask import jsonify

@app.route('/tasks/<int:id>', methods=['GET'])
def get_task(id):
    tarea = {"id": id, "nombre": "Comprar pan"}
    return jsonify(tarea)
```

`jsonify()` toma tu diccionario de Python y lo convierte en una respuesta JSON válida, con el `Content-Type` correcto (`application/json`) — esto es importante porque si solo regresas el diccionario directo, Flask puede no interpretarlo correctamente como JSON en versiones antiguas (en Flask moderno a veces lo hace automático, pero `jsonify` es la forma explícita y segura).


#### 5. Códigos de estado HTTP (normalizar respuestas)

Además de los datos, una API bien hecha regresa un **código de estado** que indica qué pasó:

|Código|Significado|
|---|---|
|200|OK — todo salió bien|
|201|Created — se creó algo nuevo|
|400|Bad Request — el cliente mandó datos mal formados|
|404|Not Found — no existe ese recurso|
|500|Internal Server Error — error del servidor|

Se regresa como una tupla `(respuesta, código)`:

```python
@app.route('/tasks', methods=['POST'])
def create_task():
    data = request.get_json()
    if 'nombre' not in data:
        return jsonify({"error": "Falta el nombre"}), 400
    return jsonify({"mensaje": "Tarea creada", "nombre": data['nombre']}), 201
```


#### 6. Probar con Postman

Postman es una herramienta donde puedes armar peticiones HTTP manualmente (elegir el método, la URL, y el cuerpo JSON) sin tener que construir un frontend — así pruebas tu API directamente. Es justo lo que menciona tu temario en "438. Instalar Postman para realizar peticiones".






# References

