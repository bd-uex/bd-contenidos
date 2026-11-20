# SELECT


---

## Objetivos

- Más operadores y CASE WHEN
- Valores nulos (null)
- Funciones de agregación de datos
- Agrupación
- Filtrado de agregaciones y agrupaciones

Seguimos trabajando con la base de datos reducida de la plataforma de streaming (`streaming_lab.sql`, tabla `cancion`).

---

## Ejercicio 1 - Repaso SELECT

Escribe una consulta que calcule y devuelva una columna llamada `que_donde` que tenga el género (`genero`) en letras mayúsculas y el país (`pais`) en letras minúsculas de cada canción separados por un solo espacio; y una columna llamada `porcentaje_me_gusta` que tenga el porcentaje de reproducciones que han acabado en un "me gusta" (`me_gusta` entre `reproducciones`, multiplicado por 100). Redondea el resultado para mostrar números con un solo decimal. El resultado solo debe incluir canciones que no estén en español. Ordena el resultado de forma descendente por `porcentaje_me_gusta`. Y limita el resultado a las 10 primeras filas.

Solución:
```sql
select Upper(genero) || " " || Lower(pais) as que_donde, round((me_gusta*100.0/reproducciones),1) as porcentaje_me_gusta 
from cancion
where Lower(idioma) != 'es'
order by porcentaje_me_gusta desc
limit 10
```

Resultado:

| que_donde           | porcentaje_me_gusta |
| ------------------- | ------------------- |
| POP reino unido     | 5.8                 |
| ROCK reino unido    | 5.6                 |
| ROCK reino unido    | 5.6                 |
| ROCK reino unido    | 5.6                 |
| ROCK estados unidos | 5.6                 |
| ROCK reino unido    | 5.4                 |
| POP reino unido     | 5.1                 |
| ROCK reino unido    | 5.0                 |
| POP reino unido     | 5.0                 |
| RAP estados unidos  | 5.0                 |

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
	titulo,
	genero,
	pais,
	anio
from cancion
where anio between 1975 and 1983
order by anio desc;
```
Resultado:

| titulo                     | genero | pais           | anio |
| -------------------------- | ------ | -------------- | ---- |
| Let's Dance                | Rock   | Reino Unido    | 1983 |
| Billie Jean                | Pop    | Estados Unidos | 1982 |
| Thriller                   | Pop    | Estados Unidos | 1982 |
| Under Pressure             | Rock   | Reino Unido    | 1981 |
| Another One Bites the Dust | Rock   | Reino Unido    | 1980 |
| Don't Stop Me Now          | Rock   | Reino Unido    | 1978 |
| We Will Rock You           | Rock   | Reino Unido    | 1977 |
| Heroes                     | Rock   | Reino Unido    | 1977 |
| Hotel California           | Rock   | Estados Unidos | 1977 |
| Somebody to Love           | Rock   | Reino Unido    | 1976 |
| Bohemian Rhapsody          | Rock   | Reino Unido    | 1975 |
| You're My Best Friend      | Rock   | Reino Unido    | 1975 |

- El resultado solo contiene canciones publicadas en el rango de años establecido (ambos extremos incluidos).
- El operador `between` permite especificar un conjunto de valores en una condición de filtrado.
- Los rangos pueden ser de valores numéricos, cadenas de texto, fechas, tiempos, etc.
- El operador `between`puede usar índices eficientemente. Mejor rendimiento que la misma condición expresada con `>= AND <=`.

---

### Filtrar valores en una lista

Código SQL:
```sql
select distinct
	genero,
	pais,
	idioma
from cancion
where pais in ('Reino Unido','España');
```
Resultado:

| genero | pais        | idioma |
| ------ | ----------- | ------ |
| Rock   | Reino Unido | EN     |
| Pop    | Reino Unido |        |
| Rock   | Reino Unido |        |
| Rock   | España      | ES     |
| Rock   | España      |        |
| Rock   | España      | EN     |
| Pop    | Reino Unido | EN     |
| Pop    | España      | ES     |
| Rap    | Reino Unido | EN     |
| Rap    | España      | ES     |
| Rap    | España      |        |

- **IN** comprueba si un valor se encuentra en una lista de valores
- **NOT IN** comprueba si un valor no se encuentra en una lista, cuidado con el manejo de los valores NULL
- Mejora la legibilidad con respecto a múltiples condiciones OR.
- Puede tener un impacto negativo en el rendimiento con listas muy largas (evitar su uso en estos casos)

---

### Clasificar el resultado

Código SQL:
```sql
select
	titulo,
	genero,
	idioma,
	duracion,
	case
		when duracion < 200 then 'corta'
		when duracion between 200 and 300 then 'media'
		when duracion > 300 then 'larga'
		else 'duracion desconocida'
	end as tipo_duracion
