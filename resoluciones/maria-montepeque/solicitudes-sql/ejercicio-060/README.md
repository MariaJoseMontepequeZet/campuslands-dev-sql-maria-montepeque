# Solicitud SQL - Ejercicio 060: Biblioteca Sci-Fi

**Nombre:** Maria Jose Montepeque
**Fecha:** 2026-08-24

## Solicitud del cliente

Una biblioteca especializada presta libros de ciencia ficcion y controla devoluciones. El cliente pidio detectar tres tipos de error: registros repetidos, relaciones invalidas y valores fuera de rango. Ademas queria poder consultar datos, corregir estados, registrar movimientos y sacar reportes utiles (no solo guardar texto).

## Que entendi de la solicitud

A diferencia de una version mas basica de este mismo caso (donde el autor se guardaba como texto dentro del libro), aqui el nivel pedido (3, operaciones controladas) exige separar `autores` en su propia tabla y ademas demostrar `UPDATE` de estado y `DELETE` controlado, no solo `SELECT`/`INSERT`. El detalle completo del analisis (entidades, relaciones, reglas de negocio y supuestos) esta en [analisis/requerimiento.md](analisis/requerimiento.md).

## Que tablas cree y por que

- `autores`: catalogo de autores, evita repetir nombre/nacionalidad en cada libro.
- `libros`: catalogo de libros, cada uno ligado a un autor real (`FOREIGN KEY`).
- `lectores`: catalogo de lectores registrados, con `nombre_lector` y `email` unicos.
- `prestamos`: tabla transaccional. Aqui se detectan y corrigen los tres errores que pidio el cliente:
  - Registros repetidos -> `UNIQUE`.
  - Relaciones invalidas -> `FOREIGN KEY` en cadena (prestamo -> libro -> autor, prestamo -> lector).
  - Valores fuera de rango -> `CHECK` (`fecha_devolucion_esperada > fecha_prestamo`).

## Como se relacionan

`autores` 1:N `libros`, `libros` 1:N `prestamos`, `lectores` 1:N `prestamos`. El diagrama esta en [diagramas/diagrama-er.svg](diagramas/diagrama-er.svg).

## Que datos de prueba use

5 autores, 7 libros, 5 lectores y 11 prestamos, incluyendo a proposito un prestamo duplicado exacto (mismo libro, lector y fecha) para poder demostrar el `DELETE` controlado, y tres `INSERT` comentados que deben fallar (uno por cada tipo de error que pidio detectar el cliente). Detalle en [dml/inserts.sql](dml/inserts.sql).

## Que operaciones de mantenimiento incluyo

En [dml/operaciones.sql](dml/operaciones.sql): dos `UPDATE` de estado (una devolucion y un atraso) y un `DELETE` que elimina duplicados exactos de forma general (conservando siempre el primer registro), en vez de borrar un id fijo.

## Que consultas responden al cliente

En [dql/consultas.sql](dql/consultas.sql): que prestamos existen (JOIN triple libro-autor-lector), que prestamos siguen sin devolverse, que lector tiene mas prestamos, el orden por fecha, y que autor tiene mas prestamos en total (para decidir de cual comprar mas ejemplares).

## Evidencias

Resultados de ejecutar todo en orden, incluyendo la verificacion de los tres casos de error y de las operaciones de mantenimiento, en [evidencias/resultados.md](evidencias/resultados.md).

## Como ejecutar

```bash
sqlite3 ejercicio-060.db < ddl/schema.sql
sqlite3 ejercicio-060.db < dml/inserts.sql
sqlite3 ejercicio-060.db < dml/operaciones.sql
sqlite3 ejercicio-060.db < dql/consultas.sql
```

No suba archivos `.db`, `.sqlite`, `.sqlite3` ni `.dump`.
