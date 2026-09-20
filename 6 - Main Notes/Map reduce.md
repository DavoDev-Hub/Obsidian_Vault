2026-07-05 15:38

Status:

Tags:[[3 - Tags/MongoDB]]


# Map reduce

Framework de agregación creado por Google, y pensada para realizar operaciones de forma paralela sobre grandes colecciones de datos.

La operaciones map-reduce constan de tres pasos

- Etapa map
- Etapa reduce
- Etapa finalize (opcional)

*Etapa map*
Se procesa cada documento y se emite uno o varios objetos por cada documento procesado.

Se implementa mediante una función que mapea los datos de origen, de manera que, para cada dato de origen, se genera una tupla clave-valor, que son unidas en una lista que se pasa a la etapa reduce.

*Etapa reduce*
Etapa reduce en la que se combinan las salidas de la etapa anterior.

Se implementa mediante una función reduce, que trata cada elemento de la lista de pares y realiza operaciones sobre ella para devolver un dato concreto.

*Etapa finalize*
Es una etapa opcional, en la que se permite realizar algunas modificaciones adicionales a las salidas de la etapa reduce.

```js
// Sintaxis

db.runCommand({
    mapReduce: <colección>,
    map: <función>,
    reduce: <función>,
    out: <salida>,
    query: <documento>,
    limit: <número>,
    finalize: <función>,
    scope: <documento>,
    jsMode: <booleano>,
    verbose: <booleano>
})

db.coleccion.mapReduce(mapFunction, reduceFunction, {
    out: <salida>,
    query: <documento>,
    sort: <documento>,
    limit: <número>,
    finalize: <función>,
    scope: <documento>,
    jsMode: <booleano>,
    verbose: <booleano>
})
```


# References

