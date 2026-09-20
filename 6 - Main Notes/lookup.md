2026-07-07 07:49

Status:

Tags: [[3 - Tags/MongoDB]]

# lookup

`$lookup` es una etapa de `aggregate` que sirve para **combinar documentos de dos colecciones distintas** — es el equivalente al `JOIN` de SQL.

#### ¿Por qué se necesita?

MongoDB es una base NoSQL, así que normalmente **no hay joins automáticos**. Si tienes datos relacionados en colecciones separadas (como `alimentos` con `grupoId` que apunta a `grupos_alimentos`), `$lookup` te permite "traer" la información relacionada en una sola consulta.

Sintaxis
```js
db.coleccionA.aggregate([
  {
    $lookup: {
      from: "coleccionB",       // colección con la que quieres cruzar
      localField: "campoA",      // campo en coleccionA (la actual)
      foreignField: "campoB",    // campo en coleccionB con el que se compara
      as: "nombreResultado"      // nombre del array donde se guarda el match
    }
  }
])
```

#### Puntos clave

1. **El resultado siempre es un arreglo** — aunque solo haya un match, `as` guarda los documentos coincidentes **dentro de un array**, no como objeto suelto.
2. **No hay error si no hay match** — si no encuentra nada relacionado, simplemente el array queda vacío `[]`, no truena.
3. Frecuentemente se combina con `$unwind` para "aplanar" ese array y quedarte con un documento por cada match (opcional, pero muy común en la práctica — aunque no viene en tu ruta de ejercicios, vale la pena que lo conozcas).

*Ejemplo: traer el grupo completo de cada alimento*
```js
db.alimentos.aggregate([
  {
    $lookup: {
      from: "grupos_alimentos",
      localField: "grupoId",
      foreignField: "_id",
      as: "infoGrupo"
    }
  }
])
```

**Resultado** (para "manzana", por ejemplo):
```js
{
  _id: "manzana",
  nombre: "Manzana",
  grupoId: "frutas",
  calorias: 52,
  unidad: "100g",
  infoGrupo: [
    { _id: "frutas", nombre: "Frutas", descripcion: "Alimentos dulces o ácidos que crecen de plantas" }
  ]
}
```

#### Ejemplo con `recetas` → `alimentos` (relación por array)

Aquí `ingredientes` es un **arreglo de _ids**, no un solo valor. `$lookup` funciona igual — Mongo automáticamente busca coincidencias para **cada elemento del array**:

```js
db.recetas.aggregate([
  {
    $lookup: {
      from: "alimentos",
      localField: "ingredientes",
      foreignField: "_id",
      as: "detalleIngredientes"
    }
  }
])
```


#### Ejemplos

**1. Para cada alimento, trae la info completa de su grupo desde `grupos_alimentos`.**
```js
db.alimentos.aggregate([
  {
    $lookup: {
      from: "grupos_alimentos",
      localField: "grupoId",
      foreignField: "_id",
      as: "infoGrupo"
    }
  }
])
```

**2. Para cada receta, trae el detalle completo de sus ingredientes desde `alimentos`.**
```js
db.recetas.aggregate([
  {
    $lookup: {
      from: "alimentos",
      localField: "ingredientes",
      foreignField: "_id",
      as: "detalleIngredientes"
    }
  }
])
```

**3. Match (porciones > 1) + Lookup de ingredientes.**
```js
db.recetas.aggregate([
  {
    $lookup: {
      from: "alimentos",
      localField: "ingredientes",
      foreignField: "_id",
      as: "detalleIngredientes"
    }
  },
{ $match: { porciones: { $gt: 1 } } }
])
```

**4. Lookup + Project: nombre del alimento y nombre del grupo.**
```js
db.alimentos.aggregate([
  {
    $lookup: {
      from: "grupos_alimentos",
      localField: "grupoId",
      foreignField: "_id",
      as: "infoGrupo"
    }
  },
  { $project: { nombre: 1, "infoGrupo.nombre": 1, _id: 0 } }
])
```




# References
[[aggregate]]