from cancion
limit 10;
```
Resultado:

| titulo                     | genero | idioma | duracion | tipo_duracion |
| -------------------------- | ------ | ------ | -------- | ------------- |
| Bohemian Rhapsody          | Rock   | EN     | 354      | larga         |
| Somebody to Love           | Rock   | EN     | 296      | media         |
| Under Pressure             | Rock   | EN     | 248      | media         |
| Blinding Lights            | Pop    | EN     | 200      | media         |
| Love Story                 | Pop    | EN     | 235      | media         |
| Love Story                 | Pop    |        | 190      | corta         |
| You're My Best Friend      | Rock   | EN     | 172      | corta         |
| Don't Stop Me Now          | Rock   | EN     | 209      | media         |
| We Will Rock You           | Rock   | EN     | 122      | corta         |
| Another One Bites the Dust | Rock   | EN     | 215      | media         |

CASE WHEN te permite:
- **Categorizar y clasificar datos** dinámicamente
- **Transformar datos basándose en condiciones** durante la ejecución de la consulta
- **Implementar lógica IF-THEN-ELSE** directamente en SQL

---

### Ejercicio 2 - CASE WHEN

Para emitir una canción en la radio hay que añadirle una cuña publicitaria. Escribe una consulta que devuelva las columnas título (`titulo`), país (`pais`), duración (`duracion`) y una nueva columna `duracion_radio_min` que sume 30 segundos a las canciones publicadas en `Reino Unido` y 45 segundos a las publicadas en `España` y devuelva el valor en minutos redondeado a dos decimales. El resultado no debe tener resultados repetidos y debe mostrar las canciones más largas primero. Limita el resultado a las 20 primeras filas.

Solución:
```sql
select distinct titulo, pais, duracion
case
	when Lower(pais) = "reino unido" then round((duracion+30)/60.0,2)
	when Lower(pais) = "españa" then round((duracion+45)/60.0,2)
end as duracion_radio_min
from cancion
order by duracion asc
limit 20
```

Resultado:

|          titulo          |      pais      | duracion | duracion_radio_min |
|--------------------------|----------------|----------|--------------------|
| Hey Jude                 | Reino Unido    | 431      | 7.68               |
| Hotel California         | Estados Unidos | 391      |                    |
| Heroes                   | Reino Unido    | 371      | 6.68               |
| Entre dos tierras        | España         | 369      | 6.9                |
| Thriller                 | Estados Unidos | 357      |                    |
| Sweet Child O' Mine      | Estados Unidos | 356      |                    |
| Bohemian Rhapsody        | Reino Unido    | 354      | 6.4                |
| Corazón partío           | España         | 349      | 6.57               |
| Like a Prayer            | Estados Unidos | 339      |                    |
| Lose Yourself            | Estados Unidos | 326      |                    |
| Space Oddity             | Reino Unido    | 315      | 5.75               |
| Copenhague               | España         | 313      | 5.97               |
| Stronger                 | Estados Unidos | 312      |                    |
| Smells Like Teen Spirit  | Estados Unidos | 301      |                    |
| So payaso                | España         | 300      | 5.75               |
| Somebody to Love         | Reino Unido    | 296      | 5.43               |
| Billie Jean              | Estados Unidos | 294      |                    |
| Without Me               | Estados Unidos | 290      |                    |
| Don't Look Back in Anger | Reino Unido    | 288      | 5.3                |
| Someone Like You         | Reino Unido    | 285      | 5.25               |

- Observa que las canciones de Estados Unidos no encajan en ningún `when` y, como no hay `else`, la nueva columna queda vacía (NULL).

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
    duracion / 60.0 as minutos,
    reproducciones / 1000000.0 as millones_reproducciones,
    pais as donde_publicada
from cancion
limit 6;
```
Salida:

