2026-07-06 16:46

Status:

Tags: [[3 - Tags/MongoDB]]


# Arquitectura de MongoDB

Cluster Básico

![[Pasted image 20260706164646.png]]

El driver conecta la aplicación con el nodo primario. Los datos se replican automáticamente a los nodos secundarios para alta disponibilidad.

*Ventajas de MongoDB*
- Escalable horizontalmente: Se reparte en shards: agregar más nodos aumenta la capacidad sin rediseñar el modelo.
*Flexible y rápido de iterar*:
- Los documentos no requieren un esquema fijo; cambiar la estructura de los datos es simple
*Alta disponibilidad*:
- Los sets de réplicas mantienen copias sincronizadas y toleran caídas de nodos
*Modelo natural en JSON*:
- El mismo formato que ya usan tus APIs y tu código de aplicación.

![[Pasted image 20260706165421.png]]

 



# References

