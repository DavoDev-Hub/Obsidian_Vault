2026-07-18 20:26

Status:

Tags: [[Angular]]


# Interpolación

La interpolación en Angular es una técnica que permite enlazar datos del componente a la vista (plantilla HTML) de manera dinámica. Utilizando la interpolación, puedes mostrar datos de propiedades del componente dentro de la plantilla de manera directa. La interpolación se realiza mediante la sintaxis {{ ... }}, donde se coloca una expresión de Angular dentro de las llaves dobles.

La interpolación se utiliza principalmente para:

1. Mostrar datos: Puedes mostrar valores de variables, propiedades del componente, o el resultado de una expresión directamente en la vista.
2. Concatenación de cadenas: Puedes concatenar texto estático con valores dinámicos.
3. Evaluación de expresiones simples: Se pueden realizar operaciones simples como aritméticas, lógicas, o llamadas a métodos del componente.

La interpolación es una de las formas más sencillas de enlazar datos en Angular y es unidireccional, lo que significa que los datos fluyen desde el componente hacia la vista.

La sintaxis básica de la interpolación es: {{ expresión }}

Donde expresión puede ser:
Ejemplo Básico Supongamos que tienes un componente app.component.ts con las siguientes propiedades:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  titulo = 'Universidad Angular';

  usuario = {
    nombre: 'Edwin Morales',
    edad: 35
  };

  saludar() {
    return `Hola, ${this.usuario.nombre}`;
  }
}
```

```ts
<section>
  <div class="container">
    <h1 class="text-center text-warning">{{ titulo }}</h1>
    <p>Bienvenido, {{ usuario.nombre }}.</p>
    <p>Tienes {{ usuario.edad }} años.</p>
    <p>{{ saludar() }}</p>
    <p>En un año tendrás {{ usuario.edad + 1 }} años.</p>
  </div>
</section>
```



# References