| minutos            | millones_reproducciones | donde_publicada |
| ------------------ | ----------------------- | --------------- |
| 5.9                | 2154.0                  | Reino Unido     |
| 4.93333333333333  | 608.139                 | Reino Unido     |
| 4.13333333333333  | 44.11                   | Reino Unido     |
| 3.33333333333333 | 4312.0                  | Estados Unidos  |
| 3.91666666666667 | 201.611                 | Estados Unidos  |
| 3.16666666666667 |                         | Reino Unido     |

- SQL utiliza un valor especial nulo (null) para representar datos no especificados
    - No es 0 ni una cadena vacía, sino “No sé”
- No se conocen las reproducciones de una de las seis primeras canciones (la segunda *Love Story*, de la que solo sabemos la duración)
- Hay que tener especial cuidado con estos valores cuando se usan operadores o funciones
	- “No sé” dividido entre 60 o 1.000.000 es “No sé”

---

### Igualdad y desigualdad con valores NULL

Tomemos una consulta que devuelve valores nulos en la columna `idioma`. Código SQL:
```sql
select distinct
    genero,
    idioma,
    pais
from cancion
where pais = 'Estados Unidos';
```
Salida:

| genero | idioma | pais           |
| ------ | ------ | -------------- |
| Pop    | EN     | Estados Unidos |
| Rock   | EN     | Estados Unidos |
| Rock   |        | Estados Unidos |
| Pop    | ES     | Estados Unidos |
| Rap    | EN     | Estados Unidos |
| Rap    |        | Estados Unidos |
| Rap    | ES     | Estados Unidos |
| Pop    |        | Estados Unidos |

Si filtramos los datos obtenidos para obtener solo las canciones en español (ES), las filas sin valor en la columna idioma (`idioma`) desaparecen. Código SQL:
```sql
select distinct
    genero,
    idioma,
    pais
from cancion
where pais = 'Estados Unidos' and idioma = 'ES';
```
Salida:

| genero | idioma | pais           |
| ------ | ------ | -------------- |
| Pop    | ES     | Estados Unidos |
| Rap    | ES     | Estados Unidos |

Pero es que si filtramos los datos obtenidos para obtener solo las canciones que NO están en español (ES), las filas sin valor en la columna idioma (`idioma`) TAMBIÉN desaparecen. Código SQL:
```sql
select distinct
    genero,
    idioma,
    pais
from cancion
where pais = 'Estados Unidos' and idioma != 'ES';
```
Salida:

| genero | idioma | pais           |
| ------ | ------ | -------------- |
| Pop    | EN     | Estados Unidos |
| Rock   | EN     | Estados Unidos |
| Rap    | EN     | Estados Unidos |

---

### Lógica ternaria

Código SQL:
```sql
select null = null;
```
Salida:

| null = null |
|-------------|
|             |

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
    titulo,
    idioma,
    pais
from cancion
where idioma is null;
```
Salida:

| titulo             | idioma | pais           |
| ------------------ | ------ | -------------- |
| Love Story         |        | Reino Unido    |
| Space Oddity       |        | Reino Unido    |
| Everlong           |        | Estados Unidos |
| Copenhague         |        | España         |
| Rehab              |        | Reino Unido    |
| Stronger           |        | Estados Unidos |
| Basureta           |        | España         |
| Pista sin título 1 |        | Reino Unido    |
| Pista sin título 2 |        | Estados Unidos |

- Usa los operadores `is null` e `is not null` para manejar valores nulos de manera segura
- Otras partes de SQL manejan valores nulos de manera especial

---

### Ejercicio 3 - Valores nulos

Escribe una consulta para encontrar las canciones (`cancion`) cuya duración (`duracion`) se conoce pero cuyo idioma (`idioma`) se desconoce.

Solución:
```sql
select *
from cancion
where duracion is not null and idioma is null
```

Resultado:

| id_cancion | titulo       | genero | pais           | idioma | duracion | anio | reproducciones | me_gusta | valoracion |
| ---------- | ------------ | ------ | -------------- | ------ | -------- | ---- | -------------- | -------- | ---------- |
| 6          | Love Story   | Pop    | Reino Unido    |        | 190      |      |                |          |            |
| 12         | Space Oddity | Rock   | Reino Unido    |        | 315      | 1969 | 3146000        | 116362   | 3.2        |
| 24         | Everlong     | Rock   | Estados Unidos |        | 250      | 1997 | 790776000      | 32095119 | 3.6        |
| 29         | Copenhague   | Rock   | España         |        | 313      | 2008 | 1974000        | 43408    | 2.6        |
| 38         | Rehab        | Pop    | Reino Unido    |        | 215      | 2006 | 12830000       | 145535   | 3.2        |
| 60         | Stronger     | Rap    | Estados Unidos |        | 312      | 2007 | 5705000        | 103635   | 4.8        |
| 70         | Basureta     | Rap    | España         |        | 277      | 2016 | 13214000       | 188239   | 4.5        |

---

### Reemplaza NULL por un valor con significado

```sql
select
	titulo,
	pais,
	idioma,
	coalesce(idioma,'no disponible') as idioma_informe
