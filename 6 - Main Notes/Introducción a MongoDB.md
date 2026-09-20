2026-06-30 14:15

Status: #child 
Tags: [[3 - Tags/MongoDB]]

# Introducción a MongoDB

##### El papel de las bases de datos en las aplicaciones de software

*Usos*:
Se pueden identificar dos usos muy comunes a las bases de datos:

* Como elemento *integrador*: 
		Las bases de datos permiten integrar multiples aplicaciones, generalmente utilizada por diferentes equipos de trabajo, que almacenan sus datos en una base de datos común.
		De esta forma se mejora la comunicación, puesto que todas las aplicaciones operan sobre un conjunto consistente de datos persistentes.
		También tiene una desventaja en relación al uso de una sola base de datos para varias aplicaciones, ya que una sola estructura de datos para varias aplicaciones en el tiempo termina siendo compleja.
 *Muchas empresas utilizan una sola base de datos para muchas aplicaciones, esto puede resultar en algo perjudicial ya que al momento que la base de datos se vuelva más compleja, entonces la gestion de la misma se vuelve compleja*
 
* Como una *aplicación única*:
		Para este caso se tiene una sola base de datos por aplicación, es decir que tenemos un solo equipo que conoce la estructura de la base de datos.
		Dado que existe una única aplicación que interactúa con la base de datos, la integración de ambas puede realizarse en el propio código de la aplicación.
		Cualquier tema de interoperabilidad puede ser gestionado desde las mismas interfaces de la aplicación.

*Cada equipo de una aplicación se concentra en su propia base de datos, es mejor practica*

##### BASES DE DATOS RELACIONALES 
##### Beneficios de las bases de datos relacionales

*Persistencia de datos*:
Este tipo de bases de datos tienen las características de poder mantener cantidades enormes de datos persistentes. Esto a su vez permite acceder de forma fácil y rápida a los datos.

*Concurrencia*:
En el uso de las aplicaciones normales suele haber muchas personas trabajando sobre los mismos datos. Estos incluyen operaciones de actualización sobre los mismos datos al mismo tiempo por diferentes usuarios.

*Integración*:
Con frecuencia se requiere que varias aplicaciones interactúen con una misma base de datos. Una forma de implementar esto es por medio de accesos para compartir la base de datos. De esta forma logramos que varias aplicaciones interactúen con una misma base de datos.

*Modelo estándar*
Las bases de datos relacionales han tenido el éxito que tienen justamente por manejar un solo modelo estándar relacional. Es decir los mecanismos de como acceder e interactuar con los datos se hacen de la misma forma utilizando un lenguaje llamado SQL.

##### Limitaciones de las bases de datos relacionales

*Impedancia*
Uno de los principales problemas de las bases de datos relacionales hace referencia a la diferencia entre el modelo relacional y las estructuras de datos en memoria.

El modelo relacional organiza los datos en forma de tablas y filas (también denominado relaciones y tuplas), todas las operaciones SQL consumen y retornan relaciones de acuerdo al álgebra relacional dando elegancia y simplicidad, pero a su vez una limitación en el uso de memoria.

Los valores en una tupla relacional tienen que ser simples, no pueden tener estructuras, tal como un registro anidado o lista. Esta limitación no se da en las estructura de datos en memoria, donde existe una estructura de datos más rica que las relaciones.

*Cluster*
Un cluster es un conjunto de maquinas que permiten implementar soluciones para escalar aplicaciones.

Desde el punto de vista de las bases de datos relacionales, el cluster se implementa a nivel de disco, es decir depende de un sistema de almacenamiento de alta disponibilidad. Es decir que el cluster es a nivel de la solución de disco mas no a nivel de base de datos.

![[Pasted image 20260630153841.png]]


# References

