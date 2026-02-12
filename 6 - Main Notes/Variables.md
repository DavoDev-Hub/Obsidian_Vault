2026-02-12 07:28

Status: #child 
Tags: [[Java]] [[JavaBasics]]

# Variables

Java contiene cuatro diferente tipos de variables:

***Variables de instancia***:
También son denominadas variables de instancia, también denominadas campos no estáticos almacenan el estado único de cada objeto de una clase.

```Java
class Bicicleta {
    int currentSpeed = 0;  // Variable de instancia
}
```

***Variables de clase***:
Se declaran con ``static`` y hay una sola copia compartida por todos los objetos de la clase. Son ideales para datos comunes en todas las instancias, como constantes.
``` Java
 class Bicicleta {
    static int numGears = 6;  // Variable de clase
}
```

***Variables Locales***:
Las variables locales se declaran solo dentro de una clase, método, constructor o bloque de código y son solo accesibles en ese ámbito. No requieren ``static`` ni modificadores de acceso y deben inicializarse antes de usarse.
``` Java
public void acelerar(int incremento) {
    int speedTemp = currentSpeed + incremento;  // Variable local
    currentSpeed = speedTemp;
}
```

***Variables Locales***:
Los parámetros son variables pasadas a métodos, constructores o bloques `catch`Siempre se clasifican como variables locales, no como campos, y su alcance es limitado al contexto que los recibe.
```Java
public static void main(String[] args) {  // 'args' es un parámetro
    System.out.println(args[0]);
}
```


##### Notas
* Las variables son ``case-sensitive`` esto quiere decir que las variables son sensibles a mayúsculas/minúsculas y deben empezar con letra (no con `$` ni `_`, aunque técnicamente se permite).
Ejemplo de buen uso de variables:
```Java
int currentSpeed = 0;     // camelCase
static final int NUM_GEARS = 6;  // constante
```


# References
