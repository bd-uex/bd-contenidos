# Subconsultas

## Objetivos

Aprender a utilizar subconsultas dentro de una sentencia SQL:
1. **Subconsultas en una sentencia SELECT**
	- **Subconsultas no correlacionadas**
		- La subconsulta devuelve un solo valor
		- La subconsulta devuelve un conjunto de filas
	-  **Subconsultas correlacionadas**
		- La subconsulta devuelve un solo valor
		- La subconsulta devuelve un conjunto de filas
2. **Subconsultas en UPDATES y DELETES**

En esta sesión trabajaremos con la [BD Empresa completa en SQL Snippets (Empresa BDE)](https://i3lab.unex.es/sql-snippets/index.html?db=empresabd).

---
## Ejercicio 01 - Repaso JOIN

La dirección de la empresa necesita un informe completo sobre la situación de cada departamento. Debes crear una consulta que muestre:

- **nombre_departamento**: Nombre del departamento
- **nombre_director**: Nombre completo del director (nombre y apellido1 separados por espacio)
- **num_empleados**: Número total de empleados del departamento (sin contar al director)
- **num_proyectos**: Número de proyectos asignados al departamento

**Requisitos importantes:**

1. Deben aparecer **TODOS los departamentos**, incluso si no tienen proyectos asignados o empleados trabajando
2. Ten cuidado con el uso de `COUNT()` cuando uses `LEFT JOIN`
3. Ordena el resultado por número de empleados (de mayor a menor)

Solución:
```sql

```

Tabla resultado:

| nombre_departamento | nombre_director | num_empleados | num_proyectos | 
| ------------------- | --------------- | ------------- | ------------- |
| Investigación       | Alberto Campos  | 3             | 3             |
| Administración      | Juana Sáinz     | 2             | 2             |
| Sede Central        | Eduardo Ochoa   | 0             | 1             |

---
## Subconsultas

Una **subconsulta** es una **consulta dentro de otra consulta**, utilizada para generar **resultados intermedios que pueden ser referenciados en la consulta principal**. Es decir, se trata de una consulta "anidada" que se ejecuta primero para proporcionar datos a la consulta externa​.

 ![SQL Correlated Subquery](https://campusvirtual.unex.es/zonauex/avuex/pluginfile.php/2616691/mod_lesson/page_contents/5520/blobid0.jpg)


Algunas características de las subconsultas son:

- **Modularidad**: permiten dividir una consulta compleja en partes más manejables, ayudando a mejorar la legibilidad y organización del código.​
- **Filtrado avanzado**: permiten filtrar resultados basados en información derivada de otras tablas o cálculos, algo que no siempre es posible con operadores simples.​
- **Optimización de consultas**: pueden mejorar el rendimiento al trabajar con subconjuntos de datos más específicos.​
- **Flexibilidad**: permiten combinar datos de maneras muy flexibles, al usarse en múltiples secciones de una consulta: en la cláusula **`SELECT`**, **`WHERE`**, o **`FROM`**.​

---
# Subconsultas No Correlacionadas

---

## Subconsultas No Correlacionadas​

Una **subconsulta no correlacionada** es un tipo de subconsulta en SQL que se ejecuta de manera independiente de la consulta principal. Esto significa que la subconsulta **se evalúa solo una vez** y sus resultados se utilizan en la consulta externa.

Este tipo de subconsulta es particularmente útil cuando necesitas hacer referencia a datos externos o valores calculados sin depender de cada fila procesada en la consulta principal. Las subconsultas no correlacionadas pueden ser utilizadas en diferentes partes de una consulta, como en las cláusulas **`SELECT`**, `FROM`, `WHERE` o `HAVING`.

Sus principales características son:

1. **Son ejecutadas Independientemente**: se ejecuta una sola vez, de manera independiente, antes de que la consulta principal sea evaluada.​
2. **No Depende de la Consulta Externa**: no requiere información de la consulta principal.​
3. **Sencillez en la Escritura**: su estructura es más simple y puede reutilizarse en múltiples contextos

Las **subconsultas pueden devolver**:

- **Un solo valor** (realmente una tabla resultado de 1x1)
- **Un conjunto de valores** (una tabla de resultados)

---
## Devuelve solo un valor: `WHERE`

```sql
SELECT
 E.nombre,
 E.apellido1,
 E.sueldo
FROM EMPLEADO E
WHERE Sueldo > ( SELECT AVG(Sueldo)
				 FROM EMPLEADO
				 WHERE Dpto = 5
				);
```

Tabla resultado:

| nombre   | apellido1 | sueldo |
| -------- | --------- | ------ |
| Alberto  | Campos    | 40000  |
| Juana    | Sáinz     | 43000  |
| Fernando | Ojeda     | 38000  |
| Eduardo  | Ochoa     | 55000  |
- subconsulta en la cláusula **`WHERE`** y devuelve solo un valor
- el valor devuelto se usa como operando en la condición del `WHERE`
- en el ejemplo:
	- la subconsulta calcula y devuelve el importe promedio (33250) de los sueldos de los empleados del departamento 5
	- la consulta principal usa ese valor para compararlo con el sueldo de cada uno de los empleados, filtrando aquellos que no cumplan la condición

---
##  Devuelve solo un valor: `SELECT` 

```sql
SELECT
 E.Nombre,
 E.Apellido1,
 E.Sueldo,
 ( SELECT AVG(Sueldo)
	FROM EMPLEADO
	WHERE Dpto = 5
  ) AS sueldoMedio5
FROM EMPLEADO E;
```

Tabla resultado:

| nombre   | apellido1 | sueldo | sueldoMedio5 |
| -------- | --------- | ------ | ------------ |
| José     | Pérez     | 30000  | 33250        |
| Alberto  | Campos    | 40000  | 33250        |
| Alicia   | Jiménez   | 25000  | 33250        |
| Juana    | Sáinz     | 43000  | 33250        |
| Fernando | Ojeda     | 38000  | 33250        |
| Aurora   | Oliva     | 25000  | 33250        |
| Luis     | Pajares   | 25000  | 33250        |
| Eduardo  | Ochoa     | 55000  | 33250        |
- subconsulta en la cláusula `SELECT` y devuelve solo un valor
- el valor devuelto se usa como valor de una nueva columna
- en el ejemplo:
	- la subconsulta calcula y devuelve el importe promedio de los sueldos de los empleados del departamento 5
	- la consulta principal usa ese valor para mostrarlo en una nueva columna de la tabla resultado.

---
##  Devuelve solo un valor: `HAVING` 

```sql
SELECT
 D.nombre,
 AVG(E.sueldo) AS sueldoMedio
FROM EMPLEADO E INNER JOIN DEPARTAMENTO D ON (E.dpto = D.numero)
HAVING AVG(E.sueldo) > ( SELECT AVG(Sueldo)
						 FROM EMPLEADO
						 WHERE Dpto = 5
					    );
```

Tabla resultado:

| nombre       | sueldoMedio |
| ------------ | ----------- |
| Sede Central | 55000       |

- subconsulta en la cláusula `HAVING` y devuelve solo un valor
- el valor devuelto se usa como operando en la condición del `HAVING`
- en el ejemplo:
	- la subconsulta calcula y devuelve el importe promedio de los sueldos de los empleados del departamento 5
	- la consulta principal usa ese valor para compararlo con el sueldo medio de cada uno de los departamentos, filtrando aquellos que no cumplan la condición

---
## Ejercicio 02

Escribe una consulta que muestre los nombres de los departamentos cuyo sueldo medio de empleados sea superior al sueldo medio general de la empresa.

Solución:
```sql

```

Tabla resultado:

| nombre       | sueldoMedio | 
| ------------ | ----------- |
| Sede Central | 55000       |

---

## Devuelve un conjunto de valores: `WHERE`

```sql
SELECT
 E.nombre,
 E.apellido1
FROM EMPLEADO E
WHERE Dni IN ( SELECT Empleado
				 FROM FAMILIAR
				 GROUP BY Empleado
				 HAVING COUNT(*) > 1
				);
```

Tabla resultado:

| nombre  | apellido1 |
| ------- | --------- |
| José    | Pérez     |
| Alberto | Campos    |
- subconsulta en la cláusula **`WHERE`** y devuelve un conjunto de valores
- el conjunto de valores devuelto se usa como operando en la condición del `WHERE`
- el operador `IN` comprueba si un valor específico aparece en un conjunto de resultados 
- en el ejemplo:
	- la subconsulta calcula y devuelve el `Dni`de los empleados que tienen más de un familiar
	- la consulta principal usa ese valor para compararlo con el `Dni` de cada uno de los empleados, filtrando aquellos que no estén en la lista

---
## Devuelve un conjunto de valores: `FROM` 

```sql
SELECT
 D.nombre,
 S.SueldoMedio
FROM DEPARTAMENTO D
INNER JOIN ( SELECT Dpto, AVG(Sueldo) AS sueldoMedio
			 FROM EMPLEADO
			 GROUP BY Dpto
			) AS S ON D.numero = S.numero;  
```

Tabla resultado:

| nombre         | sueldoMedio |
| -------------- | ----------- |
| Sede Central   | 55000       |
| Administración | 31000       |
| Investigación  | 33250       |

- subconsulta en la cláusula `FROM` y devuelve un conjunto de valores
- el conjunto de valores devuelto se usa como una tabla intermedia en el `FROM`
- en el ejemplo:
	- la subconsulta calcula y devuelve el importe promedio de los sueldos de los empleados de cada departamento 
	- la consulta principal usa ese valor para mostrarlo en una nueva columna de la tabla resultado junto al nombre de cada departamento

En algunos casos,  una subconsulta no correlacionada se puede reescribir utilizando `JOIN`. Por ejemplo, la consulta anterior puede reescribirse como un `JOIN`:

```sql
SELECT
 D.nombre,
 AVG(Sueldo) AS sueldoMedio
FROM DEPARTAMENTO D 
INNER JOIN EMPLEADO E ON D.numero = E.Dpto
GROUP BY Dpto;  
```

---
## Devuelve un conjunto de valores: `HAVING` 

```sql
SELECT
 D.nombre,
 COUNT( distinct E.Dni) AS numEmpleados
FROM DEPARTAMENTO D INNER JOIN EMPLEADO E ON D.numero = E.Dpto
GROUP BY E.Dpto
HAVING COUNT( distinct E.Dni) NOT IN (
	SELECT 
	 COUNT( distinct empleado) 
	FROM TRABAJA_EN 
	GROUP BY proyecto);  
```

Tabla resultado:

| nombre        | numEmpleados |
| ------------- | ------------ |
| Sede Central  | 1            |
| Investigación | 4            |

- subconsulta en la cláusula `HAVING` y devuelve un conjunto de valores
- el conjunto de valores devuelto se usa como como operando en la condición del `HAVING`
- `NOT IN` comprueba que un valor específico no aparezca en un conjunto de resultados 
- en el ejemplo:
	- la subconsulta calcula y devuelve el número de empleados por proyecto
	- la consulta principal usa ese valor para compararlo con el número de empleados por departamento, filtrando aquellos que estén en el conjunto de resultados


---
## Ejercicio 03

Escribe una consulta que liste el nombre y apellido de los empleados que trabajan en proyectos ubicados en 'Madrid'. Ordena los resultados alfabéticamente por apellido y nombre.

Solución:
```sql

```

Tabla resultado:

| nombre   | apellido1 | 
| -------- | --------- |
| Alberto  | Campos    |
| Eduardo  | Ochoa     |
| Fernando | Ojeda     |
| Juana    | Sáinz     |

---
# Subconsultas Correlacionadas

---
## Subconsultas Correlacionadas 

Las subconsultas correlacionadas **permiten resolver problemas más complejos y refinados**, ya que proporcionan la capacidad de hacer referencias cruzadas entre la consulta principal y la subconsulta.​ Pueden ser utilizadas en diferentes partes de una consulta, como en las cláusulas **`SELECT`**, `FROM`, `WHERE` o `HAVING`.

​Sus características principales son:

1. **Dependencia de la Consulta Externa**: La subconsulta correlacionada hace referencia a columnas de la consulta principal. Su ejecución está ligada a los valores de cada fila de esa consulta.​
2. **Ejecutada Varias Veces**: Se evalúa una vez por cada fila que procesa la consulta externa, lo que puede generar una mayor carga de procesamiento.​
3. **Interacción entre Consultas**: A diferencia de las no correlacionadas, este tipo de subconsulta crea una interacción directa entre las dos consultas, ya que cada fila de la consulta principal afecta el resultado de la subconsulta.​

Las **subconsultas pueden devolver**:

- **Un solo valor** (realmente una tabla resultado de 1x1)
- **Un conjunto de valores** (una tabla de resultados)

---
## Devuelve solo un valor: `WHERE`

```sql
SELECT DISTINCT e.nombre, e.apellido1
FROM EMPLEADO e
JOIN TRABAJA_EN t1 ON e.dni = t1.empleado
WHERE t1.horas > (
    SELECT MAX(t2.horas)
    FROM TRABAJA_EN t2
    JOIN EMPLEADO e2 ON t2.empleado = e2.dni
    WHERE e2.dpto = e.dpto    -- CORRELACIÓN
    AND t2.empleado != e.dni  -- CORRELACIÓN
    AND t2.horas IS NOT NULL
);
```

Tabla resultado:

| nombre | apellido1 | sueldo |
| ------ | --------- | ------ |
| José   | Pérez     | 30000  |
| Alicia | Jiménez   | 25000  |
| Aurora | Oliva     | 25000  |
| Luis   | Pajares   | 25000  |
- subconsulta en la cláusula **`WHERE`** y devuelve solo un valor
- el valor devuelto se usa como operando en la condición del `WHERE`
- la **subconsulta usa una columna de la consulta principal**
- en el ejemplo, encontrar empleados que trabajan más horas que cualquier otro empleado de su mismo departamento en cualquier proyecto:
	- la subconsulta calcula y devuelve el máximo de las horas trabajadas por un empleado del mismo departamento en cualquier proyecto para cada empleado (fila de consulta principal)
	- la consulta principal usa ese valor para compararlo con las horas de cada uno de los empleados, filtrando aquellos que no cumplan la condición

El **proceso de ejecución** de una subconsulta correlacionada sigue estos pasos:​

1. La consulta principal selecciona una fila de la tabla.​
2. La subconsulta se ejecuta usando los valores de esa fila.​
3. La subconsulta devuelve un resultado que se utiliza para evaluar la condición en la consulta principal.​
4. Este proceso se repite para cada fila de la consulta principal.​

---
## Devuelve solo un valor: `SELECT`

```sql
SELECT
 E1.nombre,
 E1.apellido1,
 E1.sueldo,
 ( SELECT AVG(E2.Sueldo)
	FROM EMPLEADO E2
	WHERE E1.Dpto = E2.Dpto  -- CORRELACIÓN
  ) AS sueldoMedio
FROM EMPLEADO E1;
```

Tabla resultado:

| nombre   | apellido1 | sueldo | sueldoMedio |
| -------- | --------- | ------ | ----------- |
| José     | Pérez     | 30000  | 33250       |
| Alberto  | Campos    | 40000  | 33250       |
| Alicia   | Jiménez   | 25000  | 31000       |
| Juana    | Sáinz     | 43000  | 31000       |
| Fernando | Ojeda     | 38000  | 33250       |
| Aurora   | Oliva     | 25000  | 33250       |
| Luis     | Pajares   | 25000  | 31000       |
| Eduardo  | Ochoa     | 55000  | 55000       |

- subconsulta en la cláusula **`SELECT`** y devuelve solo un valor
- el valor devuelto se usa como valor de una nueva columna
- la **subconsulta usa una columna de la consulta principal**
- en el ejemplo:
	- la subconsulta calcula y devuelve el importe promedio de los sueldos de los empleados del departamento que toca en cada ejecución (fila de consulta principal)
	- la consulta principal usa ese valor para mostrarlo en una nueva columna de la tabla resultado.

---
##  Devuelve solo un valor: `HAVING` 

```sql
SELECT dpto, AVG(sueldo) as sueldoMedio
FROM EMPLEADO
GROUP BY dpto
HAVING AVG(sueldo) = (
    SELECT AVG(sueldo)
    FROM EMPLEADO e2
    WHERE e2.dpto = EMPLEADO.dpto  -- CORRELACIÓN
    AND e2.supervisor IS NOT NULL
);
```

Tabla resultado:

| dpto | sueldoMedio |
| ---- | ----------- |
| 4    | 31000       |
| 5    | 33250       |

- subconsulta en la cláusula `HAVING` y devuelve solo un valor
- el valor devuelto se usa como operando en la condición del `HAVING`
- la **subconsulta usa una columna de la consulta principal**
- en el ejemplo:
	- la subconsulta calcula y devuelve el importe promedio de los sueldos de los empleados del departamento que toca en cada ejecución (fila de consulta principal) siempre que tengan supervisor
	- la consulta principal usa ese valor para compararlo con el sueldo medio de cada uno de los departamentos, filtrando aquellos que no cumplan la condición

---
## Ejercicio 04

Escribe una consulta que liste el el nombre y apellido de cada empleado junto con la diferencia entre su sueldo y el sueldo medio de su departamento, ordena los resultados de mayor a menor diferencia de sueldo.

Solución:
```sql

```

Tabla resultado:

| nombre   | apellido1 | diferenciaSueldoMedio | 
| -------- | --------- | --------------------- |
| Juana    | Sáinz     | 12000                 |
| Alberto  | Campos    | 6750                  |
| Fernando | Ojeda     | 4750                  |
| Eduardo  | Ochoa     | 0                     |
| José     | Pérez     | -3250                 |
| Alicia   | Jiménez   | -6000                 |
| Luis     | Pajares   | -6000                 |
| Aurora   | Oliva     | -8250                 |

---
## Devuelve un conjunto de valores: `WHERE`

```sql
SELECT 
 nombre, 
 apellido1
FROM EMPLEADO E
WHERE EXISTS (
    SELECT empleado
    FROM FAMILIAR F
    WHERE F.empleado = E.dni  -- CORRELACIÓN
    GROUP BY empleado
    HAVING COUNT(*) > 1
);
```

Tabla resultado:

| nombre  | apellido1 |
| ------- | --------- |
| José    | Pérez     |
| Alberto | Campos    |

- subconsulta en la cláusula **`WHERE`** y devuelve un conjunto de valores
- el conjunto de valores devuelto se usa como operando en la condición del `WHERE`
- el operador `EXISTS` se evalúa verdadero si la consulta devuelve resultados 
- en el ejemplo:
	- la subconsulta devuelve resultados si el empleado  que toca en cada ejecución (fila de consulta principal) tiene más de un familiar
	- la consulta principal usa ese valor booleano para filtrar aquellos empleados que no tengan familiar

---
## Devuelve un conjunto de valores: `HAVING` 

```sql
SELECT 
 D.nombre, 
 AVG(E.sueldo) as sueldoMedio
FROM EMPLEADO E INNER JOIN DEPARTAMENTO D ON E.dpto = D.numero
GROUP BY dpto
HAVING EXISTS (
    SELECT empleado
    FROM TRABAJA_EN T
    JOIN EMPLEADO E2 ON T.empleado = E2.dni
    WHERE E2.dpto = E.dpto  -- ← CORRELACIÓN
    GROUP BY empleado
    HAVING COUNT(DISTINCT proyecto) > 1
); 
```

Tabla resultado:

| nombre         | sueldoMedio |
| -------------- | ----------- |
| Administración | 31000       |
| Investigación  | 33250       |

- subconsulta en la cláusula `HAVING` y devuelve un conjunto de valores
- el conjunto de valores devuelto se usa como como operando en la condición del `HAVING`
- el operador `EXISTS` se evalúa verdadero si la consulta devuelve resultados 
- en el ejemplo, encontrar departamentos donde existan empleados que trabajen en más de un proyecto:
	- la subconsulta devuelve los empleados del mismo departamento del empleado  que toca en cada ejecución (fila de consulta principal) que trabajan en más de un proyecto
	- la consulta principal usa ese valor booleano para filtrar aquellos departamentos que no cumplen esa condición

---
## Ejercicio 05

Escribe una consulta que liste el nombre de los proyectos donde trabaja al menos un empleado del departamento de 'Investigación', ordenado alfabéticamente.

Solución:
```sql

```

Tabla resultado:

| nombre         | 
| -------------- |
| Computación    |
| ProductoX      |
| ProductoY      |
| ProductoZ      |
| Reorganización |

---

# Uso de Subconsultas en DELETE y UPDATE

---

## Uso de Subconsultas en DELETE y UPDATE

Además de su uso en **`SELECT`**, las subconsultas también se pueden utilizar en las sentencias **`DELETE`** y **`UPDATE`**. Esto nos permite eliminar o actualizar filas basándonos en los resultados de otra consulta, lo que es útil cuando las operaciones deben depender de datos relacionados o de condiciones que no se pueden expresar de manera sencilla sin una subconsulta.

---

## Subconsultas en DELETE

```sql
DELETE FROM EMPLEADO
WHERE dni NOT IN (
 SELECT empleado
 FROM FAMILIAR
);
```

- se utilizan para eliminar filas de una tabla según condiciones dependientes de otra tabla o de otros datos. 
- permiten identificar las filas que cumplen con ciertas condiciones basadas en una consulta adicional, antes de eliminarlas.
- en el ejemplo, se eliminan todos los empleados que no tienen famliares

>[!error]+ Violación de integridad referencial
>La consulta de este ejemplo no funcionará si tenemos activado el soporte de claves externas en SQLite porque viola la integridad referencial del atributo `director`en DEPARTAMENTO que es clave externa a `dni`en EMPLEADO.

---
## Subconsultas en UPDATE: WHERE

```sql
UPDATE EMPLEADO
SET sueldo = sueldo + (sueldo*0.30)
WHERE dni IN (
 SELECT empleado
 FROM TRABAJA_EN
 GROUP BY empleado
 HAVING COUNT(DISTINCT proyecto) > 2
);
```

- se utilizan para actualizar filas de una tabla según condiciones dependientes de otra tabla o de otros datos. 
- permiten identificar las filas que cumplen con ciertas condiciones basadas en una consulta adicional, antes de actualizarlas.
- en el ejemplo, se actualiza el sueldo a todos los empleados que trabajan en más de 2 proyectos.

---
## Subconsultas en UPDATE: SET


```sql
UPDATE EMPLEADO
SET sueldo = (
 SELECT AVG(Sueldo)
 FROM EMPLEADO
)
WHERE dpto=5;
```

- se utilizan para actualizar filas de una tabla en base a un valor calculado mediante la subconsulta 
- en el ejemplo, se actualiza el sueldo al sueldo medio total a todos los empleados que trabajan en el departamento 5.

---
## Ejercicio 06

Aumenta un 20% el sueldo de los empleados que trabajan más horas totales que el promedio de horas trabajadas por los empleados que tienen horas trabajadas.

Después de la actualización, consulta la tabla empleado con:

```
SELECT nombre, apellido1, sueldo FROM EMPLEADO;
```

Solución:
```sql

```

Tabla resultado:

| nombre   | apellido1 | sueldo | 
| -------- | --------- | ------ |
| José     | Pérez     | 36000  |
| Alberto  | Campos    | 48000  |
| Alicia   | Jiménez   | 30000  |
| Juana    | Sáinz     | 43000  |
| Fernando | Ojeda     | 45600  |
| Aurora   | Oliva     | 30000  |
| Luis     | Pajares   | 30000  |
| Eduardo  | Ochoa     | 55000  |

---
## Operadores típicos de subconsultas

| Operador       | Condición                                                                                  | Resultado                                         | SQLite |
| -------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------- | :----: |
| **IN**         | El valor de la consulta principal está en el conjunto devuelto por la subconsulta          | **`TRUE`** si hay coincidencias                   |   ✅    |
| **NOT IN**     | El valor de la consulta principal **no** está en el conjunto devuelto por la subconsulta   | **`FALSE`** si hay coincidencias                  |   ✅    |
| **EXISTS**     | La subconsulta devuelve al menos una fila                                                  | **`TRUE`** si la subconsulta devuelve resultados  |   ✅    |
| **NOT EXISTS** | La subconsulta **no** devuelve resultados                                                  | **`FALSE`** si la subconsulta devuelve resultados |   ✅    |
| **ALL**        | El valor de la consulta principal cumple la condición para **todos** los valores devueltos | **`TRUE`** si se cumple para todos                |   ❌    |
| **ANY**        | El valor de la consulta principal cumple la condición para **al menos uno** de los valores | **`TRUE`** si se cumple para alguno               |   ❌    |

- operadores fundamentales para trabajar con subconsultas, ya que permiten comparar un valor con un conjunto de resultados devueltos por una subconsulta de formas muy variadas
- `ALL`y `ANY`, aunque forman parte del estándar SQL, no están soportados en SQLite
- se puede conseguir el mismo comportamiento en SQLite haciendo que la subconsulta, en vez de devolver un conjunto de valores, devuelva solo un valor, el máximo o el mínimo de los valores. Las sustituciones serían:
	- `> ANY` → `> MIN`
	- `< ANY` → `< MAX`
	- `> ALL` → `> MAX`
	- `< ALL` → `< MIN`

---
## Subconsultas vs JOINs

Muchas consultas pueden definirse tanto con subconsultas como mediante JOINs, obteniéndose el mismo resultado. Sin embargo, es fundamental tener en cuenta el impacto en el rendimiento y evaluar si una subconsulta es la opción más eficiente o si se puede lograr el mismo resultado mediante un **`JOIN`**. En general, podemos decir:

- **Subconsultas**: Se ejecutan de forma separada de la consulta principal, lo que puede ser útil cuando solo se necesita un valor o conjunto de valores específico.
- **JOINs**: Combinan tablas directamente, lo que puede ser más eficiente para grandes conjuntos de datos, pero puede no ser adecuado si solo necesitas un valor calculado específico, como el resultado de un **`AVG`**, **`SUM`**, o **`COUNT`**

### **Subconsultas**

| **Ventajas** | **Inconvenientes** |
|--------------|-------------------|
| **Legibilidad en lógica compleja**: Para consultas con múltiples niveles de filtrado, pueden ser más intuitivas de leer | **Rendimiento**: Generalmente más lentas, especialmente si se ejecutan por cada fila (subconsultas correlacionadas) |
| **Modularidad**: Permiten resolver el problema paso a paso, facilitando el desarrollo y debugging | **Menor optimización**: Los optimizadores de consultas suelen tener más dificultad para optimizarlas |
| **Útiles para comparaciones agregadas**: Excelentes cuando necesitas comparar un valor con un agregado (MAX, AVG, etc.) | **Dificultad de mantenimiento**: Las subconsultas anidadas pueden volverse difíciles de seguir |
| **No generan duplicados**: En subconsultas escalares o con EXISTS, no hay riesgo de multiplicación de filas |  |

### **JOINs**

| **Ventajas** | **Inconvenientes** |
|--------------|-------------------|
| **Rendimiento superior**: Más eficientes, especialmente con índices adecuados | **Complejidad visual**: Con muchas tablas, la sintaxis puede volverse difícil de leer |
| **Mejor optimización**: Los motores de bases de datos están altamente optimizados para JOINs | **Riesgo de duplicados**: Un JOIN incorrecto puede multiplicar filas inesperadamente |
| **Flexibilidad**: Permiten combinar múltiples columnas de diferentes tablas fácilmente | **Curva de aprendizaje**: Los diferentes tipos de JOIN (INNER, LEFT, RIGHT, FULL) requieren comprensión clara |
| **Estándar para relaciones**: Son la forma natural de trabajar con bases de datos relacionales | |

---

## Ejercicio 07

Reescribe la siguiente consulta que utiliza JOINs para que use **subconsultas** en su lugar. Ordena los resultados por apellido y nombre.

```sql
-- Empleados que trabajan en proyectos ubicados en 'Madrid'
SELECT DISTINCT
  E.nombre,
  E.apellido1
FROM EMPLEADO E
INNER JOIN TRABAJA_EN T ON E.dni = T.empleado
INNER JOIN PROYECTO P ON T.proyecto = P.numero
WHERE P.ubicacion = 'Madrid';
```

Solución:
```sql

```

Tabla resultado:

| nombre   | apellido1 | 
| -------- | --------- |
| Alberto  | Campos    |
| Eduardo  | Ochoa     |
| Fernando | Ojeda     |
| Juana    | Sáinz     |

---
 