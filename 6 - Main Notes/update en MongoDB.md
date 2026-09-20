2026-07-06 19:38

Status:

Tags:[[3 - Tags/MongoDB]]


# update en MongoDB

`update` sirve para **modificar documentos existentes**. Es el equivalente al `UPDATE` de SQL.

#### Sintaxis básica

```js
db.coleccion.updateOne(filtro, actualizacion, opciones)
db.coleccion.updateMany(filtro, actualizacion, opciones)
```
- **filtro**: qué documento(s) buscar (igual que en `find`).
- **actualizacion**: qué cambiar, usando **operadores de actualización**.
- **opciones**: cosas como `upsert` (lo vemos en el ejercicio 06).

#### Diferencia clave: `updateOne` vs `updateMany`

- `updateOne` → modifica **solo el primer** documento que coincida con el filtro.
- `updateMany` → modifica **todos** los documentos que coincidan.

#### Operadores de actualización más comunes

1. **`$set`** — cambia el valor de un campo (o lo crea si no existe):
```js
db.alimentos.updateOne(
  { _id: "manzana" },
  { $set: { calorias: 55 } }
)
```

2. **`$inc`** — incrementa (o decrementa) un valor numérico:
```js
db.alimentos.updateOne(
  { _id: "manzana" },
  { $inc: { calorias: 5 } }
)
```

3. **`$unset`** — elimina un campo del documento:

```js
db.alimentos.updateOne(
  { _id: "manzana" },
  { $unset: { calorias: "" } }
)
```

4. **`$push`** — agrega un elemento a un arreglo:

```js
db.recetas.updateOne(
  { _id: "ensalada_frutas" },
  { $push: { ingredientes: "fresa" } }
)
```

5. **`$pull`** — quita un elemento de un arreglo:
```js
db.recetas.updateOne(
  { _id: "ensalada_frutas" },
  { $pull: { ingredientes: "fresa" } }
)
```

#### Errores comunes

- Olvidar el operador (`$set`, `$inc`, etc.) y poner el campo directo → esto **reemplaza todo el documento** por ese campo, borrando lo demás. Siempre usa un operador.
- Confundir `updateOne` con `updateMany` cuando el filtro aplica a varios documentos.

#### El resultado de un update

Regresa un objeto con info como:

```js
{ acknowledged: true, matchedCount: 1, modifiedCount: 1 }
```

#### Ejemplos

**1. Cambia las calorías de "platano" a 95 usando `$set`.**
```js
db.alimentos.updateOne(
  { _id: "platano" },
  { $set: { calorias: 95 } }
)
```

**2. Incrementa en 10 las calorías de "queso_panela" usando `$inc`.**

```js
db.alimentos.updateOne(
  { _id: "platano" },
  { $set: { calorias: 95 } }
)
```

**3. Agrega "espinaca" al arreglo de ingredientes de "ensalada_frutas" usando `$push`.**

```js
db.recetas.updateOne(
  { _id: "ensalada_frutas" },
  { $push: { ingredientes: "espinaca" } }
)
```

**4. Actualiza todas las recetas con `porciones: 1` para que digan `porciones: 2` (updateMany).**

```js
db.recetas.updateMany(
  { porciones: 1 },
  { $set: { porciones: 2 } }
)
```

**5. Elimina el campo `unidad` del alimento "arroz" usando `$unset`.**

```js
db.alimentos.updateOne(
  { _id: "arroz" },
  { $unset: { unidad: "" } }
)
```



# References

