2026-07-18 20:45

Status:

Tags: [[Angular]]


# Uso de varios componentes Angular

Aquí tienes un ejemplo de cómo usar un componente dentro de otro componente en Angular, utilizando PadreComponent e HijoComponent. En este caso, PadreComponent incluirá varias veces el componente hijo para que veamos cómo podemos relacionar componentes.

1. Primero, creamos el componente PadreComponent, que será un componente standalone y representará al componente padre.
```shell
ng g c padre --skip-tests
```

1. Ahora, creamos el componente HijoComponent, el cual se usará desde la clase PadreComponent. Estamos agregando el componente hijo dentro del componente padre, no es necesario, pero es común organizarlos de esta manera
```shell
ng g c /padre/hijo --skip-tests
```

Relacionar los componentes Padre e Hijo




# References

