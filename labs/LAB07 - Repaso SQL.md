# LAB07 - Repaso SQL

## Objetivos

Repasar los contenidos trabajados en los laboratorios 1 al 6 mediante ejercicios de complejidad creciente:

- SELECT básico, filtrado y ordenación
- Funciones de agregación y agrupación
- JOINs (INNER, LEFT, RIGHT, SELF)
- Subconsultas (correlacionadas y no correlacionadas)
- Consultas combinadas (UNION, INTERSECT, EXCEPT)
- CTEs (WITH)
- Manejo de fechas y valores NULL

En esta sesión trabajaremos con la [BD Empresa completa en SQL Snippets (Empresa BD)](https://i3lab.unex.es/sql-snippets/index.html?db=empresabd).

---

## Ejercicio 01 - Filtrado básico de empleados

**Complejidad:** ⭐ (Baja)

Escribe una consulta que muestre el nombre, apellido1 y sueldo de los empleados cuyo sueldo esté entre 25000 y 40000 euros (ambos incluidos) y cuyo nombre empiece por 'A' o 'J'. Ordena los resultados por sueldo de mayor a menor y, en caso de empate, por nombre alfabéticamente.

**Columnas resultado:** nombre, apellido1, sueldo

Solución:
```sql

```

**Tabla resultado:**

| nombre  | apellido1 | sueldo | 
| ------- | --------- | ------ |
| Alberto | Campos    | 40000  |
| José    | Pérez     | 30000  |
| Alicia  | Jiménez   | 25000  |
| Aurora  | Oliva     | 25000  |

---

## Ejercicio 02 - Costes laborales por departamento

**Complejidad:** ⭐⭐ (Baja-Media)

Escribe una consulta que muestre, para cada departamento, el número de empleados y el sueldo total que paga el departamento (suma de todos los sueldos). Solo incluye departamentos que tengan más de 1 empleado. Ordena los resultados por sueldo total descendente.

**Columnas resultado:** dpto, num_empleados, sueldo_total

**Pista:** Usa funciones de agregación con GROUP BY y HAVING.

Solución:
```sql

```

**Tabla resultado:**

| dpto | num_empleados | sueldo_total | 
| ---- | ------------- | ------------ |
| 5    | 4             | 133000       |
| 4    | 3             | 93000        |

---

## Ejercicio 03 - Proyectos de cada empleado

**Complejidad:** ⭐⭐ (Media)

Escribe una consulta que muestre el nombre y apellido1 del empleado, el nombre del proyecto en el que trabaja y el nombre del departamento que controla ese proyecto. Solo incluye empleados cuyo apellido empiece por 'P'. Ordena por nombre de empleado.

**Columnas resultado:** nombre_empleado, apellido1, nombre_proyecto, nombre_departamento

**Pista:** Necesitarás unir las tablas EMPLEADO, TRABAJA_EN, PROYECTO y DEPARTAMENTO.

Solución:
```sql

```

**Tabla resultado:**

| nombre_empleado | apellido1 | nombre_proyecto | nombre_departamento | 
| --------------- | --------- | --------------- | ------------------- |
| José            | Pérez     | ProductoX       | Investigación       |
| José            | Pérez     | ProductoY       | Investigación       |
| Luis            | Pajares   | Computación     | Administración      |
| Luis            | Pajares   | Comunicaciones  | Administración      |

---

## Ejercicio 04 - Clasificación salarial de empleados

**Complejidad:** ⭐⭐⭐ (Media)

Escribe una consulta que clasifique a los empleados en tres categorías salariales y cuente cuántos empleados hay en cada categoría:

- 'Junior': sueldo menor a 30000
- 'Senior': sueldo entre 30000 y 45000 (ambos incluidos)
- 'Executive': sueldo mayor a 45000

Ordena por número de empleados descendente.

**Columnas resultado:** categoria_salarial, num_empleados

Solución:
```sql

```

**Tabla resultado:**

| categoria_salarial | num_empleados | 
| ------------------ | ------------- |
| Senior             | 4             |
| Junior             | 3             |
| Executive          | 1             |

---

## Ejercicio 05 - Filtrando empleados por horas de trabajo

**Complejidad:** ⭐⭐⭐ (Media-Alta)

Usando una CTE (Common Table Expression), escribe una consulta que:

1. Primero calcule el total de horas trabajadas por cada empleado
2. Luego muestre el nombre y apellido del empleado junto con su total de horas, pero solo para empleados que trabajen más de 30 horas en total

Ordena por total de horas descendente y, luego, alfabéticamente por apellido y nombre.

**Columnas resultado:** nombre, apellido1, total_horas

**Pista:** Define una CTE llamada `horas_empleado` que calcule las horas totales por empleado.

Solución:
```sql

```

**Tabla resultado:**

| nombre   | apellido1 | total_horas | 
| -------- | --------- | ----------- |
| Alberto  | Campos    | 40          |
| Alicia   | Jiménez   | 40          |
| Fernando | Ojeda     | 40          |
| Aurora   | Oliva     | 40          |
| Luis     | Pajares   | 40          |
| José     | Pérez     | 40          |
| Juana    | Sáinz     | 35          |

---

## Ejercicio 06 - Departamentos con empleados multiproyecto

**Complejidad:** ⭐⭐⭐ (Media-Alta)

Escribe una consulta que muestre el nombre de los departamentos que tienen al menos un empleado que trabaja en más de un proyecto. Ordena alfabéticamente.

**Columnas resultado:** nombre_departamento

**Pista:** Usa EXISTS con una subconsulta correlacionada que cuente proyectos por empleado.

Solución:
```sql

```

**Tabla resultado:**

| nombre_departamento | 
| ------------------- |
| Administración      |
| Investigación       |

---

## Ejercicio 07 - Proyectos y familiares por empleado 

**Complejidad:** ⭐⭐⭐ (Media-Alta)

Escribe una consulta que muestre, para cada empleado:

- Su nombre y apellido1
- El número de familiares que tiene registrados (0 si no tiene ninguno)
- El número de proyectos en los que trabaja (0 si no trabaja en ninguno)

Ordena por número de familiares descendente y luego por nombre.

**Columnas resultado:** nombre, apellido1, num_familiares, num_proyectos

**Pista:** Ten cuidado con COUNT(*) en LEFT JOIN. 

Solución:
```sql

```

**Tabla resultado:**

| nombre   | apellido1 | num_familiares | num_proyectos | 
| -------- | --------- | -------------- | ------------- |
| Alberto  | Campos    | 3              | 4             |
| José     | Pérez     | 3              | 2             |
| Juana    | Sáinz     | 1              | 2             |
| Alicia   | Jiménez   | 0              | 2             |
| Aurora   | Oliva     | 0              | 2             |
| Eduardo  | Ochoa     | 0              | 1             |
| Fernando | Ojeda     | 0              | 1             |
| Luis     | Pajares   | 0              | 2             |

---

## Ejercicio 08 - Los empleados invisibles
**Complejidad:** ⭐⭐⭐⭐ (Alta)
 
Escribe una consulta que devuelva el nombre y apellido1 de los empleados que **no supervisan a nadie, no dirigen ningún departamento y no tienen familiares registrados**, ordenados por apellido1. Resuélvelo con una CTE que calcule los `dni` mediante consultas combinadas (`EXCEPT`) y un `JOIN` final para recuperar los nombres.

**Columnas resultado:** nombre, apellido1

**Pregunta adicional:** intenta resolver la parte de "no supervisan a nadie" con `WHERE dni NOT IN (SELECT supervisor FROM EMPLEADO)`. ¿Qué obtienes? ¿Por qué `EXCEPT` no sufre el mismo problema?

**Conceptos:** CTE, EXCEPT encadenado, JOIN, trampa NOT IN + NULL (LAB04, LAB05, LAB06 y parche EXISTS vs IN)

Solución:

```sql

```

  
**Tabla resultado:**

| nombre   | apellido1 |
| -------- | --------- |
| Alicia   | Jiménez   |
| Fernando | Ojeda     |
| Aurora   | Oliva     |
| Luis     | Pajares   |

> **Respuesta a la pregunta adicional:** la versión con `NOT IN` devuelve **0 filas** sin dar error: la columna `supervisor` contiene un NULL (Eduardo no tiene supervisor) y `NOT IN` con un NULL en la lista nunca es verdadero. `EXCEPT`, en cambio, opera con semántica de conjuntos (compara "es el mismo valor", no "es igual"), así que el NULL de la segunda consulta simplemente no coincide con ningún `dni` y no contamina el resultado. Tres formas seguras de expresar "no está en": `EXCEPT`, `NOT EXISTS`, o `NOT IN` saneado con `IS NOT NULL`.

---

## Ejercicio 09 - Informe departamentos completo

**Complejidad:** ⭐⭐⭐⭐ (Alta)

Escribe una consulta que genere un informe completo mostrando:

**Para cada departamento:**

- Nombre del departamento
- Nombre completo del director (nombre y apellido1 separados por espacio)
- Número de empleados (sin contar al director)
- Número de proyectos del departamento
- Promedio de horas trabajadas por empleado en los proyectos del departamento (redondeado a 1 decimal)
- Una columna llamada `estado_carga` que muestre:
    - 'Sobrecargado' si el promedio de horas por empleado es mayor a 30
    - 'Normal' si está entre 15 y 30 (ambos incluidos)
    - 'Baja carga' si es menor a 15

**Requisitos adicionales:**

- Deben aparecer TODOS los departamentos, incluso sin proyectos
- Las horas NULL se tratan como 0
- Si un departamento no tiene horas trabajadas, el promedio debe ser 0
- Ordena por promedio de horas descendente

**Columnas resultado:** nombre_departamento, director_completo, num_empleados, num_proyectos, promedio_horas, estado_carga

Solución:
```sql

```

**Tabla resultado:**

| nombre_departamento | director_completo | num_empleados | num_proyectos | promedio_horas | estado_carga |
| ------------------- | ----------------- | ------------- | ------------- | -------------- | ------------ |
| Administración      | Juana Sáinz       | 2             | 2             | 40             | Sobrecargado |
| Investigación       | Alberto Campos    | 3             | 3             | 40             | Sobrecargado |
| Sede Central        | Eduardo Ochoa     | 0             | 1             | 0              | Baja carga   |

---

## Ejercicio 10 - Calendario de cumpleaños
**Complejidad:** ⭐⭐⭐ (Media-Alta)

El departamento de RRHH quiere organizar las celebraciones de cumpleaños. Escribe una consulta que devuelva, para cada mes del año en el que al menos una persona registrada en la base de datos (empleado **o** familiar) cumpla años, el número de mes como entero y cuántas personas cumplen años ese mes. Ordena por número de personas descendente y, a igualdad, por mes ascendente.

**Columnas resultado:** mes, num_personas

**Pistas:** Combina ambas tablas con una consulta combinada dentro del `FROM` y extrae el mes con `strftime` + `CAST`. Piensa bien qué operador de combinación necesitas: ¿qué pasaría con el recuento si usaras `UNION` en lugar de `UNION ALL`?

**Conceptos:** UNION ALL, subconsulta en FROM, strftime, CAST, GROUP BY, ORDER BY múltiple (LAB02, LAB04, LAB06)
 

Solución:

```sql

```
  
**Tabla resultado:**

| mes | num_personas |
| --- | ------------ |
| 5   | 3            |
| 9   | 2            |
| 12  | 2            |
| 1   | 1            |
| 2   | 1            |
| 3   | 1            |
| 4   | 1            |
| 6   | 1            |
| 7   | 1            |
| 10  | 1            |
| 11  | 1            | 

---

## Ejercicio 11 - Informe de plantilla por sexo
**Complejidad:** ⭐⭐⭐⭐ (Alta)

Escribe una consulta que muestre, para cada departamento: su nombre, el número de hombres, el número de mujeres, el porcentaje de mujeres (redondeado a 1 decimal) y el sueldo medio de las mujeres y de los hombres del departamento. Si un departamento no tiene empleados de un sexo, su sueldo medio debe mostrarse como 0. Ordena por porcentaje de mujeres descendente.

**Columnas resultado:** departamento, hombres, mujeres, pct_mujeres, sueldo_medio_f, sueldo_medio_m

**Pistas:** Necesitas agregaciones condicionales: `filter (where ...)` (o su equivalente con `case` dentro de la agregación). Cuidado con la división entera al calcular el porcentaje y con los NULL que devuelve `avg` cuando el grupo filtrado está vacío.

**Conceptos:** JOIN, FILTER/CASE en agregaciones, COALESCE, ROUND, división entera (LAB02, LAB05)

Solución:

```sql

```

**Tabla resultado:**

| departamento   | hombres | mujeres | pct_mujeres | sueldo_medio_f | sueldo_medio_m |
| -------------- | ------- | ------- | ----------- | -------------- | -------------- |
| Administración | 1       | 2       | 66.7        | 34000          | 25000          | 
| Investigación  | 3       | 1       | 25.0        | 25000          | 36000          |
| Sede Central   | 1       | 0       | 0.0         | 0              | 55000          |

---

## Ejercicio 12 - Parejas salariales (SELF JOIN)
**Complejidad:** ⭐⭐⭐ (Media-Alta)

Escribe una consulta que devuelva las parejas de empleados **distintos** que trabajan en el mismo departamento y cobran exactamente el mismo sueldo. Cada pareja debe aparecer **una sola vez** (si sale A–B, no debe salir B–A) y un empleado no puede emparejarse consigo mismo.

**Columnas resultado:** empleado1, empleado2 (nombre y apellido1 separados por espacio), dpto, sueldo

**Pista:** Necesitas concatenar la tabla EMPLEADO consigo misma con alias distintos. Para eliminar los pares duplicados y el emparejamiento consigo mismo, piensa qué operador de comparación entre los dos `dni` resuelve ambas cosas a la vez.

**Conceptos:** SELF JOIN, alias, condición de deduplicación, concatenación de texto (LAB01, LAB05)

Solución:

```sql

```

**Tabla resultado:**

| empleado1    | empleado2      | dpto | sueldo |
| ------------ | -------------- | ---- | ------ |
| Luis Pajares | Alicia Jiménez | 4    | 25000  |

---

## Ejercicio 13 - Proyectos sin participación femenina
**Complejidad:** ⭐⭐⭐ (Media-Alta)
 
Escribe una consulta que devuelva el nombre de los proyectos en los que **no** trabaja ninguna mujer, ordenados alfabéticamente. Usa `NOT EXISTS` con una subconsulta correlacionada.

**Columna resultado:** nombre_proyecto

**Pista:** la subconsulta necesita concatenar TRABAJA_EN con EMPLEADO para conocer el sexo de quien trabaja en el proyecto de la fila actual.

**Conceptos:** NOT EXISTS, subconsulta correlacionada con JOIN interno, anti-join (LAB05, LAB06)

Solución:
```sql

```

**Tabla resultado:**

| nombre_proyecto |
| --------------- |
| ProductoZ       | 

---

## Ejercicio 14 - Subida salarial selectiva (UPDATE con subconsulta)
**Complejidad:** ⭐⭐⭐⭐ (Alta)

⚠️ **Este ejercicio modifica los datos: hazlo en último lugar o recarga la base de datos después.**

La empresa decide corregir desigualdades internas: sube un 5% (redondeado al entero) el sueldo de todos los empleados que ganan **menos que el sueldo medio de su propio departamento**. Escribe la sentencia `UPDATE` usando una subconsulta correlacionada y comprueba el resultado con `SELECT nombre, apellido1, sueldo FROM EMPLEADO ORDER BY apellido1;`.

**Pista:** la subconsulta debe calcular la media del departamento *del empleado de la fila actual*: necesitas correlacionar con la tabla externa del UPDATE.

**Conceptos:** UPDATE, subconsulta correlacionada, AVG, ROUND (LAB03, LAB06)

Solución:

```sql

```

**Tabla resultado** (tras la comprobación):

| nombre   | apellido1 | sueldo |
| -------- | --------- | ------ |
| Alberto  | Campos    | 40000  |
| Alicia   | Jiménez   | 26250  |
| Eduardo  | Ochoa     | 55000  |
| Fernando | Ojeda     | 38000  |
| Aurora   | Oliva     | 26250  |
| Luis     | Pajares   | 26250  |
| José     | Pérez     | 31500  |
| Juana    | Sáinz     | 43000  |

---

## Resumen de Conceptos Repasados

Esta lección de repaso ha cubierto:

| Ejercicio | Conceptos principales                                                 | Laboratorios |
| --------- | --------------------------------------------------------------------- | ------------ |
| 01        | SELECT, WHERE, BETWEEN, LIKE, ORDER BY                                | LAB01        |
| 02        | COUNT, SUM, GROUP BY, HAVING                                          | LAB02        |
| 03        | INNER JOIN múltiple, filtrado                                         | LAB05        |
| 04        | CASE WHEN, agregaciones, GROUP BY                                     | LAB02        |
| 05        | CTE (WITH), SUM, JOIN, filtrado                                       | LAB04, LAB05 |
| 06        | EXISTS, subconsulta correlacionada, DISTINCT                          | LAB06        |
| 07        | LEFT JOIN, COUNT correcto, manejo NULL                                | LAB05, LAB02 |
| 08        | CTE, EXCEPT, trampa NOT IN + NULL                                     | LAB04, LAB06 |
| 09        | Integrador: JOINs, agregaciones, CASE, COALESCE, NULLIF, subconsultas | Todos        |
| 10        | Fechas (strftime, CAST), UNION ALL, subconsulta en FROM               | LAB04, LAB06 |
| 11        | Agregación condicional (FILTER/CASE), COALESCE, división entera       | LAB02, LAB05 |
| 12        | SELF JOIN, deduplicación de pares                                     | LAB05        |
| 13        | NOT EXISTS, subconsulta correlacionada, anti-join                     | LAB05, LAB06 |
| 14        | UPDATE con subconsulta correlacionada                                 | LAB03, LAB06 |

---

**¡Enhorabuena por completar el repaso!**

Has trabajado con los principales conceptos de SQL vistos en los laboratorios 1-6. Si has podido resolver todos los ejercicios, estás preparado para afrontar problemas SQL más complejos.