# JOIN

## Objetivos

Dentro del Lenguaje de Manipulación de Datos, en esta sesión veremos:

- Producto cartesiano (CROSS JOIN)
- Concatenación interna (INNER JOIN) 
- Concatenación interna sobre la misma tabla (SELF JOIN) 
- Concatenación izquierda externa (LEFT OUTER JOIN) 
- Concatenación derecha externa (RIGHT OUTER JOIN) 
- Concatenación completa externa (FULL OUTER JOIN) 

En esta sesión trabajaremos con la [BD Empresa completa en SQL Snippets (Empresa BDE)](https://i3lab.unex.es/sql-snippets/index.html?db=empresabd).

---

## Ejercicio 01 - Repaso SELECT

Escribe una consulta para obtener el nombre del departamento, su director, su fecha de ingreso (fecha) y mes del director con el mes de incorporación más cercano a diciembre.

Solución:
```sql

```

Tabla resultado:

| nombre       | director  | fecha      | mes | 
| ------------ | --------- | ---------- | --- |
| Sede Central | 888665555 | 1981-06-19 | 06  |

---

## Introducción a la concatenación de tablas (JOIN)

La concatenación (JOIN en inglés y SQL) de tablas **combina filas de varias tablas**.

Saber concatenar adecuadamente datos almacenados en distintas tablas es una **habilidad esencial que nos permite extraer información de tablas separadas** y reunirlas en un único conjunto significativo de resultados.

Para realizar esta combinación existen distintas **variantes del JOIN en SQL** que producirán distintos resultados.

![join types](https://red9.com/wp-content/uploads/2025/05/sql-joins-visualized-venn-diagram-red9.png)

En estos ejemplos solo se muestra gráficamente la concatenación de dos tablas, pero todas las distintas concatenaciones que vamos a ver son igualmente aplicables a la concatenación de 3 o más tablas.

---

## Producto cartesiano (CROSS JOIN)

```sql
--Obtener familiares de empleadas (no significativo)

SELECT 
 E.Nombre, 
 E.Apellido1, 
 E.Dni, 
 F.Nombre
FROM Empleado E 
 CROSS JOIN Familiar F
WHERE E.sexo='F';

```

Tabla resultado:

| nombre | apellido1 | dni       | nombre  | 
| ------ | --------- | --------- | ------- |
| Alicia | Jiménez   | 999887777 | Alice   |
| Alicia | Jiménez   | 999887777 | Elisa   |
| Alicia | Jiménez   | 999887777 | Miguel  |
| Alicia | Jiménez   | 999887777 | Alicia  |
| Alicia | Jiménez   | 999887777 | Luisa   |
| Alicia | Jiménez   | 999887777 | Teodoro |
| Alicia | Jiménez   | 999887777 | Alfonso |
| Juana  | Sáinz     | 987654321 | Alice   |
| Juana  | Sáinz     | 987654321 | Elisa   |
| Juana  | Sáinz     | 987654321 | Miguel  |
| Juana  | Sáinz     | 987654321 | Alicia  |
| Juana  | Sáinz     | 987654321 | Luisa   |
| Juana  | Sáinz     | 987654321 | Teodoro |
| Juana  | Sáinz     | 987654321 | Alfonso |
| Aurora | Oliva     | 453453453 | Alice   |
| Aurora | Oliva     | 453453453 | Elisa   |
| Aurora | Oliva     | 453453453 | Miguel  |
| Aurora | Oliva     | 453453453 | Alicia  |
| Aurora | Oliva     | 453453453 | Luisa   |
| Aurora | Oliva     | 453453453 | Teodoro |
| Aurora | Oliva     | 453453453 | Alfonso |


- El **producto cartesiano** (CROSS JOIN) de dos o más tablas **produce un esquema** que será la **concatenación de los esquemas de las tablas involucradas.**
- Las **tuplas de la relación resultante** se habrán obtenido mediante **concatenación de cada tupla de una de las tablas con todas las tuplas de las otras**.
- El **resultado del producto cartesiano no suele ser especialmente útil o significativo** porque la mayoría de los datos combinados tienen tuplas que no tienen nada que ver que ver entre sí.

>[!info]+ Sintaxis en desuso
>```sql
>SELECT E.*, F.*
>FROM Empleado E, Familiar F;
>```

---

## Producto cartesiano (CROSS JOIN)

```sql
--Obtener familiares de empleadas (significativo)

SELECT 
 E.Nombre, 
 E.Apellido1, 
 E.Dni, 
 F.Nombre
FROM Empleado E 
 CROSS JOIN Familiar F
WHERE E.sexo='F' AND E.Dni = F.Empleado;

```

Tabla resultado:

| nombre | apellido1 | dni       | nombre  | 
| ------ | --------- | --------- | ------- |
| Juana  | Sáinz     | 987654321 | Alfonso |

- Consulta significativa: nos quedamos solo con las tuplas de ambas tablas que están relacionadas
- Solo tenemos que filtrar por la condición $R.PK = S.FK$
- En este caso, `E.Dni` es la PK de Empleado y F.Empleado es FK en Familiar.

---

## Concatenación interna (INNER JOIN)


```sql
--Obtener familiares de empleadas (significativo)

SELECT 
 E.Nombre, 
 E.Apellido1, 
 E.Dni, 
 F.Nombre
FROM Empleado E 
 INNER JOIN Familiar F ON E.Dni = F.Empleado
WHERE E.sexo='F';

```

Tabla resultado:

| nombre | apellido1 | dni       | nombre  | 
| ------ | --------- | --------- | ------- |
| Juana  | Sáinz     | 987654321 | Alfonso |

- `INNER JOIN` es la **operación de `CROSS JOIN`con una condición de selección**, como puede ser `E.Dni = F.Empleado`. 
- La condición de JOIN se elimina de la cláusula `WHERE` para evitar mezclarla con las condiciones de selección: mejora la legibilidad.
- La condición del JOIN **generalmente, será $R.PK = S.FK$**.

>[!info]+ Sintaxis en desuso
>```sql
>SELECT E.*, F.*
>FROM Empleado E, Familiar F
>WHERE E.sexo='F' AND E.Dni = F.Empleado;
>```

---

## Ejercicio 02 - INNER JOIN

Escribe una consulta que devuelva el nombre de cada proyecto (nombre_proyecto) y el total de horas trabajadas en cada uno (total_horas_dedicadas), ordena los resultados por las horas trabajabas empezando por el de mayor horas. 

```sql

```

Tabla resultado:

| nombre_proyecto | total_horas_dedicadas |
| --------------- | --------------------- |
| Comunicaciones  | 55                    | 
| Computación     | 55                    |
| ProductoX       | 52.5                  |
| ProductoZ       | 50                    |
| ProductoY       | 37.5                  |
| Reorganización  | 25                    |

---

## Concatenación interna (NATURAL JOIN)

```sql
--Definimos vista temporal para cambiar nombre a atributo empleado a dni
-- NATURAL JOIN con notación USING

WITH Fam(dni,nombre) AS (
  SELECT 
   empleado, 
   nombre
  FROM Familiar
)
SELECT 
 E.Nombre, 
 E.Apellido1, 
 E.Dni, 
 F.Nombre
FROM Empleado E 
 INNER JOIN Fam F USING(dni)
WHERE E.sexo='F';
```

Tabla resultado: 

| nombre | apellido1 | dni       | nombre  | 
| ------ | --------- | --------- | ------- |
| Juana  | Sáinz     | 987654321 | Alfonso |

- `NATURAL JOIN` **iguala automáticamente los atributos con el mismo nombre** en ambas tablas para generar la condición de concatenación
- `INNER JOIN USING` es una forma más segura de hacer `NATURAL JOIN`: permite indicar qué atributo debe usarse en la condición de concatenación
- La vista temporal con `WITH` es necesaria para cambiar el nombre al atributo `empleado` de `Familiar` a `dni` para que haya coincidencia de nombres entre ambas tablas.

---

## Concatenación interna con múltiples tablas


```sql
-- Empleado que trabaja en cada proyecto

SELECT 
 P.Nombre, 
 P.Ubicacion, 
 E.Nombre, 
 E.Apellido1
FROM Proyecto P 
 INNER JOIN  TRABAJA_EN T ON (P.numero=T.proyecto) 
 INNER JOIN Empleado E ON (E.dni=T.empleado)
WHERE E.sexo='F';
```

Tabla resultado: 

| nombre         | ubicacion | nombre | apellido1 | 
| -------------- | --------- | ------ | --------- |
| ProductoX      | Valencia  | Aurora | Oliva     |
| ProductoY      | Sevilla   | Aurora | Oliva     |
| Reorganización | Madrid    | Juana  | Sáinz     |
| Comunicaciones | Gijón     | Juana  | Sáinz     |
| Computación    | Gijón     | Alicia | Jiménez   |
| Comunicaciones | Gijón     | Alicia | Jiménez   |

- Se pueden **concatenar todas las tablas que hagan falta** mediante una secuencia de operaciones `JOIN`
- Una de las **primeras decisiones** al realizar una consulta SQL debe ser **qué tablas se deben concatenar** para obtener la información solicitada

---

## Ejercicio 03 - INNER JOIN con múltiples tablas

Escribe una consulta que devuelva el nombre (nombre_empleado) y apellido1 de empleado y el nombre (nombre_proyecto) y la ubicación del proyecto donde trabaja. Ordena el resultado por el nombre del empleado en orden alfabético.

Solución:

```sql

```

Tabla resultado:

| nombre_empleado | apellido1 | nombre_proyecto | ubicacion |
| --------------- | --------- | --------------- | --------- |
| Alberto         | Campos    | ProductoY       | Sevilla   | 
| Alberto         | Campos    | ProductoZ       | Madrid    |
| Alberto         | Campos    | Computación     | Gijón     |
| Alberto         | Campos    | Reorganización  | Madrid    |
| Alicia          | Jiménez   | Computación     | Gijón     |
| Alicia          | Jiménez   | Comunicaciones  | Gijón     |
| Aurora          | Oliva     | ProductoX       | Valencia  |
| Aurora          | Oliva     | ProductoY       | Sevilla   |
| Eduardo         | Ochoa     | Reorganización  | Madrid    |
| Fernando        | Ojeda     | ProductoZ       | Madrid    |
| José            | Pérez     | ProductoX       | Valencia  |
| José            | Pérez     | ProductoY       | Sevilla   |
| Juana           | Sáinz     | Reorganización  | Madrid    |
| Juana           | Sáinz     | Comunicaciones  | Gijón     |
| Luis            | Pajares   | Computación     | Gijón     |
| Luis            | Pajares   | Comunicaciones  | Gijón     |

---

## Concatenación interna sobre la misma tabla (SELF JOIN)


```sql
-- Supervisoras y sus supervisados directos

SELECT 
 S.Nombre as supervisor, 
 E.Nombre, 
 E.Apellido1
FROM Empleado S 
 INNER JOIN Empleado E ON (S.dni=E.supervisor)
WHERE S.sexo='F';
```

Tabla resultado:

| supervisor | nombre | apellido1 | 
| ---------- | ------ | --------- |
| Juana      | Alicia | Jiménez   |
| Juana      | Luis   | Pajares   |

- `SELF JOIN` es un `INNER JOIN` que  **combinar** las **filas** de la **misma tabla**
- Cuando la tabla define una FK referenciando a su propia PK
- A **cada instancia de la tabla** se le debe asignar un **alias diferente**.
- Cada referencia de columna debe ir precedida de un alias de tabla apropiado.

---

##  Ejercicio 04 - `SELF JOIN`

Escribe una consulta que devuelva los empleados que ganan entre 2000 y 10000 euros menos que su supervisor directo. Las columnas resultado deben ser: nombre_empleado, sueldo_empleado, nombre_supervisor y sueldo_supervisor.

```sql

```

Tabla resultado:

| nombre_empleado | sueldo_empleado | nombre_supervisor | sueldo_supervisor |
| --------------- | --------------- | ----------------- | ----------------- |
| José            | 30000           | Alberto           | 40000             | 
| Fernando        | 38000           | Alberto           | 40000             |

---

## Concatenación izquierda externa (LEFT JOIN)

```sql
-- Supervisores con su supervisado directo y resto empleados no supervisores

SELECT 
 S.Nombre as supervisor, 
 E.Nombre, 
 E.Apellido1
FROM Empleado S 
 LEFT JOIN Empleado E ON (S.dni=E.supervisor);
```

Tabla resultado:

| supervisor | nombre   | apellido1 |
| ---------- | -------- | --------- |
| José       |          |           |
| Alberto    | Aurora   | Oliva     |
| Alberto    | Fernando | Ojeda     | 
| Alberto    | José     | Pérez     |
| Alicia     |          |           |
| Juana      | Alicia   | Jiménez   |
| Juana      | Luis     | Pajares   |
| Fernando   |          |           |
| Aurora     |          |           |
| Luis       |          |           |
| Eduardo    | Alberto  | Campos    |
| Eduardo    | Juana    | Sáinz     |

- `LEFT JOIN` devuelve las combinaciones de **tuplas que cumplen la condición de concatenación y el resto de las tuplas de la primera tabla** con valores a `NULL` en atributos de la segunda
- En este caso, se devuelven todos los empleados en la columna `supervisor` tengan o no un empleado supervisado directo

>Es posible que vea consultas con estas uniones escritas como LEFT OUTER JOIN, RIGHT OUTER JOIN o FULL OUTER JOIN, pero la palabra clave OUTER realmente se conserva para la compatibilidad con SQL-92 y estas consultas son simplemente equivalentes a LEFT JOIN, RIGHT JOIN y FULL JOIN respectivamente.

>[!info]+ Sintaxis en desuso
>```sql
>SELECT S.*, E.*
>FROM Empleado S, Empleado E
>WHERE S.Dni (+)= E.Supervisor;
>```

---

## Concatenación derecha externa (RIGHT JOIN)


```sql
-- Empleados con su supervisor directo y resto empleados sin supervisor

SELECT 
 S.Nombre as supervisor, 
 E.Nombre, 
 E.Apellido1
FROM Empleado S 
 RIGHT JOIN Empleado E ON (S.dni=E.supervisor);
```

Tabla resultado:

| supervisor | nombre   | apellido1 |
| ---------- | -------- | --------- |
| Alberto    | José     | Pérez     |
| Alberto    | Fernando | Ojeda     |
| Alberto    | Aurora   | Oliva     |
| Juana      | Alicia   | Jiménez   | 
| Juana      | Luis     | Pajares   |
| Eduardo    | Alberto  | Campos    |
| Eduardo    | Juana    | Sáinz     |
|            | Eduardo  | Ochoa     |

- `RIGHT OUTER JOIN` devuelve las **combinaciones de tuplas que cumplen la condición de concatenación y el resto de las tuplas de la segunda tabla** con valores a `NULL` en atributos de la primera
- En este caso, se devuelven todos los empleados de la tabla E tengan o no supervisor

>[!info]+ Sintaxis en desuso
>```sql
>SELECT S.*, E.*
>FROM Empleado S, Empleado E
>WHERE S.Dni =(+) E.Supervisor;
>```

---

## Concatenación completa externa (FULL OUTER JOIN)

```sql
-- insertamos datos para poder ver todos los casos
INSERT INTO PROYECTO VALUES ('ProductoP', 40, 'Pamplona', 5);
INSERT INTO LOCALIZACIONES_DPTO VALUES (1, 'Barcelona');

SELECT
  P.nombre AS nombre_proyecto,
  P.ubicacion AS ubicacion_proyecto,
  L.dpto,
  L.ubicacion AS ubicacion_oficina
FROM
  PROYECTO AS P
FULL OUTER JOIN
  LOCALIZACIONES_DPTO AS L ON P.ubicacion = L.ubicacion;

--Ejecutar los DELETE después de obtener el resultado de la SELECT
--DELETE FROM PROYECTO WHERE numero=40;
--DELETE FROM LOCALIZACIONES_DPTO WHERE ubicacion='Barcelona';
```

Tabla resultado:

| nombre_proyecto | ubicacion_proyecto | dpto | ubicacion_oficina | 
| --------------- | ------------------ | ---- | ----------------- |
| ProductoX       | Valencia           | 5    | Valencia          |
| ProductoY       | Sevilla            | 5    | Sevilla           |
| ProductoZ       | Madrid             | 1    | Madrid            |
| ProductoZ       | Madrid             | 5    | Madrid            |
| Computación     | Gijón              | 4    | Gijón             |
| Reorganización  | Madrid             | 1    | Madrid            |
| Reorganización  | Madrid             | 5    | Madrid            |
| Comunicaciones  | Gijón              | 4    | Gijón             |
| ProductoP       | Pamplona           |      |                   |
|                 |                    | 1    | Barcelona         |

**Objetivo:** Obtener una lista completa de todas las ciudades involucradas en la empresa, ya sea porque albergan un proyecto, una oficina de departamento, o ambas cosas. Queremos ver claramente qué ciudades son "solo de proyectos", cuáles son "solo de oficinas" y cuáles tienen ambas.

`FULL OUTER JOIN` nos mostrará tres tipos de resultados combinados en una sola tabla:

1. **Coincidencia (Parte `INNER JOIN`):** Filas donde una ciudad existe tanto en `PROYECTO` como en `LOCALIZACIONES_DPTO`. Veremos los datos de ambas tablas en la misma fila.
2. **Solo en la Izquierda (`LEFT JOIN` sin coincidencia):** Filas para ciudades que tienen proyectos pero **ninguna oficina de departamento**.
3. **Solo en la Derecha (`RIGHT JOIN` sin coincidencia):** Filas para ciudades que tienen una oficina de departamento pero **ningún proyecto**.

Este ejemplo se basa en la **coincidencia de datos (nombres de ciudades)** en lugar de en una relación estructural forzada por una FK, permitiendo ilustrar el verdadero propósito del `FULL OUTER JOIN`: **crear un inventario completo de dos conjuntos de datos y encontrar tanto las coincidencias como las diferencias en ambas direcciones**.

>[!info]+ Sintaxis en desuso
>```sql
>SELECT P.*, L.*
>FROM Proyecto S, Localizaciones_Dpto L
>WHERE P.ubicacion (+)=(+) L.ubicacion;
>```

---

## Ejercicio 05 - OUTER JOIN 

Listar todos los proyectos y los empleados que trabajan en ellos. Asegurarse de que todos los proyectos aparecen en la lista, incluso si nadie trabaja en ellos. Columnas resultado: nombre_proyecto, nombre_empleado y horas. Ordena los resultados por nombre de proyecto en orden alfabético.

Antes del ejercicio: Inserta un nuevo proyecto
```sql
INSERT INTO Proyecto VALUES ('ProductoGTP',6,'Madrid',5);
```

Solución:
```sql

```

Tabla resultado:

| nombre_proyecto | nombre_empleado | horas | 
| --------------- | --------------- | ----- |
| Computación     | Alberto         | 10    |
| Computación     | Alicia          | 10    |
| Computación     | Luis            | 35    |
| Comunicaciones  | Alicia          | 30    |
| Comunicaciones  | Juana           | 20    |
| Comunicaciones  | Luis            | 5     |
| ProductoGTP     |                 |       |
| ProductoX       | Aurora          | 20    |
| ProductoX       | José            | 32.5  |
| ProductoY       | Alberto         | 10    |
| ProductoY       | Aurora          | 20    |
| ProductoY       | José            | 7.5   |
| ProductoZ       | Alberto         | 10    |
| ProductoZ       | Fernando        | 40    |
| Reorganización  | Alberto         | 10    |
| Reorganización  | Eduardo         |       |
| Reorganización  | Juana           | 15    |

---

## `LEFT Anti-JOIN`

```sql
-- Empleados que no son supervisores

SELECT 
 S.Nombre as supervisor, 
 E.Nombre, 
 E.Apellido1
FROM Empleado S 
 LEFT JOIN Empleado E ON (S.dni=E.supervisor)
WHERE E.dni is NULL;
```

Tabla resultado:

| supervisor | nombre | apellido1 | 
| ---------- | ------ | --------- |
| José       |        |           |
| Alicia     |        |           |
| Fernando   |        |           |
| Aurora     |        |           |
| Luis       |        |           |

- Un **Anti-Join** no es un tipo de `JOIN` que puedas escribir directamente en el código (como `INNER JOIN` o `LEFT JOIN`), sino que es un **patrón de consulta** que se usa para encontrar filas en una tabla que no tienen una correspondencia en la otra. El método que vimos es la forma más común de implementar este patrón:

	`LEFT JOIN` + `WHERE right_table.key IS NULL`

- Se le llama "anti" porque hace lo contrario a un `JOIN` normal. Mientras que un `INNER JOIN` busca **coincidencias**, un `ANTI-JOIN` busca la **ausencia de coincidencias**.

---

## `RIGHT Anti-JOIN`


```sql
-- Empleados sin supervisor

SELECT 
 S.Nombre as supervisor, 
 E.Nombre, 
 E.Apellido1
FROM Empleado S 
 RIGHT JOIN Empleado E ON (S.dni=E.supervisor)
WHERE S.dni IS NULL;
```

Tabla resultado:

| supervisor | nombre   | apellido1 |
| ---------- | -------- | --------- |
|            | Eduardo  | Ochoa     |

Un **Anti-Join** no es un tipo de `JOIN` que puedas escribir directamente en el código (como `INNER JOIN` o `RIGHT JOIN`), sino que es un **patrón de consulta** que se usa para encontrar filas en una tabla que no tienen una correspondencia en la otra. El método que vimos es la forma más común de implementar este patrón:

	`RIGHT JOIN` + `WHERE left_table.key IS NULL`

- Se le llama "anti" porque hace lo contrario a un `JOIN` normal. Mientras que un `INNER JOIN` busca **coincidencias**, un `ANTI-JOIN` busca la **ausencia de coincidencias**.

---

## `FULL Anti-JOIN` 

```sql
-- insertamos datos para poder ver todos los casos
INSERT INTO PROYECTO VALUES ('ProductoP', 40, 'Pamplona', 5);
INSERT INTO LOCALIZACIONES_DPTO VALUES (1, 'Barcelona');

SELECT
  P.nombre AS nombre_proyecto,
  P.ubicacion AS ubicacion_proyecto,
  L.dpto,
  L.ubicacion AS ubicacion_oficina
FROM
  PROYECTO AS P
FULL OUTER JOIN
  LOCALIZACIONES_DPTO AS L ON P.ubicacion = L.ubicacion
WHERE P.numero IS NULL OR L.dpto IS NULL;

--Ejecutar los DELETE después de obtener el resultado de la SELECT
--DELETE FROM PROYECTO WHERE numero=40;
--DELETE FROM LOCALIZACIONES_DPTO WHERE ubicacion='Barcelona';
```

Tabla resultado:

| nombre_proyecto | ubicacion_proyecto | dpto | ubicacion_oficina | 
| --------------- | ------------------ | ---- | ----------------- |
| ProductoP       | Pamplona           |      |                   |
|                 |                    | 1    | Barcelona         |

Un **Anti-Join** no es un tipo de `JOIN` que puedas escribir directamente en el código (como `INNER JOIN` o `FULL JOIN`), sino que es un **patrón de consulta** que se usa para encontrar filas en una tabla que no tienen una correspondencia en la otra. El método que vimos es la forma más común de implementar este patrón:

	`FULL JOIN` + `WHERE left_table.key IS NULL OR right_table.key IS NULL`

- Se le llama "anti" porque hace lo contrario a un `JOIN` normal. Mientras que un `INNER JOIN` busca **coincidencias**, un `ANTI-JOIN` busca la **ausencia de coincidencias**.

---
## Ejercicio 06 - `OUTER JOIN EXCLUSIVE`

Obtener un listado con el nombre completo de los empleados que **NO** tienen ningún familiar (cónyuge, hijo, etc.) registrado en la base de datos.

Solución:
```sql

```

Tabla resultado:

| nombre   | apellido1 | apellido2 |
| -------- | --------- | --------- |
| Alicia   | Jiménez   | Celaya    | 
| Fernando | Ojeda     | Ordóñez   |
| Aurora   | Oliva     | Azevuela  |
| Luis     | Pajares   | Morera    |
| Eduardo  | Ochoa     | Paredes   |

---

## El Peligro de `COUNT(*)` con `LEFT JOIN` 


```sql
SELECT
  E.nombre,
  COUNT(*) AS numero_hijos
FROM
  EMPLEADO AS E
LEFT JOIN
  FAMILIAR AS F ON E.dni = F.empleado AND F.relacion IN ('Hijo', 'Hija')
GROUP BY
  E.nombre;
```

Tabla resultado:

| nombre   | numero_hijos |
| -------- | ------------ |
| Alberto  | 2            | 
| Alicia   | 1            |
| Aurora   | 1            |
| Eduardo  | 1            |
| Fernando | 1            |
| José     | 2            |
| Juana    | 1            |
| Luis     | 1            |

**Objetivo:** Contar cuántos hijos/as tiene **cada** empleado. Es crucial que aparezcan también los que no tienen ninguno (con un cero).

Un error muy común y sutil aparece al intentar contar elementos relacionados con una `LEFT JOIN`. La intuición a menudo nos lleva a un resultado incorrecto.

**¿Por qué está mal?**

`COUNT(*)` cuenta filas, sin importar si sus columnas son NULL o no. Cuando hacemos `LEFT JOIN` desde EMPLEADO a FAMILIAR, los empleados sin hijos (como Alicia Jiménez) aún generan una fila en el resultado intermedio, solo que las columnas de FAMILIAR son NULL. Al agrupar, COUNT(*) cuenta esa fila y devuelve 1, lo cual es incorrecto.

Tabla resultado de `EMPLEADO LEFT JOIN FAMILIAR`:

| nombre_empleado | nombre_familiar |
| --------------- | --------------- |
| José            | Alice           |
| José            | Miguel          |
| Alberto         | Alicia          |
| Alberto         | Teodoro         |
| **Alicia**      | **NULL**        |
| Juana           | NULL            |
| Fernando        | NULL            | 
| Aurora          | NULL            |
| Luis            | NULL            |
| Eduardo         | NULL            |

---

## El Peligro de `COUNT(*)` con `LEFT JOIN` 😵


```sql
SELECT
  E.nombre,
  E.apellido1,
  COUNT(F.nombre) AS numero_hijos -- Contamos una columna de la tabla FAMILIAR
FROM
  EMPLEADO AS E
LEFT JOIN
  FAMILIAR AS F ON E.dni = F.empleado AND F.relacion IN ('Hijo', 'Hija')
GROUP BY
  E.dni, E.nombre, E.apellido1; -- Es buena práctica agrupar por PK
```

Tabla resultado:

| nombre   | apellido1 | numero_hijos |
| -------- | --------- | ------------ |
| José     | Pérez     | 2            |
| Alberto  | Campos    | 2            |
| Aurora   | Oliva     | 0            |
| Fernando | Ojeda     | 0            |
| Eduardo  | Ochoa     | 0            |
| Juana    | Sáinz     | 0            |
| Luis     | Pajares   | 0            |
| Alicia   | Jiménez   | 0            |

La forma correcta de contar es usar **`COUNT()` sobre una columna específica de la tabla de la derecha** (la que puede tener NULLs). La función `COUNT(nombre_columna) ignora los valores NULL, dándonos el resultado exacto.

---

## Confundir `WHERE` con `ON` en un `OUTER JOIN`

```sql
-- INCORRECTO (se comporta como un INNER JOIN)
SELECT 
 E.nombre, 
 COALESCE(SUM(T.horas),0) AS total_horas
FROM EMPLEADO E 
 LEFT JOIN TRABAJA_EN T ON E.dni = T.empleado
WHERE T.proyecto IN (1,2)
GROUP BY E.dni;
-- Esto solo devolverá a José, ALberto y Aurora, porque el WHERE filtra a todos los demás.
```

Tabla resultado:

| nombre  | total_horas | 
| ------- | ----------- |
| José    | 40          |
| Alberto | 10          |
| Aurora  | 40          |

El **`LEFT JOIN` se ha convertido en un `INNER JOIN`** porque el `WHERE` filtra a todo el resto de empleados que no ha trabajado en el proyecto 1 o 2.

Este es un **error sutil pero crítico**.
   
- Una condición en la cláusula `WHERE` se aplica **después** de la concatenación. Filtra el resultado final. Esto puede convertir un `LEFT JOIN` en un `INNER JOIN` de forma no intencionada.

---

## Confundir `WHERE` con `ON` en un `OUTER JOIN`

```sql
-- CORRECTO
SELECT 
 E.nombre, 
 COALESCE(SUM(T.horas),0) AS total_horas
FROM EMPLEADO E 
 LEFT JOIN TRABAJA_EN T ON E.dni = T.empleado AND T.proyecto IN (1,2) 
GROUP BY E.dni;
-- Esto devolverá a TODOS los empleados, pero solo mostrará horas para José, Alberto y Aurora, y para el resto un 0
```

Tabla resultado:

| nombre   | total_horas | 
| -------- | ----------- |
| José     | 40          |
| Alberto  | 10          |
| Aurora   | 40          |
| Fernando | 0           |
| Eduardo  | 0           |
| Juana    | 0           |
| Luis     | 0           |
| Alicia   | 0           |

- Ahora obtenemos el **resultado correcto**: aparecen todos los empleados en el listado.

- Una **condición en la cláusula `ON` de un `LEFT JOIN` se aplica antes** de la concatenación. Filtra las filas de la tabla derecha que participarán en el `JOIN`.

---

## Ejercicio 07 - Horas totales por departamento

Escribe una consulta que devuelva para cada departamento su nombre (nombre_departamento), el numero de proyectos (num_proyectos) en los que se encuentra y el numero de horas trabajadas (total_horas_dedicadas) en total , ordena los resultados por las horas trabajabas empezando por el de mayor horas. 

Solución:

```sql

```

Tabla resultado:

| nombre_departamento | num_proyectos | total_horas_dedicadas | 
| ------------------- | ------------- | --------------------- |
| Investigación       | 3             | 140                   |
| Administración      | 2             | 110                   |
| Sede Central        | 1             | 25                    |

---