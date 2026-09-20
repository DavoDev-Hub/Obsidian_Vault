2026-07-19 03:10

Status:

Tags:[[Angular]]


# Local Reference Angular

## 1. ¿Qué es una Local Reference en Angular?

Una **Local Reference** en Angular es una referencia que se crea directamente en la plantilla HTML y permite acceder a un elemento del DOM (como un `<input>`, un `<button>`, etc.) dentro de la misma plantilla. Esto es útil para interactuar con elementos del DOM sin necesidad de manipularlos directamente desde el código TypeScript del componente.

## 2. Sintaxis Básica

La sintaxis básica para crear una Local Reference es:

html

```html
<elemento #nombreReferencia></elemento>
```

- **`#nombreReferencia`**: Es la Local Reference que crea una referencia local al elemento. Puedes usar este nombre dentro de la misma plantilla para acceder a las propiedades y métodos del elemento.

## 3. Ejemplo Práctico con Local Reference

Este ejemplo permitirá al usuario ingresar una tarea en un campo de texto y mostrar esa tarea en un párrafo cuando se haga clic en un botón.

Creamos el componente `agregar-tarea`:

bash

```bash
ng g c agregar-tarea --skip-tests
```

### 1. Código del Componente (`agregar-tarea.component.ts`)

typescript

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-agregar-tarea',
  standalone: true,
  imports: [],
  templateUrl: './agregar-tarea.component.html',
  styleUrl: './agregar-tarea.component.css'
})
export class AgregarTareaComponent {
  tarea: string = '';

  agregarTarea(nuevaTarea: string): void {
    this.tarea = nuevaTarea;
  }
}
```

### 2. Plantilla del Componente (`agregar-tarea.component.html`)

html

```html
<div class="container mt-5">
  <h2>Agregar Tarea</h2>

  <div class="mt-3 d-flex justify-content-center">
    <input type="text" #tareaInput class="form-control mb-2 w-50"
      placeholder="Escribe una tarea">
  </div>

  <div class="d-flex justify-content-center">
    <button class="btn btn-primary"
      (click)="agregarTarea(tareaInput.value); tareaInput.value=''">
      Agregar Tarea
    </button>
  </div>

  <div class="mt-3 text-center">
    @if (tarea) {
      <p>Tarea: {{ tarea }}</p>
    }
  </div>
</div>
```

### 3. Recordar

Agregar el nuevo componente al componente principal `AppComponent` para poder visualizarlo.

## Explicación del Código

### Componente `agregar-tarea.component.ts`:

- **`tarea: string = '';`**: Esta propiedad almacena el texto de la tarea ingresada.
- **`agregarTarea(nuevaTarea: string): void`**: Este método se invoca cuando el usuario hace clic en el botón "Mostrar Tarea". Asigna el valor de `nuevaTarea` a la propiedad `tarea`.

### Plantilla `agregar-tarea.component.html`:

- **Local Reference `#tareaInput`**: Crea una referencia local al campo de entrada, lo que permite acceder al valor del campo directamente desde la plantilla.
- **Botón con `click`**: Cuando el botón es presionado, se llama al método `agregarTarea`, pasando el valor del campo de entrada. Luego, el campo de entrada se vacía para que el usuario pueda escribir otra tarea.
- **`@if="tarea"`**: Utiliza `@if` para mostrar el párrafo solo si hay una tarea definida, evitando mostrar un mensaje vacío.

## Resultado Esperado

- Cuando el usuario ingrese una tarea en el campo de texto y haga clic en "Mostrar Tarea", la tarea aparecerá debajo del botón.
- El campo de entrada se limpiará automáticamente después de que la tarea se haya mostrado.



# References

