2026-07-01 18:13

Status:

Tags: [[3 - Tags/MongoDB]]


# Colecciones en MongoDB

**Colecciones**
- Una colección es un conjunto de documentos, desempeña una función análoga a las tablas en las bases de datos relacionales.
- Las colecciones tienen elementos dinámicos, esto quiere decir que puede haber documentos con diferentes estructuras dentro de una colección.
- Por ejemplo los siguientes documentos: `{"edad":34}`, `{"x" : "casa"}` que tienen diferentes claves y diferentes tipos de valores.
- Aunque MongoDB no lo impone como obligatorio, es razonable definir un esquema y agrupar los tipos relacionados en una misma colección.

**Ventajas colecciones con un esquemas relacionados**
- Es más rápido obtener una lista de colecciones que extraer una lista de los tipos de documentos en una colección.
- La agrupación de documentos del mismo tipo juntos en la misma colección permite la localidad de datos.
- Cuando se realiza la creación de índices, se impone cierta estructura a los documentos, logrando una mejor eficiencia por tener documentos de un solo tipo sobre una misma colección.

**Nombres para las colecciones**
Una colección se identifica por su nombre, que es una cadena con las siguientes restricciones:
- Una cadena vacía no es un nombre válido para una colección.
- El nombre de una colección no debe contener el carácter nulo, pues este es utilizado para identificar el final de una colección.
- No se debe empezar ningún nombre de colección con la palabra reservada "system", ya que esta es utilizada para los nombres de colecciones internas. Ej: `system.users`, `system.namespaces`.
- Las colecciones creadas por los usuarios tampoco deben tener el carácter `$` en sus nombres.

# References

