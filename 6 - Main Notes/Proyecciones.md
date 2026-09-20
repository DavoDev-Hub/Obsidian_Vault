2026-07-06 22:09

Status:

Tags:[[3 - Tags/MongoDB]]


# Proyecciones

Una **proyección** controla **qué campos** se muestran en el resultado de un `find()` — es el segundo argumento del método.

*Sintaxis*
```js
db.coleccion.find(filtro, proyeccion)
```

#### Reglas clave

- **`1`** (o `true`) → incluir ese campo.
- **`0`** (o `false`) → excluir ese campo.
- **`_id`** se incluye **siempre por defecto**, aunque no lo pongas — a menos que lo excluyas explícitamente con `_id: 0`.

Regla de oro: no mezclar inclusión y exclusión

No puedes hacer esto:

```js
db.alimentos.find({}, { nombre: 1, calorias: 0 })  // ❌ ERROR
```

**Excepción**: siempre puedes excluir `_id` aunque estés incluyendo otros campos:

```js
db.alimentos.find({}, { nombre: 1, calorias: 1, _id: 0 })  // ✅ válido
```

#### Ejemplos

**Incluir solo ciertos campos:**

```js
db.alimentos.find({}, { nombre: 1, calorias: 1 })
```
Resultado: cada documento trae `_id`, `nombre` y `calorias` (nada más).

**Excluir ciertos campos:**

```js
db.alimentos.find({}, { grupoId: 0 })
```

Resultado: trae todos los campos **excepto** `grupoId


#### Ejemplos

**1. Todos los alimentos, mostrando solo `nombre` y `calorias` (sin `_id`).**
```js
db.alimentos.find({}, { nombre: 1, calorias: 1, _id: 0 })
```

**2. Todas las recetas, mostrando todo excepto `pasos`.**
```js
db.recetas.find({}, { pasos: 0 })
```

**3. Alimentos del grupo "verduras", mostrando solo `nombre` y `unidad`, sin `_id`.**
```js
db.alimentos.find({ grupoId: "verduras" }, { nombre: 1, unidad: 1, _id: 0 })
```

**4. Recetas con `tiempoPreparacionMin` menor a 20, mostrando solo `nombre` y `porciones`.**
```js
db.recetas.find({ tiempoPreparacionMin: { $lt: 20 } }, { nombre: 1, porciones: 1, _id: 0 })
```




# References

