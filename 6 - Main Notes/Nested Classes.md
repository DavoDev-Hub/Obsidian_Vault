2026-03-02 18:02

Status:

Tags: [[Java]] [[POO]]


# Nested Classes

Las clases anidadas (nested clases) en java son un mecanismo poderoso para definir una clase dentro del ámbito de otra clase, lo que mejora la encapsulación, reduce la proliferación de archivos fuente y permite un acceso más directo a los miembros de la clase contenedora.

##### Definición y Propósito
Una clase anidada se declara dentro de los corchetes {} de otra clase (llamada clase externa o outer class). Esto es útil cuando la clase interna tiene una relación lógica estrecha con la externa, como un iterador privado para una colección personalizada o un listener especifico par aun componente UI. Existen cuatro tipos principales: *static nested classes, inner classes (no estáticas), local clases y anonymous classes.

*Tipos de clases anidadas*:
1. **Static Nested Classes**: 
Son clases estáticas declaradas dentro de otra clase, similares a métodos estáticos.
* No requieren una instancia de la clase externa para existir o instanciarse
* Solo pueden acceder a miembros estaticos de la clase externa (no a campos de instancia).
* Sintaxis: `static class Nested Class { ... }`
* Instanciación: `Outer.NestedClass obj = new Outer.NestedClass();`
* Uso ideal: Clases auxiliares independientes, como una clase de utilidad empaquetada lógicamente.
```Java
public class Outer {
    private static int staticData = 100;
    
    static class StaticNested {
        void display() {
            System.out.println("Static data: " + staticData);  // OK
        }
    }
}
// Uso: Outer.StaticNested nest = new Outer.StaticNested(); nest.display();
```


##### 2. Inner Classes (Non-Static)

Clases no estáticas que están vinculadas a una instancia específica de la clase externa.

- Acceden directamente a todos los miembros de la externa (públicos, privados, protegidos), incluso sin calificadores `this`.
- Requieren una instancia externa para crearse: `Outer outer = new Outer(); Outer.Inner inner = outer.new Inner();`.
- La clase externa tiene una referencia implícita `Outer.this` accesible en la inner.
- Sintaxis: `class InnerClass { ... }`.
- Ventaja: Encapsulación fuerte, ideal para helpers que necesitan estado de la externa (ej. nodos en un árbol).
```java
public class Outer {
    private int instanceData = 50;
    
    class Inner {
        void display() {
            System.out.println("Instance data: " + instanceData);  // Accede directamente
            System.out.println("Outer ref: " + Outer.this.instanceData);  // Explícito
        }
    }
}
// Uso: Outer outer = new Outer(); Outer.Inner inner = outer.new Inner();
```


##### 3. Local Classes

Definidas dentro de un método, bloque `{}` o expresión lambda (pre-Java 10).

- Ámbito limitado al bloque donde se declaran; no accesibles fuera.
- Pueden capturar variables locales del método (deben ser `effectively final` hasta Java 10, o explícitamente `final` antes).
- Sintaxis: Dentro de un método: `void method() { class LocalClass { ... }; }`.
- Uso: Lógica temporal, como un comparador local en un método de ordenamiento.
```Java
public void processData(int[] data) {
    class DataProcessor {
        void process() {
            for (int value : data) { /* lógica */ }
        }
    }
    new DataProcessor().process();  // Solo aquí
}
```



##### 4. Anonymous Classes

Clases sin nombre, usadas para implementar interfaces o extender clases de forma inline.

- Sintaxis: `new Interface() { @Override public void method() { ... } };`.
- Capturan variables locales efectivamente final.
- Comunes en eventos (ej. `ActionListener`), reemplazadas frecuentemente por lambdas en Java 8+.
- Limitación: Solo un constructor implícito; no pueden ser abstractas.

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println("Ejecutando tarea anónima");
    }
};
```

###### Restricciones y Mejores Prácticas

- **Modificadores**: Inner/local/anonymous no pueden ser `static` (excepto en interfaces pre-Java 8). Static nested sí.
- **Serialización**: Inner classes no son serializables por defecto (mantienen referencia a outer).
- **Herencia**: Una inner puede extender clases o implementar interfaces; anonymous también.
- **Rendimiento**: Ligeramente más overhead por referencias implícitas en non-static.
- **Cuándo usar**: Prefiere static nested para independencia; inner para dependencia de instancia. Usa local/anonymous para código desechable. Evita anidamiento excesivo (>2 niveles).
- **Alternativas modernas**: Lambdas y method references (Java 8+) reemplazan muchos anonymous classes.jenkov+1 

###### Casos de Uso Avanzados

- **Iteradores personalizados**: Inner class como `Iterator` privado en una colección.
- **Event handlers**: Anonymous para listeners en Swing/JavaFX.
- **Builders**: Static nested como `Builder` en patrón Builder.
- **Enums anidados**: Posibles, pero raros.

# References
[[Clases y Objetos]]

