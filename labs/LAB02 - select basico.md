# SELECT


---

- Más operadores y CASE WHEN
- Valores nulos (null)
- Funciones de agregación de datos
- Agrupación
- Filtrado de agregaciones y agrupaciones

---

### Ejercicio 1 - Repaso SELECT

Escribe una consulta que calcule y devuelva una columna llamada `what_where` que tenga la especie (`species`) en letras mayúsculas y la isla (`island`) en letras minúsculas de cada pingüino separadas por un solo espacio; y una columna llamada `bill_ratio` que tenga la relación entre la longitud del pico y la profundidad del mismo. Redonda el resultado para mostrar números con un solo decimal. El resultado solo debe incluir pingüinos que sean hembras. Ordena el resultado de forma descendente por `bill_ratio`. Y limita el resultado a las 10 primeras filas.

Solución:
```sql

```

Resultado:

| what_where    | bill_ratio |
| ------------- | ---------- |
| GENTOO biscoe | 3.6        |
| GENTOO biscoe | 3.5        |
| GENTOO biscoe | 3.5        |
| GENTOO biscoe | 3.5        |
| GENTOO biscoe | 3.4        |
| GENTOO biscoe | 3.4        |
| GENTOO biscoe | 3.4        |
| GENTOO biscoe | 3.4        |
| GENTOO biscoe | 3.4        |
| GENTOO biscoe | 3.3        |


---

## Más operadores de filtrado y clasificación de salida

- Filtrar valores por rango: operador `between`
- Filtrar valores en una lista: operador `in`
- Clasificar la salida: CASE WHEN

---

### Filtrar valores por rango

Código SQL:
```sql
select 
	species,
	island,
	sex,
	body_mass_g
from penguins
where body_mass_g between 3000 and 3150
order by body_mass_g desc;
```

Resultado:

| species | island    | sex    | body_mass_g |
| ------- | --------- | ------ | ----------- |
| Adelie  | Biscoe    | FEMALE | 3150        |
| Adelie  | Dream     | FEMALE | 3150        |
| Adelie  | Biscoe    | FEMALE | 3150        |
| Adelie  | Torgersen | FEMALE | 3150        |
| Adelie  | Dream     | FEMALE | 3100        |
| Adelie  | Biscoe    | FEMALE | 3075        |
| Adelie  | Torgersen | FEMALE | 3050        |
| Adelie  | Torgersen | FEMALE | 3050        |
| Adelie  | Torgersen | FEMALE | 3050        |
| Adelie  | Dream     | FEMALE | 3050        |
| Adelie  | Dream     | FEMALE | 3000        |
| Adelie  | Dream     | FEMALE | 3000        |


- El resultado solo contiene pingüinos en el rango de peso establecido.
- El operador `between` permite especificar un conjunto de valores en una condición de filtrado. 
- Los rangos pueden ser de valores numéricos, cadenas de texto, fechas, tiempos, etc.
- El operador `between`puede usar índices eficientemente. Mejor rendimiento que la misma condición expresada con `>= AND <=`.

---

### Filtrar valores en una lista

Código SQL:
```sql
select distinct
	species,
	island,
	sex
from penguins
where island in ('Biscoe','Dream');
```

Resultado:

| species   | island | sex    |
| --------- | ------ | ------ |
| Adelie    | Biscoe | FEMALE |
| Adelie    | Biscoe | MALE   |
| Adelie    | Dream  | FEMALE |
| Adelie    | Dream  | MALE   |
| Adelie    | Dream  |        |
| Chinstrap | Dream  | FEMALE |
| Chinstrap | Dream  | MALE   |
| Gentoo    | Biscoe | FEMALE |
| Gentoo    | Biscoe | MALE   |
| Gentoo    | Biscoe |        |

- **IN** comprueba si un valor se encuentra en una lista de valores
- **NOT IN** excludes values but beware of NULL handling
- Mejora la legibilidad con respecto a múltiples condiciones OR.
- Puede tener un impacto negativo en el rendimiento con listas muy largas (evitar su uso en estos casos)

---

### Clasificar el resultado

