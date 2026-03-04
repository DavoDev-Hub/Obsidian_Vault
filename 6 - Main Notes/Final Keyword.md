2026-03-02 16:33

Status:

Tags: [[Java]] [[POO]]


# Final Keyword

El `final` keyword es un non-access modificador usado para restringir una clase, método, o variable. Es una herramienta poderosa para asegurad inmutabilidad y prevenir modificaciones no intencionadas en tu código.

##### 1. `Final` keyword con variables
Cuando una variable es declarada como `final`, su valor puede ser asignado solo una vez. Después de eso, su valor no puede ser modificado. Esto la convierte en una constante.
* *Syntax*: `final dataType variableName = value;`
* *Key Points*:
	* *Initialization*: Una variable `final` debe ser inicializada. Esto puede ser realizado en el tiempo de la declaración o con un constructor (para variables de instancia) o un static initializer block (para variables estáticas)
	* *No reassignment*: Una vez que una variable `final` tenga un valor, tu no puedes asignar una nueva variable a este. Si lo intentas hacer va a resultar en un *compile-time error.*
	* *Naming Convention*: Por convención, `final` static variables (constantes) son usualmente llamadas en `UPPER_SNAKE_CASE`
*Ejemplo:*
```java
public class FinalVariableExample {
    // A final instance variable (must be initialized in constructor or declaration)
    final int MAX_ATTEMPTS = 3;

    // A final static variable (a true constant)
    public static final double PI = 3.14159;

    final String NAME; // Blank final variable

    public FinalVariableExample(String name) {
        this.NAME = name; // Initialize the blank final variable in the constructor
        // MAX_ATTEMPTS = 5; // COMPILE-TIME ERROR: Cannot reassign final variable
    }

    public void printValues() {
        System.out.println("Max Attempts: " + MAX_ATTEMPTS);
        System.out.println("PI: " + PI);
        System.out.println("Name: " + NAME);
    }

    public static void main(String[] args) {
        FinalVariableExample obj1 = new FinalVariableExample("Alice");
        obj1.printValues();

        FinalVariableExample obj2 = new FinalVariableExample("Bob");
        obj2.printValues();

        // PI = 3.14; // COMPILE-TIME ERROR: Cannot reassign final static variable
    }
}
```

* `final` vs `immutable Objects`:
	* Si una variable `final` refiere a un objeto, significa que la referencia por si misma no puede ser cambiada para que apunte a un diferente objeto.
	* Como sea, el estado del objeto que la referencia `final` apunta puede ser modificada si el objeto por si mismo es mutable.

```Java
import java.util.ArrayList;
import java.util.List;

public class FinalReferenceExample {
    public static void main(String[] args) {
        final List<String> myList = new ArrayList<>();
        myList.add("Apple"); // This is allowed, the object's state is changing
        myList.add("Banana");

        System.out.println("My List: " + myList);

        // myList = new ArrayList<>(); // COMPILE-TIME ERROR: Cannot reassign final variable
    }
}
```

En este ejemplo, `myList` es una referencia `final` a un `ArrayList`. Se pueden agregar elementos al `ArrayList` porque el objeto `ArrayList` es mutable. Simplemente no se puede hacer que `myList` apunte a un nuevo objeto `ArrayList`.

#####  2. `final` Methods

Cuando un método es declarado como `final`, este no  puede ser sobrescrito por una subclase. Esto asegura que la implementación de ese método termina consistente a través de una clase entera heredada.
**Syntax:**
```Java
modifier final returnType methodName(parameters) {
    // method body
}
```

*Puntos a considerar:*
* *preventing overriding*: El propósito principal es prevenir subclases para alterar el comportamiento de un método critico.
* *Security*: Se puede utilizar para evitar que subclases maliciosas alteren la funcionalidad principal de una API o un marco.
```java
class Animal {
    final void eat() {
        System.out.println("Animal is eating.");
    }

    void sleep() {
        System.out.println("Animal is sleeping.");
    }
}

class Dog extends Animal {
    // void eat() { // ERROR: eat() in Dog cannot override eat() in Animal; overridden method is final
    //     System.out.println("Dog is eating kibble.");
    // }

    @Override
    void sleep() { // This is allowed as sleep() is not final
        System.out.println("Dog is sleeping soundly.");
    }
}

public class FinalMethodExample {
    public static void main(String[] args) {
        Dog myDog = new Dog();
        myDog.eat();  // Calls the final eat method from Animal
        myDog.sleep(); // Calls the overridden sleep method from Dog
    }
}
```

