	2026-02-25 16:43

Status: #child 

Tags: [[Java]] [[POO]]


# Especificadores de acceso

En programación orientada a objetos, uno de los principios fundamentales es la *encapsulación*. Encapsulación consiste en agrupar datos (atributos o campos) y métodos (funciones). que operan en los datos dentro de una sola unidad (una clase), y restringir el acceso directo de alguno de los componentes de los objectos. Esta restriction es donde los Especificadores de acceso (también llamados Modificadores de acceso) entran.

Son como guardianes de tus clases, métodos y variables. Ellos determinan quien puede acceder a que. Usándolos puedes controlar la visibilidad y accesibilidad de los miembros de tu clase (campos, métodos, constructores, incluso clases enlazadas).

Existen cuatro especificadores de acceso en java:
1. `private`
2. `default (or package-private)`
3. `protected`
4. `public`

##### Private
* *Keyword:* `private`
* *Visibility:* Solo dentro de la misma clase.
*Explicación*: El especificador de acceso `private` es el mas restrictivo. Cuando un miembro (campo, método, constructor) es declarado `private`, es solo accesible desde dentro de la clase donde es declarado. No en otras clases, ni en una subclase o en una clase con el mismo paquete pueden directamente acceder a un miembro `private`.
*¿Por qué usarlo?*
	- `Fuerte Encapsulación`: Aplica una encapsulación estricta ocultado los detalles de implementación interna de una clase. Esto es importante para mantener integridad de los datos y evitar que código externo manipule directamente el estado de un objeto de manera invalida.
	- `Ocultación de datos` : A menudo, los campos son declarados `private` para protegerlos. Para tener controlado el acceso, tu declaras `public` `getter` y `setter` métodos (accessor y mutator métodos) que pueden validar entradas o mejorar otras operaciones antes de modificar los datos.
	- `Métodos de utilidad interna`: Los métodos auxiliares que solo son relevantes para el funcionamiento interno de una clase pueden hacerse `private`

> Ejemplo:

```java
class BankAccount {
    private String accountNumber; // Private field
    private double balance;       // Private field

    public BankAccount(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber;
        this.balance = initialBalance;
    }

    // Public getter method to safely access balance
    public double getBalance() {
        return balance;
    }

    // Public method to deposit
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited " + amount + ". New balance: " + balance);
        } else {
            System.out.println("Deposit amount must be positive.");
        }
    }

    // Public method to withdraw
    public void withdraw(double amount) {
        if (amount > 0 && balance >= amount) {
            balance -= amount;
            System.out.println("Withdrew " + amount + ". New balance: " + balance);
        } else if (amount <= 0) {
            System.out.println("Withdrawal amount must be positive.");
        } else {
            System.out.println("Insufficient funds. Current balance: " + balance);
        }
    }

    private void logTransaction(String type, double amount) {
        // This method is only used internally by BankAccount
        System.out.println("Transaction Log: " + type + " of " + amount + " for account " + accountNumber);
    }
}

class TestBank {
    public static void main(String[] args) {
        BankAccount myAccount = new BankAccount("12345", 1000.0);

        // myAccount.balance = 5000.0; // ERROR: balance has private access
        // System.out.println(myAccount.accountNumber); // ERROR: accountNumber has private access

        System.out.println("Current Balance: " + myAccount.getBalance()); // OK, using public getter
        myAccount.deposit(200); // OK, using public method
        myAccount.withdraw(300); // OK, using public method
        myAccount.withdraw(1500); // OK, using public method, but fails due to check
        // myAccount.logTransaction("CHECK", 50.0); // ERROR: logTransaction has private access
    }
}
```

##### default (or Package-Private)
* *Keyword:* No hay ninguna palabra clave explícita. Si no se especifica ningún especificador de acceso, es `default`. 
* *Visibility:* Solo dentro del mismo paquete 

*Explicación:* Cuando no usas `public`, `private`, `protected`, el miembro automáticamente obtiene `default` (or Package-private) Esto significa que es accesible para alguna otra clase dentro del mismo paquete, pero no para clases fuera del paquete, incluso si estas son subclases.

*¿Por qué usarlo?*
* `Colaboración dentro de un paquete`: Útil cuando tienes un conjunto de clases que trabajan en estrecha colaboración dentro del mismo paquete y necesitan compartir ciertos miembros. Pero desea ocultar estos miembros del resto de la aplicación (otros paquetes).

**Package 1: `com.myapp.model`**
```Java
// File: com/myapp/model/Product.java
package com.myapp.model;

class Product { // This class itself has default access
    String name;         // Default access field
    double price;        // Default access field
    int quantity;        // Default access field

    Product(String name, double price, int quantity) { // Default access constructor
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    void displayDetails() { // Default access method
        System.out.println("Product: " + name + ", Price: $" + price + ", Qty: " + quantity);
    }
}
```

```Java
// File: com/myapp/model/InventoryManager.java
package com.myapp.model;

public class InventoryManager {
    public void addProduct(String name, double price, int quantity) {
        Product newProduct = new Product(name, price, quantity); // OK: Product constructor is default, accessed within same package
        System.out.println("Adding product...");
        newProduct.displayDetails(); // OK: displayDetails method is default, accessed within same package
        newProduct.price = 105.50; // OK: price field is default, accessed within same package
    }
}
```

