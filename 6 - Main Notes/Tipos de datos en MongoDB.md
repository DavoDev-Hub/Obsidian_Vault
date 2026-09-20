2026-07-01 17:58

Status:

Tags: [[3 - Tags/MongoDB]]


# Tipos de datos en MongoDB

#### Principales tipos de Datos (MongoDB)

- **Nulo**: representa un valor nulo o un campo que no existe. Ej: `{"x" : null}`
- **Booleanos**: representan datos del tipo verdadero (true) o falso (false). Ej: `{"x" : true}`
- **Números**: se distinguen números reales, Ej: `{"x" : 3.14}`, y números enteros, Ej: `{"x" : 45}`
- **Cadenas**: cualquier cadena de caracteres. Ej: `{"x" : "Ejemplo"}`
- **Fechas**: almacena la fecha en milisegundos. Ej: `{"x" : new Date()}`
- **Expresiones regulares**: se pueden utilizar para realizar consultas.
- **Arrays**: representan un conjunto o lista de valores. Ej: `{"x" : ["a","b","c"]}`
- **Documentos embebidos**: pueden representar a otros documentos, como valores de un documento padre. Ej: `{"x" : {"y" : 45}}`
- **Identificadores de objetos**: es un identificador de 12 bytes para un documento. Ej: `{"x" : ObjectId()}`
- **Datos binarios**: cadena de bytes que no puede ser manipulada directamente.
- **Código JavaScript**: los documentos y consultas pueden contener código JavaScript. Ej: `{"x" : function(){ ... }}`

#### Observaciones

**Fecha**
- Para crear un dato del tipo fecha se utiliza el comando `new Date()`.
- Si se llama sin el `new` (solo `Date()`), se retorna una cadena que representa la fecha, pero no son del mismo tipo que una fecha.
- Cuando utilizamos la SHELL de MongoDB, las fechas se muestran de acuerdo a la zona horaria de la PC, pero la base de datos las almacena sin considerar la zona horaria.
**Array**
- Pueden ser utilizados en operaciones en las que el orden es importante, por ejemplo listas, pilas y colas. También en operaciones donde el orden no es importante, como por ejemplo conjuntos.
- Un Array puede contener diferentes tipos de valores. Ej: `{"x" : ["Ana", 15]}`
- Una propiedad importante de los Arrays en MongoDB es que permite navegar sobre el interior de los Arrays, y de esta manera podemos hacer operaciones sobre sus contenidos.
**Documentos embebidos**
Se suelen utilizar para estructurar los datos de una forma más natural posible. Por ejemplo, si se tiene un documento persona y se quiere almacenar su dirección, esto podría ser otro documento o anidarse al documento principal persona.
```JSON
{
  "nombre": "Juan",
  "dirección": {
    "calle": "Mayor 3",
    "ciudad": "Madrid",
    "País": "España"
  }
}
```

**Identificador de Objetos (_id)**
- El valor de esta clave puede ser de cualquier tipo, pero por defecto es del tipo ObjectID.
- En una colección un documento debe tener un valor único y no repetido para la clave `_id`, lo que asegura que cada documento es único en la colección.
- El tipo ObjectID es el tipo por defecto para los valores asociados a la clave `_id`.
- Cada valor usa 12 bytes, lo que permite representar una cadena de 24 dígitos hexadecimales (2 dígitos por cada byte). Los 12 bytes se generan así:
]

|Byte|0|1|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|Componente|Timestamp|Timestamp|Timestamp|Timestamp|Machine|Machine|Machine|PID|PID|Increment|Increment|Increment|
- **Timestamp**: bytes 0-3
- **Machine**: bytes 4-6
- **PID**: bytes 7-8
- **Increment**: bytes 9-11

# References

