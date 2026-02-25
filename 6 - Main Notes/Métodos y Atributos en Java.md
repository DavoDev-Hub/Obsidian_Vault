2026-02-24 17:24

Status: #child 

Tags:  [[Java]] [[POO]]


# Métodos en Java

- In Java, the blueprint is called a **Class**.
- A **Class** is a template or a blueprint for creating objects. It defines the structure and behavior that all objects of that class will have.
- An **Object** is an instance of a class. It's a real-world entity created from that blueprint. Just like you can build many houses from one blueprint, you can create many objects from one class.

##### Atributos
Los atributos son los datos o características que posee un objeto. Describen el estado del objeto.
Usando la analogía de la casa: Si "Casa" es una clase, sus atributos podrían ser:
- `numeroDeHabitaciones` (un entero)
- `color` (una cadena, ej. "azul")
- `tieneGaraje` (un booleano, verdadero o falso)
- `direccion` (una cadena)

###### Puntos Clave sobre Atributos:

- **Representación del Estado**: Los atributos contienen los datos que definen el estado actual del objeto. Cada objeto de la misma clase tendrá sus propios valores de atributos, haciéndolos únicos.
- **Declaración**: Los atributos se declaran dentro de una clase pero fuera de cualquier método.

```Java
public class Perro {
    String raza;      // Atributo: raza del perro
    String nombre;    // Atributo: nombre del perro
    int edad;         // Atributo: edad del perro
    String color;     // Atributo: color del perro
}
```

- **Tipos de Datos**: Los atributos pueden ser de cualquier tipo de dato válido en Java (tipos primitivos como `int`, `double`, `boolean`, `char`, o tipos referencia como `String`, `Date`, u otros objetos personalizados).
- **Modificadores de Acceso**: Usualmente verás `private`, `public`, `protected` o acceso por defecto aplicados a atributos.
    - `private`: El más común y recomendado. Solo accesible desde dentro de la clase (refuerza la **encapsulación**).
    - `public`: Accesible desde cualquier lugar (generalmente desaconsejado para atributos).

``` Java
public class Coche {
    private String marca;    // atributo privado
    private String modelo;   // atributo privado
    private int año;         // atributo privado
    public String dueño;     // atributo público (menos común)
}
```


##### Métodos

Los métodos son los **comportamientos** o **acciones** que un objeto puede realizar. Definen qué hace un objeto. Piénsalos como los "verbos" de tu objeto.
Usando la analogía de la casa: Si "Casa" es una clase, sus métodos podrían ser:

- `abrirPuerta()`
- `cerrarVentana()`
- `encenderLuces()`
- `calentar()`

###### Puntos Clave sobre Métodos:

- **Definición de Comportamiento**: Los métodos encapsulan las acciones que puede tomar un objeto, manipulando frecuentemente sus propios atributos o interactuando con otros objetos.
    
- **Declaración**: Los métodos se declaran dentro de una clase y contienen el código que se ejecuta cuando se llama al método.

```Java
public class Perro {
    String raza, nombre;
    int edad;
    String color;
    
    // Método: El perro ladra
    public void ladrar() {
        System.out.println(nombre + " dice ¡Guau! ¡Guau!");
    }
    
    // Método: El perro trae
    public void traer(String objeto) {
        System.out.println(nombre + " trae el " + objeto);
    }
    
    // Método: Obtener edad del perro (devuelve un valor)
    public int obtenerEdad() {
        return edad;
    }
}
```


##### Tipos de Métodos que encontrarás frecuentemente:

1. **Constructores**: Métodos especiales para crear e inicializar nuevos objetos. Tienen el mismo nombre que la clase y no tienen tipo de retorno.

```Java
public class Perro {
    String nombre;
    int edad;
    
    // Constructor
    public Perro(String nombre, int edad) {
        this.nombre = nombre;  // 'this' se refiere al atributo del objeto actual
        this.edad = edad;
    }
}
```

2. **Métodos Getter (Accesores)**: Para obtener el valor de un atributo privado. Convención: `getNombreCampo()`.
```Java
public String getNombre() {
    return nombre;
}
```

3. **Métodos Setter (Mutadores)**: Para modificar el valor de un atributo privado. Convención: `setNombreCampo(nuevoValor)`. Incluyen validación.

```Java
public void setEdad(int nuevaEdad) {
    if (nuevaEdad > 0 && nuevaEdad < 30) {
        this.edad = nuevaEdad;
    } else {
        System.out.println("Edad inválida proporcionada.");
    }
}
```

#### Juntándolo Todo: Clase Carro Completa

```Java
public class Carro {
    // --- Atributos (Estado/Datos) ---
    private String marca;
    private String modelo;
    private int año;
    private String color;
    private double velocidadActual;
    private boolean motorEncendido;
    
    // --- Constructor ---
    public Carro(String marca, String modelo, int año, String color) {
        this.marca = marca;
        this.modelo = modelo;
        this.año = año;
        this.color = color;
        this.velocidadActual = 0.0;
        this.motorEncendido = false;
    }
    
    // --- Getters ---
    public String getMarca() { return marca; }
    public String getModelo() { return modelo; }
    public int getAño() { return año; }
    public String getColor() { return color; }
    public double getVelocidadActual() { return velocidadActual; }
    public boolean estaMotorEncendido() { return motorEncendido; }
    
    // --- Setter ---
    public void setColor(String nuevoColor) {
        System.out.println("Cambiando color de " + this.color + " a " + nuevoColor);
        this.color = nuevoColor;
    }
    
    // --- Métodos de Comportamiento ---
    public void encenderMotor() {
        if (!motorEncendido) {
            motorEncendido = true;
            System.out.println("El motor del " + año + " " + color + " " + 
                             marca + " " + modelo + " está encendido.");
        } else {
            System.out.println("El motor ya está encendido.");
        }
    }
    
    public void acelerar(double incrementoVelocidad) {
        if (motorEncendido) {
            this.velocidadActual += incrementoVelocidad;
            System.out.println("Acelerando. Velocidad actual: " + velocidadActual + " km/h");
        } else {
            System.out.println("No se puede acelerar. El motor no está encendido.");
        }
    }
    
    public void mostrarInfoCarro() {
        System.out.println("--- Información del Carro ---");
        System.out.println("Marca: " + marca);
        System.out.println("Modelo: " + modelo);
        System.out.println("Año: " + año);
        System.out.println("Color: " + color);
        System.out.println("Motor Encendido: " + (motorEncendido ? "Sí" : "No"));
        System.out.println("Velocidad Actual: " + velocidadActual + " km/h");
        System.out.println("-----------------------------");
    }
}

```

# References

[[Clases y Objetos]]