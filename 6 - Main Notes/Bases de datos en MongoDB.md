
2026-07-01 18:23

Status:

Tags:[[3 - Tags/MongoDB]]


# Bases de datos en MongoDB

**Base de datos**
Es un grupo de colecciones, o también se podría indicar que las colecciones se agrupan en una base de datos.
Una instancia de MongoDB puede gestionar varias bases de datos, cada una agrupando cero o más colecciones.

**Observaciones:**
- Cada base de datos tiene sus propios permisos y se almacenan en ficheros del disco separados.
- Una buena práctica es almacenar todos los datos de una aplicación en una única base de datos.
- Las bases de datos separadas son útiles cuando se almacenan datos para aplicaciones o usuarios diferentes que utilizan el mismo servidor MongoDB.

**Nombres de una base de datos**
- El nombre de una base de datos en MongoDB no debe tener una cadena vacía.
- Un nombre de una base de datos no puede contener ninguno de estos caracteres: `\ / . " * < >`, espacio o nulo.
- Los nombres son sensitivos a mayúsculas y minúsculas. La mejor práctica es utilizar nombres en minúsculas.
- Los nombres están limitados a un máximo de 64 bytes.
- Existen nombres que no pueden usarse por estar reservados: `admin`, `local`, `configura`.

# References

