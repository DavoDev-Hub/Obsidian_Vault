2026-07-06 20:51

Status:

Tags:[[3 - Tags/MongoDB]]


# Upsert

**Upsert** = "Update" + "Insert". Es una opción que le dices a `update` para que, **si no encuentra ningún documento que cumpla el filtro, lo cree** con esos datos en vez de no hacer nada.

```js
db.coleccion.updateOne(
  filtro,
  actualizacion,
  { upsert: true }
)
```

 #### ¿Por qué es útil?

Evita tener que hacer un `find` primero para checar "¿existe o no?" y luego decidir entre `insertOne` o `updateOne`. Con upsert, Mongo lo resuelve en una sola operación.

```js
db.alimentos.updateOne(
  { _id: "nuez" },
  { $set: { nombre: "Nuez", grupoId: "granos", calorias: 654, unidad: "100g" } },
  { upsert: true }
)
```
- Si `"nuez"` **no existe** → la crea con esos campos.
- Si `"nuez"` **ya existe** → la actualiza con `$set`.

#### Detalle importante: cómo se arma el documento nuevo

Cuando Mongo hace el insert (porque no encontró nada), combina:

- Los campos del **filtro** (ej. `_id: "nuez"`)
- Los campos del **`$set`**

Así que el documento resultante tiene ambos.

El resultado incluye `upsertedId`
```js
{
  acknowledged: true,
  matchedCount: 0,
  modifiedCount: 0,
  upsertedId: "nuez"
}
```



#### Ejemplos

**1. Intenta actualizar calorías de un alimento que no existe, "pera" (crea con nombre, grupoId, calorias, unidad).**

```js
db.alimentos.updateOne(
  { _id: "pera" },
  { $set: { nombre: "Pera", grupoId: "frutas", calorias: 57, unidad: "100g" } },
  { upsert: true }
)
```


**2. Upsert sobre "pera" otra vez, cambiando calorias a 60. (¿crea o actualiza?)**

```js
db.alimentos.updateOne(
  { _id: "pera" },
  { $set: { nombre: "Pera", grupoId: "frutas", calorias: 60, unidad: "100g" } },
  { upsert: true }
)
```
_Resultado esperado: actualiza (no crea), porque "pera" ya existe desde el paso 1._

**3. Upsert en `grupos_alimentos` para crear "snacks" (solo si no existe).**

```js
db.grupos_alimentos.updateOne(
  { _id: "snacks" },
  { $set: { nombre: "Snacks", descripcion: "Botanas y colaciones" } },
  { upsert: true }
)
```





# References

