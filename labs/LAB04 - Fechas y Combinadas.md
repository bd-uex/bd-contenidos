# Fechas y Consultas Combinadas

---

## Objetivos

Los objetivos de aprendizaje de la sesión son:

1. Fechas y horas
2. Consultas combinadas (teoría conjuntos): 
	- unión
	- intersección 
	- resta
3. Common Table Expressions (CTE): cláusula WITH

En esta sesión trabajaremos con la [BD Empresa completa en SQL Snippets (Empresa BDE)](https://i3lab.unex.es/sql-snippets/index.html?db=empresabd).

---

## Ejercicio 01 - Repaso SELECT

Escribe una consulta que devuelva el sueldo medio por departamento para departamentos que tengan más de 1 empleado.

Solución:
```sql

```

Resultado:

| dpto | avg(sueldo) |
| ---- | ----------- |
| 4    | 31000       | 
| 5    | 33250       |

---

## Funciones estándar para hora y fecha

```sql
select 
	CURRENT_DATE, 
	CURRENT_TIME, 
	CURRENT_TIMESTAMP;
```

Tabla resultado:

| CURRENT_DATE | CURRENT_TIME | CURRENT_TIMESTAMP   |
| ------------ | ------------ | ------------------- |
| 2025-10-03   | 15:57:05     | 2025-10-03 15:57:05 |

**¿Qué hora es?**

SQL tiene tres funciones para responder a esta pregunta:

- `CURRENT_TIME` devuelve la hora actual
- `CURRENT_DATE` devuelve la fecha actual
- `CURRENT_TIMESTAMP` devuelve la fecha y la hora actual

---

## Tipo de datos fechas y horas

```sql
-- cambiar la fecha de ingreso
update departamento
set fechaIngresoDirector = datetime('now')
where numero=5;

--insertar un nuevo departamento
insert into departamento values
('Ventas', 6, '453453453', '2024-08-15 12:00:00');

select * from departamento;
```

Tabla resultado:

| nombre         | numero | director  | fechaIngresoDirector |
| -------------- | ------ | --------- | -------------------- |
| Sede Central   | 1      | 888665555 | 1981-06-19           |
| Administración | 4      | 987654321 | 1995-01-01           |
| Investigación  | 5      | 333445555 | 2025-10-03 15:39:08  | 
| Ventas         | 6      | 453453453 | 2024-08-15 12:00:00  |


SQLite no posee un tipo de datos específico para fechas y horas. Otros SGBD tienen tipo de datos específicos.

Utiliza **funciones** para almacenar la fecha y hora como TEXT, REAL o INTEGER.

- **datetime()**: devuelve TEXT como cadenas ISO8601 ("AAAA-MM-DD HH:MM:SS.SSS").
- **julianday()**: REAL como números de días julianos, la cantidad de días desde el mediodía en Greenwich el 24 de noviembre de 4714 a. C. según el calendario gregoriano proléptico.
- **unixepoch()**: INTEGER como hora Unix, la cantidad de segundos desde el 1 de enero de 1970 a las 00:00:00 UTC.

---

## Formato de fechas y horas

```sql
select 
   nombre, 
   director, 
   datetime(fechaIngresoDirector,'localtime') as local_datetime 
from departamento;
```

Tabla resultado:

| nombre         | director  | local_datetime      | 
| -------------- | --------- | ------------------- |
| Sede Central   | 888665555 | 1981-06-19 02:00:00 |
| Administración | 987654321 | 1995-01-01 01:00:00 |
| Investigación  | 333445555 | 2025-10-03 19:42:24 |
| Ventas         | 453453453 | 2024-08-15 14:00:00 |

La función datetime convierte valores de fechas en cadenas de texto según el estándar ISO8601 ("AAAA-MM-DD HH:MM:SS.SSS").

- Devuelve el valor de fecha y hora en UTC
- Para convertirlo en tiempo local usa el modificador `‘localtime’`.

---

## Separando fechas y horas

```sql

```

Tabla resultado:

| nombre         | director  | local_date | local_time | 
| -------------- | --------- | ---------- | ---------- |
| Sede Central   | 888665555 | 1981-06-19 | 02:00:00   |
| Administración | 987654321 | 1995-01-01 | 01:00:00   |
| Investigación  | 333445555 | 2025-10-03 | 19:42:24   |
| Ventas         | 453453453 | 2024-08-15 | 14:00:00   |

- La función `date` devuelve la fecha de un valor que almacena fecha y hora (`datetime`).
- La función `time` devuelve la hora de un  que almacena fecha y hora (`datetime`).

---

## Ejercicio 02 - Fecha y hora

Escribe una consulta para obtener el nombre del departamento, su director, su fecha de ingreso y la hora en horario local del director más antiguo de un departamento.

Solución:
```sql
select 
  nombre, 
  director, 
  min(fechaIngresoDirector) as mas_antiguo,
  time(fechaIngresoDirector,'localtime') as hora
from departamento;
```

Tabla resultado:

| nombre       | director  | mas_antiguo | hora     |
| ------------ | --------- | ----------- | -------- |
| Sede Central | 888665555 | 1981-06-19  | 02:00:00 | 

---

## Intervalos de fechas y horas

```sql
select 
   nombre, 
   director, 
   date(fechaIngresoDirector,'localtime') as local_date,
   time(fechaIngresoDirector,'localtime') as local_time 
from departamento
where date(fechaIngresoDirector,'localtime') between date('now','-1 year','-6 months') and date('now');
```

Tabla resultado:

| nombre        | director  | local_date | local_time |
| ------------- | --------- | ---------- | ---------- |
| Investigación | 333445555 | 2025-10-03 | 19:42:24   |
| Ventas        | 453453453 | 2024-08-15 | 14:00:00   | 

Es posible comparar fechas con los operadores de comparación y otros operadores.

Las funciones de tiempo admiten **modificadores** para obtener fácilmente otras fechas a partir de una dada. P.ej. la fecha correspondiente a hace un año mediante el modificador `‘-1 year’`.

Otros modificadores disponibles:

- NNN days
- NNN hours
- NNN minutes
- NNN seconds
- NNN years
- start of month
- start of year
- start of day
- weekday N

Puedes consultar la [lista completa de modificadores](https://www.sqlite.org/lang_datefunc.html#modifiers) en la documentación de referencia de SQLite.

---

## Otros modificadores de fechas y horas

```sql
-- Devuelve el úlitmo día del mes a partir de la fecha dada

SELECT date('2024-02-01','start of month','+1 month','-1 day') as last_day;
```

Tabla resultado:

| last_day   |
| ---------- |
| 2024-02-29 | 

Otros modificadores disponibles:

- start of month
- start of year
- start of day
- weekday N

Los modificadores `start of` retroceden la fecha al inicio del mes, año o día en cuestión.

El modificador `weekday` adelanta la fecha, si es necesario, a la siguiente fecha cuyo número de día laborable sea N. El domingo es 0, el lunes es 1, y así sucesivamente. Si la fecha ya corresponde al día laborable deseado, el modificador `weekday` la deja sin cambios.

Puedes consultar la [lista completa de modificadores](https://www.sqlite.org/lang_datefunc.html#modifiers) en la documentación de referencia de SQLite.

---

## Ejercicio 03 - Intervalos

Escribe una consulta para obtener el nombre, apellido1 y fechaNac de los empleados que tengan entre 60 y 70 años.

Solución:
```sql

```

Tabla resultado:

| nombre   | apellido1 | fechaNac   |
| -------- | --------- | ---------- |
| José     | Pérez     | 1965-09-01 |
| Alberto  | Campos    | 1955-12-08 | 
| Fernando | Ojeda     | 1962-09-15 |

---

## Extraer partes de fechas y horas

```sql
select 
   nombre, 
   director, 
   fechaIngresoDirector as fecha,
   strftime('%Y',fechaIngresoDirector) as year,
   strftime('%m',fechaIngresoDirector) as month,
   strftime('%d',fechaIngresoDirector) as day
from departamento;
```

Tabla resultado:

| nombre         | director  | fecha               | year | month | day |
| -------------- | --------- | ------------------- | ---- | ----- | --- |
| Sede Central   | 888665555 | 1981-06-19          | 1981 | 06    | 19  |
| Administración | 987654321 | 1995-01-01          | 1995 | 01    | 01  | 
| Investigación  | 333445555 | 2025-10-03 17:42:24 | 2025 | 10    | 03  |
| Ventas         | 453453453 | 2024-08-15 12:00:00 | 2024 | 08    | 15  |

La función `strftime()` devuelve la fecha formateada según la cadena de formato especificada como primer argumento. Si no es posible realizar el formateo, devuelve `NULL`.

Puedes consultar la [lista completa de elementos](https://sqlite.org/lang_datefunc.html) en la documentación de referencia de SQLite.

---

## Expresiones CAST

```sql
-- empleados que hayan nacido en el segundo semestre del año
SELECT nombre, fechaNac AS fecha, CAST(strftime('%m',fechaNac) AS INTEGER) mes
FROM empleado
WHERE CAST(strftime('%m',fechaNac) AS INTEGER) > 6;
```

Tabla resultado:

| nombre   | fecha      | mes |
| -------- | ---------- | --- |
| José     | 1965-09-01 | 9   |
| Alberto  | 1955-12-08 | 12  |
| Fernando | 1962-09-15 | 9   |
| Aurora   | 1972-07-31 | 7   |
| Eduardo  | 1937-11-10 | 11  |

- Se utiliza una expresión `CAST` con la forma `CAST(expr AS nombre-tipo)` para convertir el valor de `expr` a un tipo de datos diferente especificado por `nombre-tipo`.
- Si el valor de `expr` es `NULL`, entonces el resultado de la expresión `CAST` también es `NULL`.

---

## Consultas Combinadas

Este tipo de consultas combinan los resultados de múltiples consultas en una sola tabla resultado.

$$(SELECT\ 1)\ UNION/INTERSECT/EXCEPT\ (SELECT\ 2)$$

Para ello, existen tres operadores de conjuntos:  
•    UNION (con la variante UNION ALL)  
•    INTERSECT  
•    EXCEPT (también llamado MINUS en otras versiones de SQL)

---

## Tablas resultado compatibles

Para poder aplicar estos operadores, las tablas resultado de las consultas deben ser compatibles:

- Todas las consultas tienen el **mismo número de columnas**.
- Las **columnas en la misma posición deben tener tipos de datos compatibles**.

Los nombres de las columnas de la primera consulta determinan los nombres de las columnas del conjunto de resultados combinado.

Solo se pueden utilizar las cláusulas **`ORDER BY` o `LIMIT` al final de la consulta combinada**. Estas cláusulas se aplicarán al conjunto de resultados combinado, no al conjunto de resultados individual.

---

## UNION

```sql
SELECT 
   nombre, 
   CAST(strftime('%Y', 'now') AS INTEGER) - CAST(strftime('%Y', fechaNac) AS INTEGER) AS edad
FROM empleado

UNION

SELECT 
   nombre, 
   CAST(strftime('%Y', 'now') AS INTEGER) - CAST(strftime('%Y', fechaNac) AS INTEGER) AS edad
FROM familiar
ORDER BY edad DESC;
```

Tabla resultado:

| nombre   | edad |
| -------- | ---- |
| Eduardo  | 88   |
| Juana    | 84   |
| Alfonso  | 83   |
| Alberto  | 70   |
| Luisa    | 67   |
| Fernando | 63   |
| José     | 60   |
| Elisa    | 58   |
| Alicia   | 57   |
| Luis     | 56   |
| Aurora   | 53   |
| Teodoro  | 42   |
| Alicia   | 39   |
| Alice    | 37   |
| Miguel   | 37   |

`UNION`devuelve la **unión de las tablas resultado de ambas consultas**.

**Consulta**: devuelve los nombres y la edad de todas las personas almacenadas en la base de datos, sean empleados o familiares, ordenados por edad de más anciano a más joven.

- La primera consulta devuelve el `nombre` y la edad de los empleados

| nombre   | edad | 
| -------- | ---- |
| José     | 60   |
| Alberto  | 70   |
| Alicia   | 57   |
| Juana    | 84   |
| Fernando | 63   |
| Aurora   | 53   |
| Luis     | 56   |
| Eduardo  | 88   |

- La segunda consulta devuelve el `nombre` y la edad de los familiares

| nombre  | edad |
| ------- | ---- |
| Alfonso | 83   |
| Luisa   | 67   |
| Elisa   | 58   |
| Teodoro | 42   |
| Alicia  | 39   |
| Miguel  | 37   |
| Alice   | 37   |

---

## UNION ALL

```sql
select numero from departamento
UNION ALL
select distinct dpto from proyecto;
```

Tabla resultado:

| numero |
| ------ |
| 5      |
| 6      | 
| 1      |
| 4      |
| 5      |
| 4      |
| 1      |

`UNION ALL` no elimina duplicados de la tabla resultado después de realizar la unión.

--- 

## INTERSECT

```sql
SELECT supervisor as dni FROM EMPLEADO WHERE supervisor IS NOT NULL
INTERSECT
SELECT empleado from FAMILIAR
GROUP BY empleado
HAVING count(*)> 1;
```

Tabla resultado:

| dni       | 
| --------- |
| 333445555 |

`INTERSECT` devuelve la **intersección de las tablas resultado de ambas consultas**.

**Consulta**: devuelve los empleados que son supervisores y tienen más de un familiar.

- La primera consulta devuelve los`dni` de los supervisores de la tabla `EMPLEADO`

| dni       | 
| --------- |
| 333445555 |
| 888665555 |
| 987654321 |
| 888665555 |
| 333445555 |
| 333445555 |
| 987654321 |

- La segunda consulta devuelve los `dni` de los empleados con más de un familiar en la tabla `FAMILIAR`

| empleado  | 
| --------- |
| 123456789 |
| 333445555 |

---

## EXCEPT

```sql
-- Todos los empleados 
SELECT dni FROM EMPLEADO 
EXCEPT 
-- Empleados que son directores 
SELECT director as dni FROM DEPARTAMENTO;
```

Tabla resultado:

| dni       | 
| --------- |
| 123456789 |
| 666884444 |
| 987987987 |
| 999887777 |

`EXCEPT` devuelve la **resta de las tablas resultado de ambas consultas**.

**Consulta**: devuelve los empleados que no dirigen un departamento.

- La primera consulta devuelve los`dni` de los empleados de la tabla `EMPLEADO`

| dni       |
| --------- |
| 123456789 |
| 333445555 |
| 453453453 |
| 666884444 | 
| 888665555 |
| 987654321 |
| 987987987 |
| 999887777 |

- La primera consulta devuelve los`dni` de los directores de la tabla `DEPARTAMENTO`

| dni       |
| --------- |
| 333445555 |
| 453453453 | 
| 888665555 |
| 987654321 |

---

## Ejercicio 04 - Consultas combinadas

Escribe una consulta que devuelva los empleados con más de un familiar que no son supervisores.

Solución:
```sql

```

Tabla resultado:

| empleado  | 
| --------- |
| 123456789 |

---

## Múltiples consultas combinadas

```sql
-- Empleados que trabajan en proyectos pero NO tienen familiares registrados
-- y tampoco son supervisores

-- Empleados en proyectos
SELECT DISTINCT empleado as dni
FROM TRABAJA_EN
 
EXCEPT
 
-- Empleados con familiares
SELECT DISTINCT empleado as dni
FROM FAMILIAR

EXCEPT
  
-- Empleados que son supervisores
SELECT DISTINCT supervisor as dni
FROM EMPLEADO
WHERE supervisor IS NOT NULL;
```

Tabla resultado:

| dni       |
| --------- |
| 453453453 | 
| 666884444 |
| 987987987 |
| 999887777 |

En SQLite, cuando tres o más `SELECT` simples se conectan en una consulta combinada, se agrupan de izquierda a derecha. Si "A", "B" y "C" son sentencias `SELECT` simples, (A op B op C) se procesa como ((A op B) op C).

**Consulta**: Empleados que trabajan en proyectos pero NO tienen familiares registrados y tampoco son supervisores.

- La primera consulta devuelve los `dni` de los empleados que aparecen en `TRABAJA_EN`

| dni       |
| --------- |
| 123456789 |
| 333445555 |
| 453453453 |
| 666884444 |
| 888665555 |
| 987654321 |
| 987987987 | 
| 999887777 |

- La segunda consulta devuelve los `dni` de los empleados que tienen familiar

| dni       |
| --------- |
| 123456789 | 
| 333445555 |
| 987654321 |

- La operación `EXCEPT`sobre las tablas anteriores devuelve:

| dni       |
| --------- |
| 453453453 | 
| 666884444 |
| 888665555 |
| 987987987 |
| 999887777 |

- La tercera consulta devuelve los `dni` de los empleados que son supervisores

| dni       |
| --------- |
| 333445555 |
| 888665555 | 
| 987654321 |

---

## UNION y múltiples tablas en FROM

```sql
SELECT DISTINCT P.numero, P.nombre, E.dni
FROM PROYECTO P, DEPARTAMENTO D, EMPLEADO E
WHERE P.dpto = D.numero AND D.director = E.dni
AND apellido1 = 'Campos'
UNION
SELECT DISTINCT P.numero, P.nombre, E.dni
FROM PROYECTO P, TRABAJA_EN T, EMPLEADO E
WHERE P.numero = T.proyecto AND T.empleado = E.dni
AND apellido1 = 'Campos';
```

Tabla resultado:

| numero | nombre         | nombre  | apellido1 |
| ------ | -------------- | ------- | --------- |
| 1      | ProductoX      | Alberto | Campos    |
| 2      | ProductoY      | Alberto | Campos    |
| 3      | ProductoZ      | Alberto | Campos    |
| 10     | Computación    | Alberto | Campos    |
| 20     | Reorganización | Alberto | Campos    |

**Una consulta algo más interesante**: Hacer una lista de todos los números de proyecto para aquellos proyectos que involucran a un empleado cuyo apellido sea  'Campos', ya sea como trabajador o como director del departamento que controla el proyecto.

- La primera consulta SELECT recupera los proyectos que involucran a un 'Campos' como director del departamento que controla el proyecto.
- La segunda recupera los proyectos que involucran a un 'Campos' como trabajador.
- Al aplicar la operación UNION a las dos consultas SELECT se obtiene el resultado deseado.

>[!info] Múltiples tablas en la cláusula `FROM` sin `JOIN`.
>En esta consulta aparecen **varias tablas en la cláusula `FROM`** que tiene como resultado la creación de una tabla con el producto cartesiano de las tablas listadas. Esta forma de juntar resultados de varias tablas es común en código SQL heredado, pero actualmente no se recomienda. La opción recomendable es usar `JOIN`, que veremos más adelante.

---

## Ejercicio 05 - Varias combinadas

Escribe una consulta que devuelva el dni de los empleados que no tienen familiares y el dni de los supervisores con más de 2 empleados a su cargo.

Solución:
```sql


```

Tabla resultado:

| dni       |
| --------- |
| 333445555 |
| 453453453 |
| 666884444 | 
| 888665555 |
| 987987987 |
| 999887777 |

---

## Cláusula WITH

```sql
WITH resumen_dpto AS (
    SELECT 
        dpto,
        COUNT(*) as num_empleados,
        AVG(sueldo) as sueldo_medio,
        MAX(sueldo) as sueldo_maximo
    FROM empleado
    GROUP BY dpto
)
SELECT * FROM resumen_dpto
ORDER BY sueldo_medio DESC;
```

Tabla resultado:

| dpto | num_empleados | sueldo_medio | sueldo_maximo |
| ---- | ------------- | ------------ | ------------- |
| 1    | 1             | 55000        | 55000         |
| 5    | 4             | 33250        | 40000         |
| 4    | 3             | 31000        | 43000         |

Una Expresión de Tabla Común (**Common Table Expression, CTE**) es una **consulta temporal con nombre** que puedes definir dentro de una sentencia SQL y luego referenciar como si fuera una tabla. Se define **usando la cláusula `WITH`**.

Ventajas de las CTEs:

1. **Legibilidad**: Hacen el código más claro y fácil de entender
2. **Reutilización**: Se puede referenciar a la misma CTE múltiples veces
3. **Modularidad**: Dividen consultas complejas en partes más simples
4. **Recursividad**: Permiten consultas recursivas (como la jerarquía de supervisores)

---

## CTEs recursivas

```sql
WITH RECURSIVE jerarquia_supervisores (empleado_dni, supervisor_dni) AS (
    -- Caso base: cada empleado con su supervisor directo
    SELECT 
        dni,
        supervisor
    FROM empleado
    WHERE supervisor IS NOT NULL
    
    UNION ALL
    
    -- Caso recursivo: obtener el supervisor del supervisor
    SELECT 
        js.empleado_dni,
        e.supervisor as supervisor_dni
    FROM jerarquia_supervisores js, empleado e
    WHERE js.supervisor_dni = e.dni
      AND e.supervisor IS NOT NULL
)
SELECT DISTINCT
    empleado_dni,
    supervisor_dni
FROM jerarquia_supervisores
ORDER BY empleado_dni;

```

Tabla resultado:

| empleado_dni | supervisor_dni | 
| ------------ | -------------- |
| 123456789    | 333445555      |
| 123456789    | 888665555      |
| 333445555    | 888665555      |
| 453453453    | 333445555      |
| 453453453    | 888665555      |
| 666884444    | 333445555      |
| 666884444    | 888665555      |
| 987654321    | 888665555      |
| 987987987    | 987654321      |
| 987987987    | 888665555      |
| 999887777    | 987654321      |
| 999887777    | 888665555      |

`WITH RECURSIVE` define una CTE recursiva que contiene:
	1. Un caso base
	2. Uno o más casos recursivos
	3. Un operador de combinación 

Esta sintaxis se añadió en SQL:99 para permitir a los usuarios especificar una consulta recursiva de forma declarativa. 

Ejemplo de consulta recursiva en SQL entre tuplas del mismo tipo: relación recursiva entre  empleado y supervisor. Explicación de la consulta:

- La vista `jerarquia_supervisores` mantiene el resultado de la consulta recursiva
- La vista está vacía inicialmente
- Se inicializa con todas las relaciones empleado-supervisor directas (caso base)
- Este conjunto de resultados inicial se combina, mediante la `UNION`, con cada nivel sucesivo de supervisores (caso recursivo)
- El caso recursivo se repite hasta que la consulta no devuelve más resultados

---

## Ejercicio 06 - Cláusula WITH

Escribe una consulta que devuelva los proyectos del departamento con mayor número de trabajadores (pista: usa una CTE para hallar el departamento con más personal), mostrando su nombre, ubicación y departamento.

Solución:
```sql

```

Tabla resultado:

| nombre    | ubicacion | dpto |
| --------- | --------- | ---- |
| ProductoX | Valencia  | 5    |
| ProductoY | Sevilla   | 5    |
| ProductoZ | Madrid    | 5    |

---