from cancion
limit 12;
```
Resultado:

| titulo                     | pais           | idioma | idioma_informe |
| -------------------------- | -------------- | ------ | -------------- |
| Bohemian Rhapsody          | Reino Unido    | EN     | EN             |
| Somebody to Love           | Reino Unido    | EN     | EN             |
| Under Pressure             | Reino Unido    | EN     | EN             |
| Blinding Lights            | Estados Unidos | EN     | EN             |
| Love Story                 | Estados Unidos | EN     | EN             |
| Love Story                 | Reino Unido    |        | no disponible  |
| You're My Best Friend      | Reino Unido    | EN     | EN             |
| Don't Stop Me Now          | Reino Unido    | EN     | EN             |
| We Will Rock You           | Reino Unido    | EN     | EN             |
| Another One Bites the Dust | Reino Unido    | EN     | EN             |
| Heroes                     | Reino Unido    | EN     | EN             |
| Space Oddity               | Reino Unido    |        | no disponible  |

- Con esta función puedes reemplazar NULL en una consulta por un valor con significado.
- La función `COALESCE(x,y,...)` toma cualquier número de parámetros y devuelve el valor del primer parámetro que no es nulo (NULL).

- Beneficios:
	- **Evita la propagación de NULL** en los cálculos
	- **Proporciona alternativas elegantes** para datos faltantes
	- **Simplifica las sentencias CASE** que de otro modo verificarían NULL
	- **Mejora la completitud de los datos** en los informes
	- **Habilita lógica flexible de valores predeterminados** sin condicionales complejos

---

### Reemplaza un valor por NULL

```sql
select
	titulo,
	reproducciones,
	round(me_gusta * 100.0 / NULLIF(reproducciones, 0), 1) as porcentaje_me_gusta
from cancion
where reproducciones < 1000000
order by porcentaje_me_gusta;
```
Resultado:

| titulo          | reproducciones | porcentaje_me_gusta |
| --------------- | -------------- | ------------------- |
| Primer single   | 0              |                     |
| Corazón partío  | 734000         | 0.9                 |
| Yellow          | 369000         | 1.1                 |
| Like a Prayer   | 392000         | 2.0                 |
| Mala mujer      | 222000         | 2.4                 |
| Bodak Yellow    | 158000         | 3.6                 |
| Save Your Tears | 100000         | 3.8                 |
| Malamente       | 235000         | 4.3                 |
| Starboy         | 232000         | 4.9                 |

- La función `NULLIF(x, y)` devolverá NULL si el valor de x es el mismo que el de y, sino devolverá el valor x.
	- Observa la primera fila: *Primer single* es un estreno con 0 reproducciones, y debería dar ERROR al dividir por cero
- En ocasiones, es conveniente reemplazar valores por un valor nulo:
	- Para **evitar divisiones por cero**
	- **Eliminar el uso de cadenas vacías** (u otros valores comodines) en los datos
	- Para evitar usar valores 0 en funciones de agregación (lo vemos más adelante)

- Comportamiento de SQLite no estándar: : `SELECT 10 / 0; -- Devuelve NULL, no un error!`

---

### Ejercicio 4 - Manejar valores nulos

Escribe una consulta que devuelva todas las columnas de las canciones y añada una columna nueva (`primer_dato`) que contenga el primer valor no nulo de las cifras de una canción en este orden: `duracion`, `reproducciones`, `me_gusta`, `valoracion`. Si todas tienen valor nulo, entonces devolverá el valor -1. Ordena el resultado por `id_cancion` descendente (las fichas vacías son las últimas que se añadieron) y limita el resultado a las 10 primeras filas.

Solución:
```sql


