2026-07-06 21:12

Status:

Tags:[[3 - Tags/MongoDB]]


# Operadores de comparación y lógicos

Estos operadores se usan **dentro del filtro** de `find()` o `update()` para hacer consultas más flexibles que una simple igualdad.

#### Operadores de comparación

| Operador | Significado                 | Ejemplo                                         |
| -------- | --------------------------- | ----------------------------------------------- |
| `$gt`    | mayor que (`>`)             | `{ calorias: { $gt: 100 } }`                    |
| `$gte`   | mayor o igual (`>=`)        | `{ calorias: { $gte: 100 } }`                   |
| `$lt`    | menor que (`<`)             | `{ calorias: { $lt: 50 } }`                     |
| `$lte`   | menor o igual (`<=`)        | `{ calorias: { $lte: 50 } }`                    |
| `$ne`    | diferente de (`!=`)         | `{ grupoId: { $ne: "frutas" } }`                |
| `$in`    | está dentro de una lista    | `{ grupoId: { $in: ["frutas", "verduras"] } }`  |
| `$nin`   | NO está dentro de una lista | `{ grupoId: { $nin: ["frutas", "verduras"] } }` |
#### Operadores lógicos

- **`$and`** — todas las condiciones deben cumplirse (Mongo lo asume implícito si pones varios campos, pero a veces se necesita explícito):
```js
db.alimentos.find({ $and: [ { calorias: { $gt: 50 } }, { grupoId: "frutas" } ] })
```
Nota: esto es equivalente a simplemente:
```js
db.alimentos.find({ calorias: { $gt: 50 }, grupoId: "frutas" })
```

`$and` explícito se usa sobre todo cuando necesitas repetir el **mismo campo** con condiciones distintas.

```js
db.alimentos.find({ $or: [ { grupoId: "frutas" }, { grupoId: "lacteos" } ] })
```

 `$exists`

Verifica si un campo **existe** (o no) en el documento, sin importar su valor:

```js
db.alimentos.find({ unidad: { $exists: true } })
db.alimentos.find({ unidad: { $exists: false } })  // útil después de tu $unset en "arroz"
```


#### Ejemplos

**1. Alimentos con más de 100 calorías.**

```js
db.alimentos.find({ calorias: { $gt: 100 } })
```

**2. Alimentos con calorías entre 40 y 100.**
```js
db.alimentos.find({ calorias: { $gte: 40, $lte: 100 } })
```

**3. Alimentos de grupo "frutas" o "lacteos" usando `$in`.**
```js
db.alimentos.find({ grupoId: { $in: ["frutas", "lacteos"] } })
```

**4. Alimentos que no sean de grupo "granos" usando `$ne`.**
```js
db.alimentos.find({ grupoId: { $ne: "granos" } })
```

**5. Alimentos que no tengan el campo `unidad`.**
```js
db.alimentos.find({ unidad: { $exists: false } })
```



# References

