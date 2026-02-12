2026-02-12 08:29

Status:

Tags: [[Java]] [[JavaBasics]]

# Variables de tipo primitivo

Las variables en java son ``statically-typed`` esto quiere decir que las variables primero deben estar declaradas antes de usarse.

```java
int gear = 1;
```

Existen ocho tipos de variables primitivas, las cuales son:
- **byte**: 8 bits, de -128 a 127. Bueno para ahorrar memoria en arrays grandes.
- **short**: 16 bits, de -32,768 a 32,767. Similar a byte, para memoria.
- **int**: 32 bits (por defecto), de -2 mil millones a 2 mil millones aprox.
- **long**: 64 bits, rangos gigantes (agrega "L" al final, como 100L).
- **float**: 32 bits, precisión simple. Usa "F" al final (ej: 3.14F). No para dinero.
- **double**: 64 bits (por defecto), más preciso. Ideal para cálculos decimales generales.
- **boolean**: Solo `true` o `false`. Perfecto para banderas (ej: `isActive`).
- **char**: 16 bits, un carácter Unicode (ej: `'A'` o `\u0041`). De 0 a 65,535.

### Arrays
Los arrays son objetos que contienen un conjunto de valores de un solo tipo. El tamaño del arreglo (en java) se tiene que definir cuando el arreglo es creado.

![[Pasted image 20260212174637.png]]




# References

[[Variables]]