Código SQL:
```sql
select
	species,
	island,
	sex,
	body_mass_g,
	case
		when body_mass_g < 3650 then 'light'
		when body_mass_g between 3650 and 4500 then 'medium_weight'
		when body_mass_g > 4500 then 'heavy'
		else 'unknown weight'
	end as weight_class
from penguins
limit 10;
```

Resultado:

| species | island    | sex    | body_mass_g | weight_class   |
| ------- | --------- | ------ | ----------- | -------------- |
| Adelie  | Torgersen | MALE   | 3750        | medium_weight  |
| Adelie  | Torgersen | FEMALE | 3800        | medium_weight  |
| Adelie  | Torgersen | FEMALE | 3250        | light          |
| Adelie  | Torgersen |        |             | unknown weight |
| Adelie  | Torgersen | FEMALE | 3450        | light          |
| Adelie  | Torgersen | MALE   | 3650        | medium_weight  |
| Adelie  | Torgersen | FEMALE | 3625        | light          |
| Adelie  | Torgersen | MALE   | 4675        | heavy          |
| Adelie  | Torgersen |        | 3475        | light          |
| Adelie  | Torgersen |        | 4250        | medium_weight  |

CASE WHEN te permite:
- **Categorizar y clasificar datos** dinámicamente
- **Transformar datos basándose en condiciones** durante la ejecución de la consulta
- **Implementar lógica IF-THEN-ELSE** directamente en SQL

---

### Ejercicio 2 - CASE WHEN

Escribe una consulta que devuelva las columnas especie (`species`),  isla (`island`), peso (`body_mass_g`) y una nueva columna `fixed_body_mass_kg` que sume 500g a los pingüinos avistados en la isla `Biscoe` y 750g a los de Torgersen y devuelva el valor en kilogramos. Limita el resultado a las 20 primeras filas.

Solución:
```sql

```

Resultado:

| species | island    | body_mass_g | fixed_body_mass_kg |
| ------- | --------- | ----------- | ------------------ |
| Adelie  | Torgersen | 3750        | 4.5                |
| Adelie  | Torgersen | 3800        | 4.55               |
| Adelie  | Torgersen | 3250        | 4                  |
| Adelie  | Torgersen |             |                    |
| Adelie  | Torgersen | 3450        | 4.2                |
| Adelie  | Torgersen | 3650        | 4.4                |
| Adelie  | Torgersen | 3625        | 4.375              |
| Adelie  | Torgersen | 4675        | 5.425              |
| Adelie  | Torgersen | 3475        | 4.225              |
| Adelie  | Torgersen | 4250        | 5                  |
| Adelie  | Torgersen | 3300        | 4.05               |
| Adelie  | Torgersen | 3700        | 4.45               |
| Adelie  | Torgersen | 3200        | 3.95               |
| Adelie  | Torgersen | 4400        | 5.15               |
| Adelie  | Torgersen | 4500        | 5.25               |
| Adelie  | Torgersen | 3325        | 4.075              |
| Adelie  | Torgersen | 4200        | 4.95               |
| Adelie  | Biscoe    | 3400        | 3.9                |
| Adelie  | Biscoe    | 3600        | 4.1                |
| Adelie  | Biscoe    | 3800        | 4.3                |

---

## Valores NULL

- Operaciones aritméticas con valores NULL
- Igualdad y desigualdad con valores NULL
- Lógica ternaria
- Manipular valores NULL de forma segura:
	- IS NULL
	- COALESCE
	- NULLIF

---

### Operaciones aritméticas con valores NULL

Código SQL:
```sql
select
    flipper_length_mm / 10.0 as flipper_cm,
    body_mass_g / 1000.0 as weight_kg,
    island as where_found
from penguins
limit 5;
```
Salida:

| flipper_cm | weight_kg | where_found |
|------------|-----------|-------------|
| 18.1       | 3.75      | Torgersen   |
| 18.6       | 3.8       | Torgersen   |
| 19.5       | 3.25      | Torgersen   |
|            |           | Torgersen   |
| 19.3       | 3.45      | Torgersen   |

