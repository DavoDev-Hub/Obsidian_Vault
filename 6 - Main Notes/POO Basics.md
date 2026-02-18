2026-02-16 17:27

Status: #child 

Tags:[[Java]] [[JavaBasics]]


# POO Basics

Una clase de Java puede contener los siguientes bloques de construcción:
* Campos
* Constructores
* Métodos
* Clases anidadas

	Los campos son variables (data) que son locales a la clase, o instancias (objects) de esa clase. 

	Los constructores son métodos que inicializan una instancia de la clase. Los constructores son métodos que inicializan una instancia de la clase. Establecen valores de los campos de la instancia dada.

	Los métodos son operaciones que la clase o instancia de la clase pueden realizar. Por instancia, un método puede realizar una operación en un parámetro ingresado. O cambiar el valor de los campos guardados internamente en el objeto.

	Las clases anidadas son clases Java que se definen dentro de otra clase. Las clases anidadas generalmente están destinadas a usarse solo internamente como la clase Java que las contiene o en conexión con la clase que las contiene. 


### Definiendo una clase en Java

Todo lo que se necesita para definir una clase es esto:
``` Java
public class MyClass {

}
```
Esto define una clase de Java publica llamada `myclass`. La clase no tiene campos, constructores ni métodos.


##### .java files

La clase de arriba que acabamos de definir debe ser puesta en su propio archivo llamado `MyClass.java` los archivos de Java deben ser llamados como el nombre de la clase que contienen, con el .java (archivo de extensión).

##### Clases con campos

El siguiente ejemplo muestra una clase de java con el modelo de `car`. Enseguida la clase 

``` Java
public class Car {
    public String brand = null;
    public String model = null;
    public String color = null;
}
```

Este código define una clase llamada Car. La clase Car tiene tres campos. No tiene métodos. Solo campo declarados. 

##### Clases con constructores
La clase de Java puede tener un constructor. Un constructor es un método especial que es llamado cuando un objeto de la clase dada es creado.
El propósito del constructor es inicializar los campos en la clase. Los campos también son llamados "estados internos":
```Java
public class Car {

    public String brand = null;
    public String model = null;
    public String color = null;

    public Car() {
    }

    public Car(String theBrand, String theModel, String theColor) {
        this.brand = theBrand;
        this.model = theModel;
        this.color = theColor;
    }

}
```

Los constructores son los dos métodos que tienen el mismo nombre de la clase, y cada uno no tienen un tipo de retorno especifico. El primer constructor toma 


# References