```

Resultado:

| id_cancion | titulo                  | genero | pais           | idioma | duracion | anio | reproducciones | me_gusta | valoracion | primer_dato |
| ---------- | ----------------------- | ------ | -------------- | ------ | -------- | ---- | -------------- | -------- | ---------- | ----------- |
| 74         | Pista sin título 2      | Pop    | Estados Unidos |        |          |      |                |          |            | -1          |
| 73         | Pista sin título 1      | Rock   | Reino Unido    |        |          |      |                |          |            | -1          |
| 72         | Primer single           | Rock   | España         | ES     | 201      | 2026 | 0              | 0        |            | 201         |
| 71         | Efectos vocales         | Rap    | España         | ES     | 229      | 2005 | 320282000      | 16154999 | 4.6        | 229         |
| 70         | Basureta                | Rap    | España         |        | 277      | 2016 | 13214000       | 188239   | 4.5        | 277         |
| 69         | Quédate                 | Rap    | España         | ES     | 204      | 2022 | 1105000000     | 35714063 | 3.3        | 204         |
| 68         | Tú me dejaste de querer | Rap    | España         | ES     | 213      | 2020 | 1443000        | 48882    | 4.5        | 213         |
| 67         | Mala mujer              | Rap    | España         | ES     | 191      | 2017 | 222000         | 5295     | 3.4        | 191         |
| 66         | Woman                   | Rap    | Reino Unido    | EN     | 263      | 2021 | 5198000        | 156555   | 3.5        | 263         |
| 65         | Location                | Rap    | Reino Unido    | EN     | 243      | 2019 | 1618023000     | 26439006 | 4.6        | 243         |


---

### La trampa de NOT IN con valores NULL

Al principio de la lección vimos el operador `IN`. Ahora que conocemos la lógica ternaria, podemos entender un comportamiento sorprendente de su negación, `NOT IN`, cuando hay valores NULL de por medio.

```sql
select 2 in (1, 2, null) as dos_en_lista;
```

Resultado:

| dos_en_lista |
| ------------ |
| 1            | 

```sql
select 3 not in (1, 2, null) as tres_no_en_lista;
```

Resultado:

| tres_no_en_lista |
| ---------------- |
|                  | 

¿Por qué la segunda consulta devuelve NULL en vez de verdadero? Porque los operadores `IN` y `NOT IN` son azúcar sintáctico sobre comparaciones y lógica booleana:

- `x IN (a, b, NULL)` equivale a `x = a OR x = b OR x = NULL`
    - Si `x` coincide con algún valor de la lista, hay un `TRUE` y el `OR` devuelve `TRUE` (por eso la primera consulta funciona)
    - Si no coincide con ninguno, queda `FALSE OR FALSE OR NULL`, que es `NULL`
- `x NOT IN (a, b, NULL)` equivale a `x != a AND x != b AND x != NULL`
    - `x != NULL` siempre es `NULL` (lógica ternaria)
    - Por tanto, el `AND` **nunca puede ser `TRUE`**: como mucho será `NULL`

**Conclusión**: si la lista de un `NOT IN` contiene algún `NULL`, la condición nunca es verdadera y **la consulta no devuelve ninguna fila**. Y recuerda: las filas cuya condición se evalúa a `NULL` no pasan el filtro del `WHERE`.

---

### ### La trampa de NOT IN en acción

Comprobemos el efecto sobre la tabla `cancion` (47 canciones en inglés, 18 en español y 9 con idioma desconocido):

Código SQL:

```sql
select count(*) as no_ingles
from cancion
where idioma not in ('EN');
```

Salida:

| no_ingles |
| --------- |
| 18        | 

Código SQL:

```sql
select count(*) as no_ingles
from cancion
where idioma not in ('EN', null);
```

Salida:

| no_ingles |
| --------- |
| 0         | 

Observa dos cosas:

1. En la primera consulta, las 9 canciones de idioma desconocido **tampoco cuentan** (18, no 27): su comparación `NULL != 'EN'` es `NULL` y no pasan el filtro. Es el mismo comportamiento que ya vimos con `!=`.
2. En la segunda, basta **un solo NULL en la lista** para que el resultado sea vacío. Aquí el NULL está escrito a mano y salta a la vista pero, cuando veamos subconsultas, la lista del `NOT IN` vendrá calculada de otra tabla y el NULL puede colarse **sin que lo veas**. Es uno de los errores más difíciles de depurar en SQL.

**Cómo protegerse**:

- Si quieres incluir los NULL en el resultado, sé explícito: `where idioma not in ('EN') or idioma is null`
- O elimina los NULL antes de comparar: `where coalesce(idioma, 'desconocido') not in ('EN')`
- Cuando la lista venga de una subconsulta (lo veremos más adelante), garantiza que no contenga NULL (`... where columna is not null`) o usa `NOT EXISTS`

---

### Ejercicio 5 - NOT IN y NULL

Escribe una consulta que cuente las canciones que **no** están en inglés, contando también aquellas cuyo idioma se desconoce.

Solución:

```sql
```

Resultado:
 
| no_ingles |
| --------- |
| 27        | 

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
select sum(reproducciones) as total_reproducciones
from cancion;
```
Salida:

