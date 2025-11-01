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

## Ejercicio 01 - SELECT básico con filtrado y ordenación

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

## Ejercicio 02 - Agregaciones y GROUP BY

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

## Ejercicio 03 - INNER JOIN con múltiples tablas

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

## Ejercicio 04 - Subconsulta no correlacionada en WHERE

**Complejidad:** ⭐⭐ (Media)

Escribe una consulta que muestre el nombre, apellido1 y sueldo de los empleados que ganan más que el sueldo promedio de todos los empleados de la empresa. Ordena por sueldo descendente.

**Columnas resultado:** nombre, apellido1, sueldo

**Pista:** Usa una subconsulta para calcular el sueldo promedio.

Solución:
```sql

```

**Tabla resultado:**

| nombre   | apellido1 | sueldo | 
| -------- | --------- | ------ |
| Eduardo  | Ochoa     | 55000  |
| Juana    | Sáinz     | 43000  |
| Alberto  | Campos    | 40000  |
| Fernando | Ojeda     | 38000  |

---

## Ejercicio 05 - CASE WHEN con agregaciones

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

## Ejercicio 06 - CTE con JOINs y agregaciones

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

## Ejercicio 07 - Subconsulta correlacionada con EXISTS

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

## Ejercicio 08 - LEFT JOIN con COUNT correcto y manejo de NULL

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

## Ejercicio 09 - Consultas combinadas 

**Complejidad:** ⭐⭐ (Baja-Media)

Escribe una consulta que devuelva una lista única de todas las ubicaciones (ciudades) mencionadas en la base de datos, ya sea como:

- Ubicación de un proyecto
- Ubicación de una oficina de departamento (tabla LOCALIZACIONES_DPTO)

Ordena alfabéticamente y elimina duplicados.

**Columna resultado:** ciudad

Solución:
```sql

```


**Tabla resultado:**

| ciudad   | 
| -------- |
| Gijón    |
| Madrid   |
| Sevilla  |
| Valencia |

---

## Ejercicio 10 - Ejercicio integrador

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
| Sede Central        | Eduardo Ochoa     | 0             | 1             |                | Baja carga   |

---

## Resumen de Conceptos Repasados

Esta lección de repaso ha cubierto:

| Ejercicio | Conceptos principales                                                 | Laboratorios | 
| --------- | --------------------------------------------------------------------- | ------------ |
| 01        | SELECT, WHERE, BETWEEN, LIKE, ORDER BY                                | LAB01        |
| 02        | COUNT, SUM, GROUP BY, HAVING                                          | LAB02        |
| 03        | INNER JOIN múltiple, filtrado                                         | LAB05        |
| 04        | Subconsulta no correlacionada, AVG                                    | LAB06        |
| 05        | CASE WHEN, agregaciones, GROUP BY                                     | LAB02        |
| 06        | CTE (WITH), SUM, JOIN, filtrado                                       | LAB04, LAB05 |
| 07        | EXISTS, subconsulta correlacionada, DISTINCT                          | LAB06        |
| 08        | LEFT JOIN, COUNT correcto, manejo NULL                                | LAB05, LAB02 |
| 09        | UNION, eliminación duplicados                                         | LAB04        |
| 10        | Integrador: JOINs, agregaciones, CASE, COALESCE, NULLIF, subconsultas | Todos        |

---

**¡Enhorabuena por completar el repaso!**

Has trabajado con los principales conceptos de SQL vistos en los laboratorios 1-6. Si has podido resolver todos los ejercicios, estás preparado para afrontar problemas SQL más complejos.