##### 3. `final` Classes
Cuando una clase es declarada como `final`, esta no puede ser heredada por alguna otra clase. Esto significa que tu no puedes crear subclases de una clase `final`.
**Syntax:**
```Java
final class ClassName {
    // class members
}
```

*Puntos a considerar:*
* *Preventing Inheritance*: La razón principal para hacer una clase `final` es prevenir extension.
* *Inmutabilidad*: Seguido en conjunto con clases inmutables (como `string`, `Integer`, `Double`, etc). Si una clase es inmutable, haciéndola `final` asegura que su inmutabilidad no puede ser comprometida por  una subclase que posiblemente trate de modificar su estado.
* *Seguridad* : Puede ser usada para crear clases seguras donde la implementación no debe ser alterada o extendida.
* *Performance*: A veces, las clases `final` pueden permitir optimizaciones de rendimiento menores porque el compilador sabe que los métodos no se pueden anular, lo que potencialmente permite la incrustación en línea.

```java
final class ImmutablePoint {
    private final int x;
    private final int y;

    public ImmutablePoint(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    // No setters, ensuring immutability
}

// class ColoredPoint extends ImmutablePoint { // ERROR: cannot inherit from final class ImmutablePoint
//     private String color;
//     public ColoredPoint(int x, int y, String color) {
//         super(x, y);
//         this.color = color;
//     }
// }

public class FinalClassExample {
    public static void main(String[] args) {
        ImmutablePoint p = new ImmutablePoint(10, 20);
        System.out.println("Point coordinates: (" + p.getX() + ", " + p.getY() + ")");

        // Cannot create a subclass of ImmutablePoint
    }
}
```

*Puntos a considerar:*
* *Preventing Inheritance*: La razon principal para hacer una clase `final` es para prevenir alguna forma de extension. Esto puede ser crucial para verios diseños y consideraciones de seguridad.
* *Inmutability*: Las clases `final` son seguido usadas en conjunto con objetos inmutables. Si una clase está diseñada para ser inmutable (su estado no se puede cambiar después de su creación), hacerla final evita la subclasificación maliciosa o accidental que podría introducir un comportamiento mutable y comprometer así la garantía de inmutabilidad.Un excelente ejemplo es la clase String en Java, que es final e inmutable.
	* **Why `final` is important for immutable classes:** If an immutable class were not `final`, a malicious subclass could override its methods to return mutable versions of its internal state, breaking the immutability contract.
*  *Security*: Para clases altamente sensibles (por ejemplo, relacionadas con credenciales de seguridad, transacciones financieras), hacerlas finales evita la modificación no autorizada de su comportamiento a través de la herencia.
* ***Design Integrity:***: Cuando tienes una clase cuya implementación se considera completa, autónoma y no destinada a ser modificada o ampliada, hacerla final comunica claramente esta intención de diseño.
* **Performance (Minor):** De manera similar a los métodos finales, las clases finales permite pequeñas optimizaciones de rendimiento, ya que el compilador sabe que ninguno de sus métodos puede sobrescribirse. Esto elimina la necesidad de realizar búsquedas en tablas de métodos virtuales, lo que potencialmente permite una alineación más agresiva.

* Usa `final` para las variables cuando quieras que sean constantes (su valor no debe cambiar después de la inicialización). Esto es especialmente importante para las variables `public static final`, ya que se convierten en verdaderas constantes globales. En el caso de las variables de referencia, recuerda que `final` solo hace constante la referencia, no el objeto al que apunta.

* Usa `final` en los métodos cuando quieras evitar que las subclases modifiquen el comportamiento o la lógica principal definida en la superclase. Esto garantiza una funcionalidad consistente a lo largo de la jerarquía de herencia.

* Usa `final` en las clases cuando quieras impedir que se extiendan. Esto suele hacerse por razones de seguridad, para reforzar la inmutabilidad o cuando el diseño indica que la clase es una unidad completa y autónoma.


# References

* https://www.baeldung.com/java-final
* https://stackoverflow.com/questions/15655012/how-does-the-final-keyword-in-java-work-i-can-still-modify-an-object



[[POO]]
[[Clases y Objetos]]
[[Static Keyword]]
