2026-07-19 03:04

Status:

Tags:[[Angular]]


# if-else Angular

### 1. ¿Qué es @if en Angular?

La directiva `@if` es una forma de manejar el flujo condicional en las plantillas de Angular. Permite mostrar u ocultar partes de la interfaz de usuario dependiendo de si una condición es verdadera o falsa.

### 2. Sintaxis Básica

La sintaxis básica de `@if` en Angular es:

html

```html
@if (condición) {
  <!-- Código HTML que se muestra si la condición es verdadera -->
} @else {
  <!-- Código HTML que se muestra si la condición es falsa -->
}
```

## Ejemplo práctico con @if en Angular

Vamos a crear un ejemplo donde se controla el flujo de la interfaz mostrando un mensaje diferente dependiendo del estado de un usuario, por ejemplo, si el usuario está autenticado o no. Creamos un nuevo componente:

bash

```bash
ng g c componente-if --skip-tests
```

### Componente (`componente-if.component.ts`)

typescript

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-componente-if',
  standalone: true,
  imports: [],
  templateUrl: './componente-if.component.html',
  styleUrl: './componente-if.component.css'
})
export class ComponenteIfComponent {
  isAutenticado: boolean = false;

  alternarAutenticacion(): void {
    this.isAutenticado = !this.isAutenticado;
  }
}
```

### Plantilla (`componente-if.component.html`)

html

```html
<div class="container mt-5 text-center">
  <h2>Estatus de Autenticación</h2>

  @if (isAutenticado) {
    <p class="text-success">Estás autenticado</p>
  } @else {
    <p class="text-danger">No estás autenticado</p>
  }

  <button class="btn btn-primary mt-3" (click)="alternarAutenticacion()">
    Alternar Estado de Autenticación
  </button>
</div>
```

### 4. Explicación del Ejemplo

1. **`isAutenticado: boolean`**:
    - Esta propiedad del componente determina si el usuario está autenticado o no. Por defecto, está configurada como `false`.
2. **Directiva `@if`**:
    - `@if (isAutenticado)`: Si `isAutenticado` es verdadero, se mostrará el mensaje "Estás autenticado" en verde.
    - `else`: Si `isAutenticado` es falso, se mostrará el mensaje "No estás autenticado" en rojo.
3. **`alternarAutenticacion()`**:
    - Este método invierte el valor de `isAutenticado`, permitiendo alternar entre los dos estados (autenticado y no autenticado) cuando se presiona el botón.
4. **Botón Alternar Estado de Autenticación**:
    - El botón utiliza event binding para llamar al método `alternarAutenticacion()` cuando se hace clic, lo que alterna el estado de autenticación y actualiza la vista de acuerdo con la condición `@if`.

### 5. Resultado Esperado

Al cargar la aplicación, verás un mensaje que dice "No estás autenticado". Al hacer clic en el botón "Alternar Estado de Autenticado", el mensaje cambiará a "Estás autenticado". Cada vez que hagas clic en el botón, el estado cambiará, demostrando el uso de `@if` para controlar qué parte de la interfaz se muestra en función del estado de la aplicación.



# References

