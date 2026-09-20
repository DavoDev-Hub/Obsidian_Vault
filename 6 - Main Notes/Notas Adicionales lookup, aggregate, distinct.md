2026-07-07 20:29

Status:

Tags:[[IMongoDB]] [[lookup]] [[Distinct]] [[aggregate]]

# Notas Adicionales lookup, aggregate, distinct

*Aggregate*
`aggregate()` no es "un método más" como `find()`. Es una **tubería (pipeline)**: una fila de estaciones de trabajo, y los documentos van pasando por cada una, transformándose en el camino.

```js
documentos originales → [estación 1] → [estación 2] → [estación 3] → resultadoFinal
```

Cada estación **recibe lo que salió de la anterior**, no los documentos originales. Esto es lo más importante y lo que más confusión causa: si en la estación 2 haces algo, la estación 3 ya no ve los documentos "crudos" de la colección, ve lo que dejó la estación 2.

#### Analogía: una fábrica de jugo

Imagina que tienes una fábrica con:

1. **Estación de selección**: solo dejan pasar naranjas maduras (`$match`)
2. **Estación de exprimido**: agrupan las naranjas por lote y sacan el total de jugo (`$group`)
3. **Estación de empaquetado**: deciden qué etiqueta pegarle a cada caja (`$project`)

Si pones el exprimido antes de la selección, exprimes naranjas podridas también. **El orden de las estaciones cambia el resultado.**

### Ahora, cada operador — qué pregunta responde
En vez de memorizar sintaxis, pregúntate qué necesitas **lograr**, y eso te dice qué operador usar:

|Pregunta que te haces|Operador|
|---|---|
|"¿Cuáles documentos quiero conservar?"|`$match` (filtrar)|
|"¿Cómo quiero juntar/resumir varios documentos en uno?"|`$group`|
|"¿Qué campos quiero mostrar o calcular al final?"|`$project`|
|"¿En qué orden quiero verlos?"|`$sort`|
|"Tengo datos relacionados en otra colección, ¿cómo los traigo?"|`$lookup`|
|"Tengo un array y quiero tratarlo como documentos separados"|`$unwind`|

---
*lookup*
Conceptualmente, `$lookup` hace esto:
> "Por cada documento que tengo aquí, ve a buscar en **otra colección** los documentos donde `[cierto campo de allá]` sea igual a `[cierto campo de aquí]`, y pégamelos en un campo nuevo."

sin código:
> "Por cada **alimento**, ve a la colección de **grupos_alimentos** y busca donde el `_id` del grupo sea igual al `grupoId` de mi alimento. Pega ese resultado en un campo que se llame `infoGrupo`."

Eso, literal, es la traducción de:

```js
{
  $lookup: {
    from: "grupos_alimentos",   // a dónde voy
    localField: "grupoId",      // mi campo (el que ya tengo)
    foreignField: "_id",        // el campo de allá con el que comparo
    as: "infoGrupo"             // dónde guardo el resultado
  }
}
```

**El resultado siempre llega como un arreglo**, aunque haya un solo match — porque técnicamente podría haber más de uno.



#### Ejercicios de los tres juntos

```js
db.recetas.aggregate([
  { "$match": { "porciones": { "$gt": 1 } } },
  { "$lookup": { "from": "alimentos", "localField": "ingredientes",
	"foreignField": "_id", "as": "detalleIngredientes" } },
  { "$project": { "nombre": 1, "detalleIngredientes.nombre": 1, "_id": 0 } }
])
```


# References