| total_reproducciones |
| -------------------- |
| 39592661000          |

- Una función de agregación **combina muchos valores para producir un solo valor**
- La suma es una función de agregación o de grupo
- Combina los valores correspondientes de múltiples filas

---

### Funciones de agregación

Código SQL:
```sql
select
    max(duracion) as mas_larga,
    min(duracion) as mas_corta,
    avg(me_gusta) / avg(reproducciones) as ratio_raro
from cancion;
```
Salida:

| mas_larga | mas_corta | ratio_raro         |
| --------- | --------- | ------------------ |
| 431       | 122       | 0.0376337567207215 | 

- `max`, `min`y `avg` son otras funciones de agregación en SQL
- En realidad, esto no debería funcionar: no se puede calcular el máximo ni el promedio si algún valor es nulo.
    - Consulta todos los valores de la tabla para comprobar la existencia de valores nulos (hay canciones sin duración ni reproducciones)
- SQL hace lo que es útil en lugar de lo que es correcto
	- Ignora las filas con valores nulos

>[!question] Pregunta
>¿Qué devolverá la función `avg` si todos los valores son nulos?

---

### Ejercicio 6 - Funciones agregación

¿Cuál es el número medio de reproducciones de las canciones que tienen más de un millón de reproducciones?

Solución:
```sql


```

Resultado:

| avg(reproducciones) |
| ------------------- |
| 638551919.354839   |

---

### Contar

Código SQL:
```sql
select
    count(*) as count_star,
    count(idioma) as count_specific,
    count(distinct idioma) as count_distinct
from cancion;
```
Salida:

| count_star | count_specific | count_distinct |
| ---------- | -------------- | -------------- |
| 74         | 65             | 2              |

- `count(*)` cuenta las filas, tiene en cuenta los valores nulos
- `count(column)` cuenta las entradas (valores) no nulas de una columna
- `count(distinct column)` cuenta las entradas (valores) distintas no nulas de una columna

---

### Ejercicio 7 - Contar

¿Cuántos años de publicación (`anio`) diferentes hay en el conjunto de datos de canciones?

Solución:
```sql


```

Resultado:

| anios_distintos |
| --------------- |
| 39              |

---

### Agrupar

Código SQL:
```sql
select
    idioma,
    avg(reproducciones) as media_reproducciones
from cancion
group by idioma;
```
Salida:

| idioma | media_reproducciones |
| ------ | -------------------- |
|        | 137940833.333333     |
| EN     | 691769319.148936     |
| ES     | 347325444.444444     | 

- Coloca las filas en grupos según distintas combinaciones de valores en las columnas especificadas con `group by`
- Luego realiza la agregación por separado para cada grupo
- Todas las filas de cada grupo tienen el mismo valor para el idioma (`idioma`), por lo que no es necesario agregarlas
- Observa que las canciones sin idioma forman **su propio grupo** (la primera fila): `group by` trata todos los nulos como si fueran el mismo valor

---

### Agregación arbitraria

Código SQL:
```sql
select
    idioma,
    reproducciones
from cancion
group by idioma;
```
Salida:

| idioma | reproducciones |
| ------ | -------------- |
|        |                |
| EN     | 2154000000     |
| ES     | 25164000       |

- Si no especificamos cómo agregar una columna, SQLite elige cualquier valor arbitrario del grupo
    - Todas las canciones de cada grupo tienen el mismo idioma porque las agrupamos por eso, por lo que obtenemos la respuesta correcta
    - Los valores de reproducciones están en los datos, pero son impredecibles
    - Es un error común
- Otros gestores de bases de datos no hacen esto
    - Por ejemplo, PostgreSQL se queja de que la columna debe usarse en una función de agregación

---

### Ejercicio 8 - Agrupar