**Package 2: `com.myapp.app`**
```Java
// File: com/myapp/app/MainApp.java
package com.myapp.app;

import com.myapp.model.InventoryManager;
// import com.myapp.model.Product; // Can't directly import or use Product as it has default access

public class MainApp {
    public static void main(String[] args) {
        InventoryManager manager = new InventoryManager();
        manager.addProduct("Laptop", 1200.0, 5);

        // Product p = new Product("Mouse", 25.0, 10); // ERROR: Product has default access; not visible from another package
        // manager.newProduct.name = "Something"; // ERROR: newProduct is a local variable; Product fields are not directly accessible either
    }
}
```

##### Protected
* *Keyword:* `protected`
* *Visibility:* Dentro del mismo paquete + subclases (incluso en diferentes paquetes)
*Explicación:* El especificador de acceso `protected` orotga un nivel de acceso que esta entre `default` y `public`. Un miembro `protected` es accesible:
	1. Desde cualquier clase dentro del mismo paquete
	2. Desde cualquier subclase, incluso si esa subclase esta en diferente paquete
*¿Por qué usarlo?*
* `Inheritance`: Se utiliza principalmente cuando desea permitir que las subclases accedan o modifiquen ciertos miembros, pero evitar que clases no relacionadas lo hagan.
* `Extension controlada`: Permite una forma controlada para que las subclases extiendan la funcionalidad o el estado de su clase padre.

**Package 1: `com.vehicles`**

```Java
// File: com/vehicles/Vehicle.java
package com.vehicles;

public class Vehicle {
    protected String brand;        // Protected field
    protected double currentSpeed; // Protected field

    public Vehicle(String brand) {
        this.brand = brand;
        this.currentSpeed = 0.0;
    }

    protected void accelerate(double speedIncrease) { // Protected method
        this.currentSpeed += speedIncrease;
        System.out.println(brand + " accelerating. Current speed: " + currentSpeed);
    }

    public void displayBrand() {
        System.out.println("Vehicle brand: " + brand);
    }
}
```

**Package 2: `com.cars`**
```Java
// File: com/cars/Car.java
package com.cars;

import com.vehicles.Vehicle; // Important to import the superclass

public class Car extends Vehicle {
    private int numberOfDoors;

    public Car(String brand, int numberOfDoors) {
        super(brand); // Calls Vehicle's constructor
        this.numberOfDoors = numberOfDoors;
    }

    public void drive() {
        // Accessing protected field 'brand' from superclass (OK, it's a subclass)
        System.out.println(brand + " car is driving with " + numberOfDoors + " doors.");
        
        // Accessing protected method 'accelerate' from superclass (OK, it's a subclass)
        accelerate(50.0); 
    }

    public void honk() {
        System.out.println("Honk honk!");
    }
}
```

**Package 3: `com.app`**

```Java
// File: com/app/MainApplication.java
package com.app;

import com.vehicles.Vehicle;
import com.cars.Car;

public class MainApplication {
    public static void main(String[] args) {
        Car myCar = new Car("Toyota", 4);
        myCar.displayBrand(); // OK, public method
        myCar.drive();        // OK, public method in Car

        // myCar.accelerate(20.0); // ERROR: accelerate has protected access from outside its package and not via inheritance context
        // System.out.println(myCar.brand); // ERROR: brand has protected access from outside its package

        // Let's create a Vehicle instance directly
        Vehicle generalVehicle = new Vehicle("Motorbike");
        // generalVehicle.accelerate(30.0); // ERROR: accelerate has protected access from outside its package
        // System.out.println(generalVehicle.brand); // ERROR: brand has protected access from outside its package
    }
}
```


##### Public

- _Keyword:_ `public`
- _Visibility:_ Accesible desde cualquier lugar (mismo paquete, otros paquetes, subclases, clases no relacionadas).  
    _Explicación:_ El especificador de acceso `public` es el menos restrictivo. Cuando un miembro (campo, método, constructor, clase) es declarado `public`, es accesible desde cualquier clase en cualquier paquete. No hay restricciones de visibilidad.

_¿Por qué usarlo?_

- `Interfaz pública de la clase`: Se usa para exponer la funcionalidad principal de una clase que debe ser utilizada por otras partes de la aplicación.
- `APIs y bibliotecas`: Métodos y clases `public` definen la interfaz pública de tu código, permitiendo que otros programadores lo usen.
- `Puntos de entrada`: Constructores y métodos principales que los usuarios de tu clase necesitan para crear objetos e interactuar con ellos.
    
```Java
// File: com/mycompany/utils/Calculator.java
package com.mycompany.utils;

public class Calculator { // Public class, visible everywhere

    public int add(int a, int b) { // Public method, callable from anywhere
        return a + b;
    }

    public double PI = 3.14159; // Public field (generally discouraged for variables, prefer constants)

    // A private helper method (internal implementation detail)
    private double square(double num) {
        return num * num;
    }

    public double calculateCircleArea(double radius) {
        return PI * square(radius); // Uses private helper method internally
    }
}
```

```Java
// File: com/mycompany/app/MyApp.java
package com.mycompany.app;

import com.mycompany.utils.Calculator; // Import the public class

public class MyApp {
    public static void main(String[] args) {
        Calculator calc = new Calculator();

        int sum = calc.add(10, 5); // OK, public method
        System.out.println("Sum: " + sum);

        System.out.println("Value of PI: " + calc.PI); // OK, public field

        double circleArea = calc.calculateCircleArea(7.0); // OK, public method
        System.out.println("Circle Area: " + circleArea);

        // double squared = calc.square(4.0); // ERROR: square() has private access
    }
}
```

#  References

[[Métodos y Atributos en Java]]
[[Clases y Objetos]]
[[POO Basics]]