- SQL utiliza un valor especial nulo (null) para representar datos no especificados
    - No es 0 ni una cadena vacía, sino “No sé”
- No se conoce la longitud de la aleta ni el peso corporal de uno de los primeros cinco pingüinos
- Hay que tener especial cuidado con estos valores cuando se usan operadores o funciones
	- “No sé” dividido entre 10 o 1000 es “No sé”

---

### Igualdad y desigualdad con valores NULL

Tomemos una consulta que devuelve valores nulos en la columna `sex`. Código SQL:
```sql
select distinct
    species,
    sex,
    island
from penguins
where island = 'Biscoe';
```
Salida:

| species |  sex   | island |
|---------|--------|--------|
| Adelie  | FEMALE | Biscoe |
| Adelie  | MALE   | Biscoe |
| Gentoo  | FEMALE | Biscoe |
| Gentoo  | MALE   | Biscoe |
| Gentoo  |        | Biscoe |

Si filtramos los datos obtenidos para obtener solo los pingüinos hembra (FEMALE), la fila sin valor en la columna sexo (sex) desaparece. Código SQL:
```sql
select distinct
    species,
    sex,
    island
from penguins
where island = 'Biscoe' and sex = 'FEMALE';
```
Salida:

| species |  sex   | island |
|---------|--------|--------|
| Adelie  | FEMALE | Biscoe |
| Gentoo  | FEMALE | Biscoe |

Pero es que si filtramos los datos obtenidos para obtener solo los pingüinos que NO son hembra (FEMALE), la fila sin valor en la columna sexo (sex) TAMBIÉN desaparece esa fila. Código SQL:
```sql
select distinct
    species,
    sex,
    island
from penguins
where island = 'Biscoe' and sex != 'FEMALE';
```
Salida:

| species | sex  | island |
|---------|------|--------|
| Adelie  | MALE | Biscoe |
| Gentoo  | MALE | Biscoe |

---

### Lógica ternaria

Código SQL:
```sql
select null = null;
```
Salida:

| null = null |
|-------------|
|             |

Código SQL:
```sql
select null != null;
```
Salida:

| null != null |
| ------------ |
|              |

- Si no conocemos los valores izquierdo y derecho, no sabemos si son iguales o no
- Por lo tanto, el resultado es nulo (null)
- Lógica ternaria de igualdad, sean X e Y dos valores cualesquiera:

|      | X     | Y     | null |
| ---- | ----- | ----- | ---- |
| X    | true  | false | null |
| Y    | false | true  | null |
| null | null  | null  | null | 

---

### Manipular valores nulos de forma segura

Código SQL:
```sql
select
    species,
    sex,
    island
from penguins
where sex is null;
```
Salida:

| species | sex |  island   |
|---------|-----|-----------|
| Adelie  |     | Torgersen |
| Adelie  |     | Torgersen |
| Adelie  |     | Torgersen |
| Adelie  |     | Torgersen |
| Adelie  |     | Torgersen |
| Adelie  |     | Dream     |
| Gentoo  |     | Biscoe    |
| Gentoo  |     | Biscoe    |
| Gentoo  |     | Biscoe    |
| Gentoo  |     | Biscoe    |
| Gentoo  |     | Biscoe    |

- Usa los operadores `is null` e `is not null` para manejar valores nulos de manera segura
- Otras partes de SQL manejan valores nulos de manera especial

---

### Ejercicio 3 - Valores nulos

Escribe una consulta para encontrar pingüinos (penguins) cuya masa corporal (body_mass_g) se conoce pero cuyo sexo (sex) se desconoce.

Solución:
```sql

```

Resultado:

| species | island    | bill_length_mm | bill_depth_mm | flipper_length_mm | body_mass_g | sex |
| ------- | --------- | -------------- | ------------- | ----------------- | ----------- | --- |
| Adelie  | Torgersen | 34.1           | 18.1          | 193               | 3475        |     |
| Adelie  | Torgersen | 42             | 20.2          | 190               | 4250        |     |
| Adelie  | Torgersen | 37.8           | 17.1          | 186               | 3300        |     |
| Adelie  | Torgersen | 37.8           | 17.3          | 180               | 3700        |     |
| Adelie  | Dream     | 37.5           | 18.9          | 179               | 2975        |     |
| Gentoo  | Biscoe    | 44.5           | 14.3          | 216               | 4100        |     |
| Gentoo  | Biscoe    | 46.2           | 14.4          | 214               | 4650        |     |
| Gentoo  | Biscoe    | 47.3           | 13.8          | 216               | 4725        |     |
| Gentoo  | Biscoe    | 44.5           | 15.7          | 217               | 4875        |     |