Escribe una consulta que muestre cada año de publicación (`anio`) distinto en el conjunto de datos de canciones y la cantidad de canciones publicadas ese año.

Solución:
```sql


```

Resultado:

| anio | canciones_mismo_anio |
| ---- | -------------------- |
|      | 3                    |
| 1968 | 1                    |
| 1969 | 1                    |
| 1970 | 1                    |
| 1975 | 2                    |
| 1976 | 1                    |
| 1977 | 3                    |
| 1978 | 1                    |
| 1980 | 1                    |
| 1981 | 1                    |
| 1982 | 2                    |
| 1983 | 1                    |
| 1986 | 1                    |
| 1987 | 1                    |
| 1989 | 1                    |
| 1990 | 1                    |
| 1991 | 1                    |
| 1992 | 1                    |
| 1995 | 1                    |
| 1996 | 2                    |
| 1997 | 3                    |
| 2000 | 1                    |
| 2002 | 2                    |
| 2003 | 1                    |
| 2005 | 1                    |
| 2006 | 1                    |
| 2007 | 2                    |
| 2008 | 2                    |
| 2010 | 1                    |
| 2011 | 1                    |
| 2013 | 1                    |
| 2014 | 2                    |
| 2016 | 4                    |
| 2017 | 5                    |
| 2018 | 3                    |
| 2019 | 5                    |
| 2020 | 3                    |
| 2021 | 2                    |
| 2022 | 6                    |
| 2026 | 1                    |

---

### Filtrar grupos

Código SQL:
```sql
select
    idioma,
    avg(reproducciones) as media_reproducciones
from cancion
group by idioma
having media_reproducciones > 500000000.0;
```
Salida:

| idioma | media_reproducciones |
| ------ | -------------------- |
| EN     | 691769319.1489362    |

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
    idioma,
    round(
        avg(reproducciones) filter (where reproducciones < 100000000.0),
        1
    ) as media_reproducciones
from cancion
group by idioma;
```
Salida:

| idioma | media_reproducciones |
| ------ | -------------------- |
|        | 7373800.0            |
| EN     | 22238700.0           |
| ES     | 6413727.3            |

- `filter (where condition)` se aplica a las entradas
- La cláusula `filter` extiende las funciones agregadas (`sum`, `avg`, `count`, …) mediante una cláusula `where` adicional.
	- Filtra **valores a incluir en el cálculo de una función de agregación individual**
	- Se aplica **dentro** de cada función de agregación.
	- Afecta a qué valores se incluye en cada agregación concreta
	- Los grupos seguirán apareciendo en el resultado, aunque se filtren algunos valores agregados
- SQL:2003 introdujo la cláusula `filter` como parte de la característica opcional “Operaciones OLAP avanzadas” (T612). Actualmente, [apenas tiene soporte](https://modern-sql.com/feature/filter), pero es fácil de emular usando `case`.

---

### Ejercicio 9 - Filtrar valores agregados

Escribe una consulta que cuente el número de canciones de cada una de las siguientes categorías:
	- corta si dura menos de 200 segundos
	- larga si dura más de 300 segundos
	- media si dura entre los valores anteriores

Solución:
```sql


```

Resultado:

| corta | media | larga |
| ----- | ----- | ----- |
| 15    | 43    | 14    |

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
SELECT genero, COUNT(*) as num_canciones
FROM cancion
WHERE idioma = 'EN'
GROUP BY genero
HAVING COUNT(*) > 10
ORDER BY num_canciones DESC
LIMIT 2;
```
Resultado:

| genero | num_canciones |
| ------ | ------------- |
| Rock   | 22            |
| Pop    | 17            |

El sistema gestor de bases de datos primero filtra las canciones (`cancion`) que están en inglés (`EN`), después las agrupa por género (`genero`), filtra los grupos con más de 10 canciones, selecciona las columnas, ordena las filas por número de canciones (`num_canciones`), y finalmente limita los resultados a 2.

---


## Fin de la lección

Enhorabuena has llegado al final de la sesión!

<img src="http://1.bp.blogspot.com/-WolgaAXg06M/Tz-3rIN1p0I/AAAAAAAAG2M/ip75NZ1wOsM/s1600/Queen+Don%27t+Stop+Me+Now+en+comic+3.jpg" alt="Alt text" style="display: block; margin: 0 auto;" />

Fuente: [Diego's Tumblr](https://temblorxd.tumblr.com/)

---
