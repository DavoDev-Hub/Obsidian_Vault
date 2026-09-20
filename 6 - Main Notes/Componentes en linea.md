2026-07-17 12:35

Status:

Tags:[[Angular]]


# Componentes en linea
Para crear un componente en Angular usando Angular CLI, utilizando plantillas, estilos en línea (inline) y escapando la generación de pruebas, puedes utilizar los siguientes parámetros:
```shell
ng g c componente-inline --inline-template --inline-style --skip-tests
```
Explicación de los parámetros: 
	• `--inline-template`: Inserta el HTML directamente en la propiedad template dentro del archivo TypeScript del componente, en lugar de crear un archivo .html separado. 
	• `--inline-style`: Inserta los estilos CSS directamente en la propiedad styles dentro del archivo TypeScript del componente, en lugar de crear un archivo .css separado. 
	• `--skip-tests: Evita la creación del archivo de pruebas (.spec.ts).
Este comando generará un componente llamado componente-inline con las opciones de HTML y CSS en línea y sin archivo de pruebas.

Se generará un componente con la siguiente estructura en el archivo
`componente-inline.component.ts`:

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-componente-inline',
  standalone: true,
  imports: [],
  template: `
    <h1>{{tituloComponenteInline}}</h1>
    <p>Fuente más grande</p>
  `,
  styles: [
    `
      h1 {
        color: blue;
      }

      p {
        font-size: 20px;
      }
    `,
  ],
})
export class ComponenteInlineComponent {
  tituloComponenteInline = 'Componente Inline';
}
```

Descripción del resultado:
* template: La propiedad template incluye directamente el HTML del componente. En este caso, un título y un párrafo. 
* styles: La propiedad styles contiene los estilos CSS en forma de cadena, aplicados directamente al componente.

No se genera el archivo .html, .css, ni .spec.ts. Todo está contenido en el archivo TypeScript (.ts) del componente.


# References

