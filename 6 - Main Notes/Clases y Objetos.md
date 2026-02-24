2026-02-24 08:00

Status: #child 

Tags: [[Java]] [[POO]]


# Clases y Objetos

Las clases y objetos son conceptos fundamentales en Java, una programación orientada a objetos que modela problemas reales mediante entidades con estado y comportamiento. Una clase actúa como un plano o prototipo, mientras que un objeto es una instancia concreta de esa clase.

### ¿Qué es una Clase?

Una clase define la estructura de un objeto, incluyendo campos (variables para el estado) y métodos (funciones para el comportamiento). Se crea con la palabra clave `class`, como en `class Bicicleta { ... }`.

Por ejemplo, una clase `Lamp` puede tener un campo `boolean isOn` para el estado de la luz y métodos como `turnOn()` y `turnOff()` para cambiarlo.

### ¿Qué es un Objeto?

Un objeto es una instancia de una clase, creada con `new NombreClase()`. Múltiples objetos pueden crearse de la misma clase, cada uno con su propio estado independiente.

En el ejemplo, `Lamp led = new Lamp();` crea un objeto `led`, y se accede a sus miembros con el operador `.`, como `led.turnOn();`.

### Ejemplo Práctico

```Java
class Lamp {
    boolean isOn;

    void turnOn() {
        isOn = true;
        System.out.println("Light on? " + isOn);
    }

    void turnOff() {
        isOn = false;
        System.out.println("Light on? " + isOn);
    }

    public static void main(String[] args) {
        Lamp led = new Lamp();
        led.turnOn();  // Salida: Light on? true
    }
}
```
Este código crea un objeto dentro de la misma clase y demuestra cómo cada objeto mantiene su estado propio.

### Acceso a Miembros

Usa el punto (`.`) para acceder a campos y métodos: `objeto.campo` o `objeto.metodo()`. Modificadores como `private` y `public` controlan la visibilidad.

# References

[[Métodos en Java]]