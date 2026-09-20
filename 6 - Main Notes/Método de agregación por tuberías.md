2026-07-04 20:47

Status:

Tags:[[3 - Tags/MongoDB]]


# Método de agregación por tuberías

en esta modalidad de procesamiento, los datos son procesados como si atravesaran por unas tuberías, de forma que en cada tubería se aplica una determinada función, y la salida de la misma es la entrada de la siguiente, en la que se realiza otra operación.

La llamada al Framework se realiza mediante la función aggregate, que recibe un array de operaciones separadas por comas

![[Pasted image 20260704204915.png]]

*$project*
Se utiliza para modificar el conjunto de datos de entrada, añadiendo, eliminando o recalculando campos para que la salida sea diferente.

```js
{"$project":{<campo>:1, <campo>:1}}
```


Crear nuevos campos usando los valores de otros campos mediante la notación $ y el nombre 
```js
{"$project":{<campo_nuevo>:"$otro_campo",<campo_nuevo>:<expresion>}}
```

*$match*
Filtra la entrada para reducir el número de documentos, dejando solo los que cumplan las condiciones establecidas

```js
{"$match":<expresion>}
```

*$group*
Agrupa documentos según una determinada condición. Se puede elegir varios campos como identificador.

```js
{"$group":<expresion>}
```

*$sort*
Ordena un conjunto de documentos segun el campo especificado.
```js
{"$sort":<expresion>}
```

*Operadores de agrupación*
Aquellos que se utilizan cuando se agrupan información con $group.
$first, $last, $max, $min, $avg, $sum


*Ejemplo de aplicación de tuberías*
Obtener los documentos de la colección "ordenes" en los que el campo estado sea A, para ello utilice el operador $match

A continuación la salida de la operación anterior (documentos que cumplan con la condición indicada) es ordenada mediante el operador $group, utilizando el campo cliente_id de la salida anterior.




# References