---

### Reemplaza NULL por un valor con significado

```sql
select 
	species,
	island,
	sex,
	coalesce(sex,'not available') as sex_report
from penguins
limit 10;
```

Resultado:

| species | island    | sex    | sex_report    | 
| ------- | --------- | ------ | ------------- |
| Adelie  | Torgersen | MALE   | MALE          |
| Adelie  | Torgersen | FEMALE | FEMALE        |
| Adelie  | Torgersen | FEMALE | FEMALE        |
| Adelie  | Torgersen |        | not available |
| Adelie  | Torgersen | FEMALE | FEMALE        |
| Adelie  | Torgersen | MALE   | MALE          |
| Adelie  | Torgersen | FEMALE | FEMALE        |
| Adelie  | Torgersen | MALE   | MALE          |
| Adelie  | Torgersen |        | not available |
| Adelie  | Torgersen |        | not available |

- Con esta función puedes reemplazar NULL en una consulta por un valor con significado.
- La función `COALESCE(x,y,...)` toma cualquier número de parámetros y devuelve el valor del primer parámetro que no es nulo (NULL).

- Beneficios:
	- **Evita la propagación de NULL** en los cálculos
	- **Proporciona alternativas elegantes** para datos faltantes
	- **Simplifica las sentencias CASE** que de otro modo verificarían NULL
	- **Mejora la completitud de los datos** en los reportes
	- **Habilita lógica flexible de valores predeterminados** sin condicionales complejos

---

### Reemplaza un valor por NULL

```sql
select 
	body_mass_g,
	round(flipper_length_mm/NULLIF(body_mass_g - 2700,0),1) as flipper_ratio
from penguins
where body_mass_g < 3000
order by flipper_ratio;
```

Resultado:

| body_mass_g | flipper_ratio |
| ----------- | ------------- |
| 2700        |               |
| 2975        | 0.7           |
| 2900        | 0.9           |
| 2900        | 0.9           |
| 2925        | 0.9           |
| 2900        | 0.9           |
| 2900        | 0.9           |
| 2850        | 1.2           |
| 2850        | 1.2           |

- La función `NULLIF(x, y)` devolverá NULL si el valor de x es el mismo que el de y, sino devolverá el valor x. 
	- Observa la primera fila, debería dar ERROR al dividir por cero
- En ocasiones, es conveniente reemplazar valores por un valor nulo:
	- Para **evitar divisiones por cero**
	- **Eliminar el uso de cadenas vacías** (u otros valores comodines) en los datos
	- Para evitar usar valores 0 en funciones de agregación (lo vemos más adelante)

- Comportamiento de SQLite no estándar: : `SELECT 10 / 0; -- Devuelve NULL, no un error!`

---

### Ejercicio 4 - Manejar valores nulos

Escribe una consulta que devuelva todas las columnas de pingüinos y añada una columna nueva (first_measure) que contenga el primer valor no nulo de las medidas de un pingüino en este orden: bill_length_mm, bill_depth_mm, flipper_length_mm, body_mass_g. Si todas tienen valor nulo, entonces devolverá el valor -1.

Solución:
```sql

```

Resultado:

| species | island    | bill_length_mm | bill_depth_mm | flipper_length_mm | body_mass_g | sex    | first_measure |
| ------- | --------- | -------------- | ------------- | ----------------- | ----------- | ------ | ------------- |
| Adelie  | Torgersen | 39.1           | 18.7          | 181               | 3750        | MALE   | 39.1          |
| Adelie  | Torgersen | 39.5           | 17.4          | 186               | 3800        | FEMALE | 39.5          |
| Adelie  | Torgersen | 40.3           | 18            | 195               | 3250        | FEMALE | 40.3          |
| Adelie  | Torgersen |                |               |                   |             |        | -1            |
| Adelie  | Torgersen | 36.7           | 19.3          | 193               | 3450        | FEMALE | 36.7          |
| Adelie  | Torgersen | 39.3           | 20.6          | 190               | 3650        | MALE   | 39.3          |
| Adelie  | Torgersen | 38.9           | 17.8          | 181               | 3625        | FEMALE | 38.9          |
| Adelie  | Torgersen | 39.2           | 19.6          | 195               | 4675        | MALE   | 39.2          |
| Adelie  | Torgersen | 34.1           | 18.1          | 193               | 3475        |        | 34.1          |
| Adelie  | Torgersen | 42             | 20.2          | 190               | 4250        |        | 42            |


---

## Funciones de agregación y cláusula de agrupación

- Funciones de agregación: sum, max, min, avg, count
- Agrupación de valores: group by
- Filtrar grupos
- Filtrar valores a agregar

---

### Funciones de agregación

Código SQL:
```sql
select sum(body_mass_g) as total_mass
from penguins;
```
Salida:

| total_mass |
|------------|
| 1437000.0  |

- Una función de agregación **combina muchos valores para producir un solo valor**
- La suma es una función de agregación o de grupo
- Combina los valores correspondientes de múltiples filas

---

### Funciones de agregación

Código SQL:
```sql
select
    max(bill_length_mm) as longest_bill,
    min(flipper_length_mm) as shortest_flipper,
    avg(bill_length_mm) / avg(bill_depth_mm) as weird_ratio
from penguins;
```
Salida:

| longest_bill | shortest_flipper |   weird_ratio    |
|--------------|------------------|------------------|
| 59.6         | 172.0            | 2.56087082530644 |

- `max`, `min`y `avg`son otras funciones de agregación en SQL
- En realidad, esto no debería funcionar: no se puede calcular el máximo ni el promedio si algún valor es nulo.
    - Consulta todos los valores de la tabla para comprobar la existencia de valores nulos
- SQL hace lo que es útil en lugar de lo que es correcto
	- Ignora las filas con valores nulos

>[!question] Pregunta
>¿Qué devolverá la función `avg` si todos los valores son nulos?

---

### Ejercicio 5 - Funciones agregación

¿Cuál es la masa corporal promedio de los pingüinos que pesan más de 3000,0 gramos?

Solución:
```sql

```

Resultado:

| avg(body_mass_g)  |
| ----------------- |
| 4245.015105740181 |

---

### Contar 

Código SQL:
```sql
select
    count(*) as count_star,
    count(sex) as count_specific,
    count(distinct sex) as count_distinct
from penguins;
```
Salida:

| count_star | count_specific | count_distinct |
|------------|----------------|----------------|
| 344        | 333            | 2              |

- `count(*)` cuenta las filas, tiene en cuenta los valores nulos
- `count(column)` cuenta las entradas (valores) no nulas de una columna
- `count(distinct column)` cuenta las entradas (valores) distintas no nulas de una columna

---

### Ejercicio 6 - Contar 

¿Cuántas masas corporales (body_mass_g) diferentes hay en el conjunto de datos de pingüinos?

Solución:
```sql

```

Resultado:
| body_distinct |
| ------------- |
| 94            |

---

### Agrupar

Código SQL:
```sql
select
    sex,
    avg(body_mass_g) as average_mass_g
from penguins
group by sex;
```
Salida:

|  sex   |  average_mass_g  |
|--------|------------------|
|        | 4005.55555555556 |
| FEMALE | 3862.27272727273 |
| MALE   | 4545.68452380952 |

- Coloca las filas en grupos según distintas combinaciones de valores en las columnas especificadas con `group by`
- Luego realiza la agregación por separado para cada grupo
- Todas las filas de cada grupo tienen el mismo valor para el sexo (`sex`), por lo que no es necesario agregarlas

---

### Agregación arbitraria

Código SQL:
```sql
select
    sex,
    body_mass_g
from penguins
group by sex;
```
Salida:

|  sex   | body_mass_g |
|--------|-------------|
|        |             |
| FEMALE | 3800.0      |
| MALE   | 3750.0      |

- Si no especificamos cómo agregar una columna, SQLite elige cualquier valor arbitrario del grupo
    - Todos los pingüinos de cada grupo tienen el mismo sexo porque los agrupamos por eso, por lo que obtenemos la respuesta correcta
    - Los valores de masa corporal están en los datos, pero son impredecibles
    - Es un error común
- Otros gestores de bases de datos no hacen esto
    - Por ejemplo, PostgreSQL se queja de que la columna debe usarse en una función de agregación

---

### Ejercicio 7 - Agrupar

Escriba una consulta que muestre cada masa corporal (body_mass_g) distinta en el conjunto de datos de pingüinos y la cantidad de pingüinos que pesan esa cantidad.

Solución:
```sql

```

Resultado:

| body_mass_g | number_same_weight |
| ----------- | ------------------ |
|             | 2                  |
| 2700        | 1                  |
| 2850        | 2                  |
| 2900        | 4                  |
| 2925        | 1                  |
| 2975        | 1                  |
| 3000        | 2                  |
| 3050        | 4                  |
| 3075        | 1                  |
| 3100        | 1                  |
| 3150        | 4                  |
| 3175        | 2                  |
| 3200        | 5                  |
| 3250        | 5                  |
| 3275        | 1                  |
| 3300        | 6                  |
| 3325        | 5                  |
| 3350        | 5                  |
| 3400        | 8                  |
| 3425        | 2                  |
| 3450        | 8                  |
| 3475        | 3                  |
| 3500        | 7                  |
| 3525        | 2                  |
| 3550        | 9                  |
| 3575        | 1                  |
| 3600        | 7                  |
| 3625        | 1                  |
| 3650        | 6                  |
| 3675        | 2                  |
| 3700        | 11                 |
| 3725        | 3                  |
| 3750        | 5                  |
| 3775        | 4                  |
| 3800        | 12                 |
| 3825        | 1                  |
| 3850        | 1                  |
| 3875        | 1                  |
| 3900        | 10                 |
| 3950        | 10                 |
| 3975        | 1                  |
| 4000        | 5                  |
| 4050        | 6                  |
| 4075        | 1                  |
| 4100        | 5                  |
| 4150        | 6                  |
| 4200        | 5                  |
| 4250        | 5                  |
| 4275        | 1                  |
| 4300        | 8                  |
| 4350        | 2                  |
| 4375        | 1                  |
| 4400        | 8                  |
| 4450        | 5                  |
| 4475        | 1                  |
| 4500        | 3                  |
| 4550        | 2                  |
| 4575        | 1                  |
| 4600        | 5                  |
| 4625        | 2                  |
| 4650        | 5                  |
| 4675        | 1                  |
| 4700        | 6                  |
| 4725        | 3                  |
| 4750        | 5                  |
| 4775        | 1                  |
| 4800        | 3                  |
| 4850        | 4                  |
| 4875        | 3                  |
| 4900        | 2                  |
| 4925        | 2                  |
| 4950        | 2                  |
| 4975        | 1                  |
| 5000        | 6                  |
| 5050        | 3                  |
| 5100        | 3                  |
| 5150        | 2                  |
| 5200        | 4                  |
| 5250        | 3                  |
| 5300        | 4                  |
| 5350        | 3                  |
| 5400        | 5                  |
| 5450        | 1                  |
| 5500        | 5                  |
| 5550        | 6                  |
| 5600        | 2                  |
| 5650        | 3                  |
| 5700        | 5                  |
| 5750        | 1                  |
| 5800        | 2                  |
| 5850        | 3                  |
| 5950        | 2                  |
| 6000        | 2                  |
| 6050        | 1                  |
| 6300        | 1                  |

---

### Filtrar grupos

Código SQL:
```sql
select
    sex,
    avg(body_mass_g) as average_mass_g
from penguins
group by sex
having average_mass_g > 4000.0;
```
Salida:

| sex  |  average_mass_g  |
|------|------------------|
|      | 4005.55555555556 |
| MALE | 4545.68452380952 |

- Se utiliza la condición de la cláusula `having` en vez de la condición del `where`
	- Recuerda que la condición del `where`se evalúa fila a fila
- Cláusula `having`
	- Filtra **grupos enteros** después de la agregación
	- Se aplica al resultado completo de `group by`
	- Afecta a qué grupos aparecen en el resultado final

----

### Filtrar valores a agregar

Código SQL:
```sql
select
    sex,
    round(
        avg(body_mass_g) filter (where body_mass_g < 4000.0),
        1
    ) as average_mass_g
from penguins
group by sex;
```
Salida:

|  sex   | average_mass_g |
|--------|----------------|
|        | 3362.5         |
| FEMALE | 3417.3         |
| MALE   | 3729.6         |

- `filter (where condition)` se aplica a las entradas
- La cláusula `filter` extiende las funciones agregadas (`sum`, `avg`, `count`, …) mediante una cláusula `where` adicional.
	- Filtra **valores a incluir en el cálculo de una función de agregación individual**
	- Se aplica **dentro** de cada función de agregación.
	- Afecta a qué valores se incluye en cada agregación concreta
	- Los grupos seguirán apareciendo en el resultado, aunque se filtren algunos valores agregados
- SQL:2003 introdujo la cláusula `filter` como parte de la característica opcional “Operaciones OLAP avanzadas” (T612). Actualmente, [apenas tiene soporte](https://modern-sql.com/feature/filter), pero es fácil de emular usando `case`.

---

### Ejercicio 8 - Filtrar valores agregados

Escriba una consulta que cuente el número de pingüinos de cada una de las siguientes categorías:
	- light si pesa menos de 3500g
	- heavy si pesa más de 4500g
	- medium_weight si pesa entre los valores anteriores

Solución:
```sql

```

Resultado:

| light | medium_weight | heavy |
| ----- | ------------- | ----- |
| 98    | 129           | 115   |

---

## Orden de ejecución

El orden de ejecución de las cláusulas de una consulta SQL SELECT sigue una secuencia lógica específica que es diferente del orden escrito. Este es el orden de ejecución lógica:

1. **FROM** - Determina las tablas/vistas fuente
2. **JOIN** - Combina tablas basándose en las condiciones de unión
3. **WHERE** - Filtra filas antes de agrupar
4. **GROUP BY** - Agrupa filas que comparten valores comunes
5. **HAVING** - Filtra grupos (aplicado después de GROUP BY)
6. **SELECT** - Determina qué columnas devolver
7. **DISTINCT** - Elimina filas duplicadas
8. **ORDER BY** - Ordena el conjunto de resultados
9. **LIMIT/TOP** - Restringe el número de filas devueltas

Este orden de ejecución explica varios comportamientos importantes de SQL:

- Por qué no puedes usar alias de columnas del SELECT en cláusulas WHERE (WHERE se ejecuta antes que SELECT)
- Por qué no puedes usar una función de agregación en cláusulas WHERE (WHERE se ejecuta antes que GROUP BY)
- Por qué se usa HAVING para filtrar grupos en lugar de WHERE (HAVING se ejecuta después de GROUP BY)
- Por qué puedes usar alias de columnas en ORDER BY (ORDER BY se ejecuta después de SELECT)
- Por qué las funciones de agregación no pueden usarse en cláusulas WHERE sin subconsultas

Por ejemplo, en esta consulta:

```sql
SELECT species, COUNT(*) as view_count
FROM penguins
WHERE sex = 'MALE'
GROUP BY species
HAVING COUNT(*) > 50
ORDER BY view_count DESC
LIMIT 2;
```

El sistema gestor de bases de datos primero filtra los pingüinos (`penguins`) que sean machos (`MALE`), después los agrupa por especie (`specie`), filtra los grupos con más de 50 avistamientos, selecciona las columnas, ordena las filas por número de avistamientos (`view_count`), y finalmente limita los resultados a 2.

---


## Fin de la lección

Enhorabuena has llegado al final de la sesión!

![[BD - Well done penguins.png]]

--- 