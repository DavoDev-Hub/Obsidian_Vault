2026-06-30 18:37

Status: #child

Tags: [[3 - Tags/MongoDB]]


# Documentos en MongoDB

MongoDb es una base de datos NoSQL orientado a documentos.
Almacena datos estructurados de forma JSON, bajo un esquema dinámico llamado BSON.
Son la unidad basica de organizacion de la estructura de la informacion en MongoDB, tiene un papel equivalente a una fila en las bases de datos relacionales.
En general un documento contiene multipliers pares clave-valor.

*ejemplo de un documento*
```JSON
	name: "sue",
	age:: 26,
	status: "A",
	groups: [ "news", "sports" ]
```

*Características de documentos*
- Las claves son cadenas, por lo que se permite el uso de cualquier carácter, teniendo un par de excepciones:
		- La clave no puede contener un carácter nulo \0.
		- Deben evitar el uso de punto "." y el dólar "$", pues tiene propiedades especiales.
* MongoDB es sensitivo a mayúsculas y minúsculas, y la los tipos de datos, por ejemplo los siguientes documentos se consideran distintos: {"Edad":3}, {"Edad":"3"}, {"edad":3}, {"edad":"3"}.
* Un documento no puede tener claves duplicadas, esto es incorrecto {"edad":3, "edad":5}.
* Los pares valores están ordenados en los documentos, por ejemplo el documento {"x": 3, "y": 5} no es igual que {"y":5, "x":3}.
* Los valores de un documento pueden ser de diferentes tipos.

*Documento*
```JSON
{
  _id: ObjectId("5099803df3f4948bd2f98391"),
  name: { first: "Alan", last: "Turing" },
  birth: new Date('Jun 23, 1912'),
  death: new Date('Jun 07, 1954'),
  contribs: [ "Turing machine", "Turing test", "Turingery" ],
  views: NumberLong(1250000)
}
```


# References

