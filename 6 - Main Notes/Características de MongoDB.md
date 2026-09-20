2026-07-06 16:57

Status:

Tags:[[3 - Tags/MongoDB]]


# Características de MongoDB

*Base de datos*: Contenedor de colecciones. Un servidor puede alojar varias.
*Colección*: Grupo de documentos, sin estructura fija. Equivale a una tabla
*Documento*: Registro de campos nombre/valor. Equivale a una fila
*_id*: Campo obligatorio y único en cada documento: su clave principal

*Estructura de un documento*:

```JSON
{  
"_id": "347237fda4b344234",  
"nombre": "Manzana",  
"grupoId": "frutas",  
"calorias": 52,  
"unidad": "100g"  
}
```


**_id**  
Identificador único del documento (clave principal).

**Campos**  
Pares nombre/valor: nombre, calorías, unidad...

**Referencias**  
`grupoId` conecta este alimento con su grupo — la base del `$lookup`.

# References

