c2026-07-06 23:21

Status:

Tags: [[3 - Tags/MongoDB]]


# aggregate

`aggregate` te permite procesar documentos a través de una serie de **etapas** (stages) que se van encadenando, cada una transformando el resultado de la anterior. Piensa en ello como una "línea de producción" de datos.

Sintaxis

```js
db.coleccion.aggregate([
  { etapa1 },
  { etapa2 },
  { etapa3 }
])
```

#### Las 3 etapas más comunes

**`$match`** — filtra documentos (como el filtro de `find()`):
```js
{ $match: { grupoId: "frutas" } }
```

**`$group`** — agrupa documentos por un campo y permite calcular acumulados (sumas, promedios, conteos, etc.). **Siempre necesita un `_id`**, que define por qué campo agrupas:

```js
{ $group: { _id: "$grupoId", totalCalorias: { $sum: "$calorias" } } }
```

Nota el **`$`** antes de `grupoId` y `calorias` dentro del pipeline: así es como referencias el **valor de un campo** (diferente a poner el string plano).

Operadores acumuladores comunes dentro de `$group`:

|Operador|Significado|
|---|---|
|`$sum`|suma valores (o cuenta con `$sum: 1`)|
|`$avg`|promedio|
|`$min` / `$max`|valor mínimo / máximo|
|`$push`|mete los valores en un array|

**`$project`** — como la proyección de `find()`, pero más poderosa (puedes crear campos calculados):

```js
{ $project: { nombre: 1, caloriasPorPorcion: { $divide: ["$calorias", 2] } } }
```

#### Ejemplo completo: total de calorías por grupo

```js
db.alimentos.aggregate([
  { $group: { _id: "$grupoId", totalCalorias: { $sum: "$calorias" } } }
])
```

Resultado:
```js
[
  { _id: "frutas", totalCalorias: 147 },
  { _id: "verduras", totalCalorias: 64 },
  ...
]
```

#### Contar documentos por grupo

```js
db.alimentos.aggregate([
  { $group: { _id: "$grupoId", cantidad: { $sum: 1 } } }
])
```

#### Orden importa

Las etapas se ejecutan **en secuencia**: si pones `$match` después de `$group`, ya no filtras los documentos originales, sino los grupos ya agregados.


#### Ejemplos

**1. Cuenta cuántos alimentos hay por `grupoId`.**
```js
db.alimentos.aggregate([
  { $group: { _id: "$grupoId", cantidad: { $sum: 1 } } }
])
```
**2. Promedio de `calorias` por `grupoId`.**

```js
db.alimentos.aggregate([
  { $group: { _id: "$grupoId", promedioCalorias: { $avg: "$calorias" } } }
])
```

**3. Máximo de calorías por grupo.**
```js
db.alimentos.aggregate([
  { $group: { _id: "$grupoId", maxCalorias: { $max: "$calorias" } } }
])
```

**4. Match (calorias > 50) + Group contando por `grupoId`.**
```js
db.alimentos.aggregate([
  { $match: { calorias: { $gt: 50 } } },
  { $group: { _id: "$grupoId", total: { $sum: 1 } } }
])
```

**5. Project con campo calculado `caloriasPorGramo` (calorias / 100).**

```js
db.alimentos.aggregate([
  { $project: { nombre: 1, _id: 0, caloriasPorGramo: { $divide: ["$calorias", 100] } } }
])
```

**6. Cuenta recetas por `porciones`.**
```js
db.recetas.aggregate([
  { $group: { _id: "$porciones", cantidad: { $sum: 1 } } }
])
```

**7. Promedio de `tiempoPreparacionMin` de todas las recetas (un solo grupo).**
```js
db.recetas.aggregate([
  { $group: { _id: null, promedioTiempo: { $avg: "$tiempoPreparacionMin" } } }
])
```

**8. Match (tiempoPreparacionMin > 10) + Group por `porciones`.**
```js
db.recetas.aggregate([
  { $match: { tiempoPreparacionMin: { $gt: 10 } } },
  { $group: { _id: "$porciones", cantidad: { $sum: 1 } } }
])
```

**9. Group con `$push` — nombres de alimentos por `grupoId`.**
```js
db.alimentos.aggregate([
  { $group: { _id: "$grupoId", alimentosDelGrupo: { $push: "$nombre" } } }
])
```

**10. Project con campo booleano calculado `esAltoEnCalorias`.**

```js
db.alimentos.aggregate([
  { $project: {
      nombre: 1,
      _id: 0,
      esAltoEnCalorias: { $gt: ["$calorias", 100] }
  }}
])
```

**11. Match + Group + Project combinados.**

```js
db.alimentos.aggregate([
  { $match: { calorias: { $gt: 40 } } },
  { $group: { _id: "$grupoId", totalCalorias: { $sum: "$calorias" } } },
  { $project: { grupo: "$_id", totalCalorias: 1, _id: 0 } }
])
```



# References

