2026-07-06 22:52

Status:

Tags: [[3 - Tags/MongoDB]]


# Distinct

`distinct` regresa los **valores únicos** que existen para un campo específico en una colección, sin repetidos. Es el equivalente al `SELECT DISTINCT` de SQL.

```js
db.coleccion.distinct("campo", filtro)
```

- **campo**: el campo del que quieres los valores únicos (string).
- **filtro** (opcional): igual que en `find`, para limitar sobre qué documentos aplicar el distinct.

#### Puntos clave

1. **Sin filtro** — todos los valores únicos de ese campo en toda la colección:

```js
db.alimentos.distinct("grupoId")
```
Resultado: `["frutas", "verduras", "lacteos", "granos"]` (aunque haya 2 alimentos con `"frutas"`, solo aparece una vez).

2. **Con filtro** — únicos, pero solo considerando los documentos que cumplen el filtro:

```js
db.alimentos.distinct("grupoId", { calorias: { $gt: 50 } })
```

- **Diferencia con `find()`**: `find()` te regresa **documentos completos** (puede haber duplicados de valores en un campo); `distinct()` te regresa un **arreglo simple de valores**, sin duplicados y sin el resto del documento.
- Funciona también con campos dentro de **arrays** — por ejemplo, si quieres saber qué ingredientes distintos se usan en todas las recetas, Mongo "aplana" el array automáticamente:

```js
db.recetas.distinct("ingredientes")
```

#### Ejemplos
**1. Valores únicos de `grupoId` en `alimentos`.**
```js
db.alimentos.distinct("grupoId")
```

**2. Ingredientes únicos usados en todas las recetas.**
```js
db.recetas.distinct("ingredientes")
```

**3. Valores únicos de `unidad` en `alimentos`, solo para calorias > 60.**
```js
db.alimentos.distinct("unidad", { calorias: { $gt: 60 } })
```

**4. Valores únicos de `porciones` en `recetas`.**
```js
db.recetas.distinct("porciones")
```




# References

