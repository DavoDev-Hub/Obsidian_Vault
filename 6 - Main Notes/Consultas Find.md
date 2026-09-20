2026-07-06 18:22

Status:

Tags: [[3 - Tags/MongoDB]]

# Consultas find

`find()` es el método para **leer/consultar documentos** de una colección. Es el equivalente al `SELECT` de SQL.

```js
db.coleccion.find(filtro, proyeccion)
```
- **filtro**: objeto que describe qué documentos quieres (si lo dejas vacío `{}`, trae todos).
- **proyección**: qué campos quieres mostrar.

#### Puntos clave

1. **Sin filtro** → trae todo:

```js
db.alimentos.find()
```

2. **Filtro por igualdad simple** — el campo debe coincidir exactamente:
```js
db.alimentos.find({ grupoId: "frutas" })
```

3. **Filtrar por `_id`**:
```js
db.alimentos.find({ _id: "manzana" })
```

4. **`findOne()`** — regresa solo el primer documento que coincide (no un cursor):
```js
db.alimentos.findOne({ grupoId: "lacteos" })
```

5. **Filtrar por campo anidado o arrays** — por ejemplo, buscar recetas que usen cierto ingrediente (esto funciona porque Mongo revisa si el valor está _dentro_ del arreglo):

```js
db.recetas.find({ ingredientes: "platano" })
```

- **El resultado es un cursor** — en `mongosh` se imprime automáticamente, pero técnicamente puedes iterarlo con `.forEach()`, `.toArray()`, etc.

6. **`.pretty()`** — para que el resultado se vea formateado (más legible):
```js
db.alimentos.find().pretty()
```


#### Ejemplos

**1. Encuentra todos los alimentos del grupo "verduras".**
```js
db.alimentos.find({ grupoId: "verduras" })
```

**2. Encuentra la receta con `_id: "avena_con_platano"`.**

```js
db.recetas.find({ _id: "avena_con_platano" })
```

**3. Encuentra todos los alimentos cuya `unidad` sea "100g".**

```js
db.alimentos.find({ unidad: "100g" })
```

**4. Encuentra todas las recetas que usen "leche" como ingrediente.**

```js
db.recetas.find({ ingredientes: "leche" })
```

**5. Usa `findOne()` para traer un solo grupo de alimentos (cualquiera).**

```js
db.grupos_alimentos.findOne({ _id: "frutas" })
```



# References

