2026-07-07 09:17

Status:

Tags:[[MongoDB]]


# Ejercicios Practicar

###  find

- Encuentra todos los alimentos con `calorias` igual a `89`.
```js
db.alimentos.find({"calorias":89})
```
- Encuentra la receta llamada `"Arroz con verduras"` (usa el campo `nombre`, no `_id`).
```js
db.recetas.find({"nombre":"Arroz con verduras"})
```


### Update

*  Cambia la `descripcion` del grupo `"granos"` a `"Cereales, granos y sus derivados"`.
```js
db.grupos_alimentos.update({"_id":"granos"}, {"$set":{"descripcion":"Creales, granos y sus derivados"}})
```

* Usa `$push` para agregar `"queso_panela"` a los ingredientes de la receta `"arroz_con_verduras"`.
```js
db.recetas.updateOne({"_id":"arroz_con_verduras"},{"$push":{"ingredientes":"queso_panela"}})
```

### Upsert


- Haz upsert sobre el alimento `"aguacate"` (no existe): créalo con `nombre: "Aguacate"`, `grupoId: "frutas"`, `calorias: 160`, `unidad: "100g"`.
```js
db.alimentos.updateOne({"_id":"aguacate"}, {"$set":{"nombre":"Aguacate", "grupoId":"frutas", "calorias": 160, "unidad":"100g"}}, {upsert:true})
```

- Haz upsert sobre la receta `"tacos_pescado"` (no existe): créala con `nombre: "Tacos de pescado"`, `ingredientes: ["arroz"]`, `porciones: 2`.
```js
db.recetas.updateOne({"_id":"tacos_pescado"}, {"$set":{"nombre":"Tacos de pescado", "ingredientes":["arroz"], "porciones":2}}, {upsert:true})
```

### Operadores básicos

* Encuentra alimentos con `calorias` menor o igual a `50`.
```js
db.alimentos.find({"calorias":{"$lte":50}})
```
* Encuentra recetas con `porciones` igual a `2` o `3` (usa `$in`).
```js
db.recetas.find({ "porciones": { "$in": [2, 3] } })
```
* Encuentra alimentos cuyo `grupoId` no sea `"lacteos"` ni `"granos"` (usa `$nin`).
```js
db.alimentos.find({"grupoId":{"$nin":["lacteos","granos"]}})
```


### Proyecciones
* Encuentra todas las recetas mostrando solo `nombre` y `tiempoPreparacionMin`, sin `_id`.
```js
db.recetas.find({}, {"nombre":1, "tiempoPreparacionMin":1, "_id":0})
```
- Encuentra todos los alimentos, mostrando todo excepto `calorias` y `unidad`.
```js
db.alimentos.find({}, {"calorias":0, "unidad":0})
```

### Distinct

* Obtén los valores únicos de `tiempoPreparacionMin` en `recetas`.
```js
db.recetas.distinct("tiempoPreparacionMin")
```
* Obtén los valores únicos de `grupoId` en `alimentos`, pero solo para alimentos cuya `unidad` sea `"100g"`.
```js
db.alimentos.distinct("grupoId", {"unidad":"100g"})
```

### Aggregate

* Cuenta cuántas recetas tiene cada `ingrediente` (pista: agrupa directamente sobre el array `ingredientes`, Mongo lo desglosa solo).
```js
db.recetas.aggregate([
  { "$unwind": "$ingredientes" },
  { "$group": { "_id": "$ingredientes", "cantidad": { "$sum": 1 } } }
])
```
* Calcula el total de `calorias` de todos los alimentos (un solo grupo, `_id: null`).
```js
db.alimentos.aggregate([ { "$group": { "_id": null, "totalCalorias": { "$sum": "$calorias" } } } ])
```
* Usa `$match` (`grupoId: "frutas"` o `"verduras"` con `$in`) + `$group` calculando el promedio de calorías por grupo.
```js
db.alimentos.aggregate([
  { "$match": { "grupoId": { "$in": ["frutas", "verduras"] } } },
  { "$group": { "_id": "$grupoId", "promedioCalorias": { "$avg": "$calorias" } } }
])
```


### Lookup

* Para la receta `"avena_con_platano"` (usa `$match` primero), trae el detalle completo de sus ingredientes con `$lookup`.
```js
db.recetas.aggregate([
  { "$match": { "_id": "avena_con_platano" } },
  {
    "$lookup": {
      "from": "alimentos",
      "localField": "ingredientes",
      "foreignField": "_id",
      "as": "detalleIngredientes"
    }
  }
])
```
* Combina `$lookup` (alimentos → grupos) + `$match` después del lookup, para quedarte solo con alimentos cuyo `infoGrupo.nombre` sea `"Frutas"` (pista: como `infoGrupo` es array, el match sería algo como `{ "infoGrupo.nombre": "Frutas" }`).
```js
db.alimentos.aggregate([
  {
    "$lookup": {
      "from": "grupos_alimentos",
      "localField": "grupoId",
      "foreignField": "_id",
      "as": "infoGrupo"
    }
  },
  { "$match": { "infoGrupo.nombre": "Frutas" } }
])
```


# References

