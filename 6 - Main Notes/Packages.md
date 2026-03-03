2026-03-02 18:31

Status:

Tags: [[Java]] [[POO]]


# Packages

Los paquetes en Java son mecanismos fundamentales para organizar clases y recursos en namespaces jerárquicos, evitando conflictos de nombres y facilitando la modularidad en aplicaciones grandes.

## Concepto Básico

Un paquete es un contenedor lógico para clases relacionadas, representado por un nombre como `java.lang` o `com.example.myapp`. Se declara al inicio de un archivo fuente con `package nombre.paquete;`. Los nombres usan convención de minúsculas con puntos (ej. `java.util`), mapeados a directorios en el filesystem (ej. `java/util/`). Esto permite agrupar código funcionalmente, como `java.io` para entrada/salida.

## Clase java.lang.Package

La clase `Package` (en `java.lang`) representa metadatos de un paquete cargado por un `ClassLoader`. Proporciona información de versión, sellado (sealing) y anotaciones, extraída típicamente del MANIFEST.MF en JARs. Cada `ClassLoader` mantiene un objeto `Package` único por nombre de paquete para clases cargadas por él.[asjordi](https://asjordi.dev/blog/nested-classes-en-java/)​

## Métodos Principales

- **getName()**: Devuelve el nombre del paquete, ej. `"java.lang"`.
    
- **Versiones de especificación**: `getSpecificationTitle()`, `getSpecificationVersion()`, `getSpecificationVendor()` – Identifican la spec implementada (ej. "Java Platform API Specification" versión "1.8").
- **Versiones de implementación**: `getImplementationTitle()`, `getImplementationVersion()`, `getImplementationVendor()` – Detalles del proveedor específico (vendor-specific, sin sintaxis fija).
- **isSealed() / isSealed(URL url)**: Verifica si el paquete está sellado (protegido contra adiciones de clases desde ciertas URLs, para integridad).
- **isCompatibleWith(String desired)**: Compara versiones de spec (formato "1.2.3" con enteros separados por puntos; compara secuencialmente hasta diferencia).
- **getPackage(String name) / getPackages()**: Obtiene `Package` por nombre o lista todos conocidos por el `ClassLoader` actual.
- **Métodos de anotaciones** (heredados de `AnnotatedElement`): `getAnnotation()`, `getAnnotations()`, etc., para inspeccionar anotaciones en el paquete.[asjordi](https://asjordi.dev/blog/nested-classes-en-java/)​

|Categoría|Métodos Clave|Propósito|
|---|---|---|
|Nombre|`getName()`|Identificador único|
|Spec|`getSpecification*()`|Versión estándar (ej. Java SE)|
|Impl|`getImplementation*()`|Detalles del proveedor (ej. Oracle)|
|Sellado|`isSealed()`, `isSealed(URL)`|Seguridad contra modificaciones|
|Compatibilidad|`isCompatibleWith(String)`|Verificar versiones deseadas|
|Listado|`getPackage()`, `getPackages()`|Acceso runtime|

## Cómo Funcionan los Paquetes

- **Estructura**: `package com.miapp.modelo;` → Archivo en `src/com/miapp/modelo/MiClase.java`.
    
- **Imports**: `import java.util.List;` o `import static java.lang.Math.PI;` para usar clases sin calificar.
    
- **ClassLoader**: Carga clases por paquete completo; un paquete por `ClassLoader` es único.
    
- **Sellado**: En JARs, `Manifest-Version: 1.0\nSealed: true` sella el paquete desde la URL base.
    
- **Versiones**: Sintaxis de spec: "Digits.RefinedVersion" (ej. "1.8.0_XXX"); comparación lexicográfica numérica.

## Ejemplos Prácticos

## Declaración y Uso

```Java
// MiClase.java
package com.example.model;

public class MiClase {
    // ...
}

// En otro paquete:
package com.example.control;
import com.example.model.MiClase;  // O fully qualified: new com.example.model.MiClase();

```

```Java
Package pkg = Package.getPackage("java.lang");
System.out.println("Nombre: " + pkg.getName());  // "java.lang"
System.out.println("Spec Version: " + pkg.getSpecificationVersion());  // ej. "1.8"
System.out.println("Sealed: " + pkg.isSealed());  // true/false

// Verificar compatibilidad
if (pkg.isCompatibleWith("1.8")) {
    System.out.println("Compatible");
}
```

## Mejores Prácticas y Restricciones

- **Convenciones**: Paquetes en minúsculas, estructura inversa de dominio (ej. `com.empresa.proyecto.modulo`).
    
- **Default Package**: Sin `package` declara en paquete por defecto (evitar en proyectos grandes).
    
- **Colisiones**: Paquetes únicos evitan conflictos; no hay herencia de paquetes.
    
- **Módulos (Java 9+)**: Evolución con `module-info.java`, pero `Package` sigue para metadatos legacy.
    
- **Uso avanzado**: Reflexión para logging de versiones en apps enterprise; verificación de compatibilidad en plugins.
    
- **Limitaciones**: Versiones solo si definidas en manifest; `hashCode()` basado solo en nombre; `toString()` incluye detalles completos.



# References

