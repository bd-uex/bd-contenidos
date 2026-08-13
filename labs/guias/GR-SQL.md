# Guía de repaso: SQL (LAB01–LAB06)

> **Cómo usar esta guía.** Es un resumen para repasar, no sustituye a los laboratorios. Cada sección condensa los conceptos clave con un ejemplo mínimo. Al final tienes una chuleta general y preguntas de autoevaluación. Bases de datos de práctica: `penguins` y `empresa` en [SQL Snippets](https://i3lab.unex.es/sql-snippets/).

---

## 1. Fundamentos

- Una **base de datos** es una colección de datos que se pueden buscar y recuperar. Un **SGBD** (sistema gestor de bases de datos) es el programa que la gestiona.
- Un SGBD **relacional** (SGBDR) almacena los datos en **tablas** y se consulta con **SQL**.
- Dos arquitecturas: **cliente-servidor** (PostgreSQL, Oracle: el servidor es un proceso independiente) o **biblioteca embebida** (SQLite: funciones en C que se ejecutan dentro del proceso de la aplicación).
- **SQL** (*Structured Query Language*, ISO/IEC 9075) es un lenguaje **declarativo**: dices *qué* quieres, no *cómo* obtenerlo. Cada SGBD tiene su propio **dialecto**.
- Sublenguajes principales:
  - **LDD** (definición de datos): `CREATE`, `ALTER`, `DROP`.
  - **LMD** (manipulación de datos): `SELECT`, `INSERT`, `UPDATE`, `DELETE`.
- **SQLite** guarda cada base de datos en un único archivo y es el sistema de base de datos más desplegado del mundo (móviles, navegadores, aplicaciones). Es el SGBD que usamos en la asignatura.

---

## 2. Consultar datos: SELECT

**El resultado de un `SELECT` siempre es una tabla.**

```sql
select distinct              -- distinct elimina filas duplicadas
    species,
    body_mass_g / 1000.0 as weight_kg,   -- expresión con alias (as)
    species || ' - ' || island as what_where  -- concatenación
from penguins
where island = 'Biscoe'      -- filtra fila a fila
order by weight_kg desc      -- asc por defecto; mejor especificarlo
limit 10 offset 3;           -- 10 filas, saltando las 3 primeras
```

Puntos clave:

- `*` selecciona todas las columnas; también puedes listar columnas en cualquier orden (incluso repetidas).
- SQL trabaja con **multiconjuntos**: el resultado puede contener duplicados salvo que uses `distinct`.
- Las expresiones (`+ - * / %`, `round`, `abs`, `sqrt`, `ceil`, `floor`, `length`, `upper`, `lower`, `replace`, `||`…) se evalúan **fila a fila**. Ponles siempre un **alias** con `as`.
- Comillas **simples** para texto (`'Biscoe'`); comillas dobles solo para nombres de columna "raros".
- `limit`/`offset` no son estándar (varían entre SGBD).

### Filtrado con WHERE

| Herramienta | Uso | Ejemplo |
| --- | --- | --- |
| Comparación | `=  <  >  <=  >=  <>  !=` | `body_mass_g < 3000` |
| Booleanos | `and`, `or`, `not` | `island = 'Biscoe' and sex != 'MALE'` |
| Rango | `between x and y` (extremos incluidos) | `body_mass_g between 3000 and 3150` |
| Lista | `in (...)`, `not in (...)` | `island in ('Biscoe','Dream')` |
| Patrón | `like` con `_` (1 carácter) y `%` (0 o más) | `island like '_rea_'` |

### CASE WHEN: clasificar sobre la marcha

```sql
case
    when body_mass_g < 3650 then 'light'
    when body_mass_g between 3650 and 4500 then 'medium_weight'
    when body_mass_g > 4500 then 'heavy'
    else 'unknown weight'
end as weight_class
```

Implementa lógica *if-then-else* dentro de la consulta: sirve para categorizar, transformar valores según condiciones, y (combinado con agregación) para emular `filter`.

---

## 3. Valores NULL

`NULL` significa **"no sé"**: no es 0 ni la cadena vacía.

- Cualquier **operación aritmética con NULL da NULL** ("no sé" ÷ 10 = "no sé").
- Cualquier **comparación con NULL da NULL**, incluso `null = null`. Por eso SQL usa **lógica ternaria** (verdadero / falso / null) y una fila con NULL en la columna comparada **no pasa el filtro** ni con `= 'X'` ni con `!= 'X'`.
- Para tratar NULL de forma segura:

| Herramienta | Qué hace |
| --- | --- |
| `is null` / `is not null` | Única forma correcta de comprobar NULL en un filtro |
| `coalesce(x, y, ...)` | Devuelve el primer valor no nulo (para dar valores por defecto y evitar propagación) |
| `nullif(x, y)` | Devuelve NULL si `x = y`; útil para evitar divisiones por cero: `a / nullif(b, 0)` |

> ⚠️ Peculiaridad de SQLite: `select 10 / 0;` devuelve NULL en lugar de error.

---

## 4. Agregación y agrupación

Una **función de agregación** combina muchos valores en uno: `sum`, `avg`, `max`, `min`, `count`. **Ignoran los NULL** (si todos los valores son NULL, devuelven NULL).

Las tres formas de contar (¡pregunta típica de examen!):

```sql
select
    count(*)            as filas,          -- cuenta filas, NULL incluidos
    count(sex)          as valores,        -- cuenta valores NO nulos
    count(distinct sex) as distintos       -- cuenta valores distintos no nulos
from penguins;    -- 344, 333, 2
```

### GROUP BY y HAVING

```sql
select sex, avg(body_mass_g) as average_mass_g
from penguins
group by sex                       -- un grupo por cada valor de sex
having average_mass_g > 4000.0;    -- filtra GRUPOS (tras agrupar)
```

- `where` filtra **filas antes** de agrupar; `having` filtra **grupos después** de agrupar.
- Toda columna del `select` debe estar en el `group by` o dentro de una función de agregación. SQLite no protesta si no lo haces y devuelve un **valor arbitrario** del grupo (error común y silencioso); PostgreSQL directamente da error.
- `filter (where ...)` restringe qué valores entran en **una agregación concreta** sin eliminar grupos: `avg(body_mass_g) filter (where body_mass_g < 4000)`. Es estándar SQL:2003 pero con poco soporte fuera de SQLite/PostgreSQL; se puede emular con `case` dentro de la agregación.

### Orden de ejecución lógica (memorízalo)

```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

Explica por qué: no puedes usar alias del `select` ni agregaciones en el `where` (se ejecuta antes), sí puedes usar alias en `order by` (se ejecuta después), y los grupos se filtran con `having`.

---

## 5. Fechas y horas

SQLite **no tiene tipo fecha**: las almacena como `TEXT` (ISO 8601: `AAAA-MM-DD HH:MM:SS`), `REAL` (día juliano) o `INTEGER` (época Unix), y las manipula con funciones.

| Función | Devuelve |
| --- | --- |
| `current_date`, `current_time`, `current_timestamp` | Fecha/hora actual (estándar SQL) |
| `datetime(v)`, `date(v)`, `time(v)` | Fecha+hora, solo fecha, solo hora (en UTC; añade `'localtime'` para hora local) |
| `strftime('%Y', v)` | Parte formateada de la fecha (`%Y` año, `%m` mes, `%d` día…) |

Los **modificadores** encadenan aritmética de fechas:

```sql
date('now', '-1 year', '-6 months')                      -- hace 18 meses
date('2024-02-01', 'start of month', '+1 month', '-1 day')  -- último día del mes
```

`strftime` devuelve **texto**: usa `cast(... as integer)` para comparar numéricamente:

```sql
where cast(strftime('%m', fechaNac) as integer) > 6   -- nacidos en 2.º semestre
```

---

## 6. Consultas combinadas y CTEs

### Operadores de conjuntos

Combinan las **tablas resultado** de dos o más `SELECT` compatibles (mismo número de columnas, tipos compatibles por posición):

| Operador | Resultado |
| --- | --- |
| `UNION` | Unión, **sin** duplicados |
| `UNION ALL` | Unión, **con** duplicados (más rápido) |
| `INTERSECT` | Filas presentes en ambas |
| `EXCEPT` | Filas de la primera que no están en la segunda |

- Los nombres de columna los fija la **primera** consulta.
- `ORDER BY`/`LIMIT` solo al **final**, y se aplican al resultado combinado.
- Con tres o más consultas se evalúan de izquierda a derecha: `(A op B) op C`.

```sql
select dni from EMPLEADO
except
select director as dni from DEPARTAMENTO;   -- empleados que no dirigen
```

### CTEs: la cláusula WITH

Una **CTE** (*Common Table Expression*) es una consulta temporal con nombre, referenciable como si fuera una tabla. Mejora la legibilidad y modularidad de consultas complejas:

```sql
with resumen_dpto as (
    select dpto, count(*) as num_empleados, avg(sueldo) as sueldo_medio
    from empleado
    group by dpto
)
select * from resumen_dpto order by sueldo_medio desc;
```

Con `WITH RECURSIVE` (caso base + `UNION ALL` + caso recursivo) se recorren jerarquías, como la cadena empleado → supervisor → supervisor del supervisor…

---

## 7. JOIN: combinar filas de varias tablas

La primera decisión de toda consulta multi-tabla: **¿qué tablas necesito concatenar?** La condición de JOIN casi siempre es `tabla1.PK = tabla2.FK`.

| Tipo | Qué devuelve |
| --- | --- |
| `CROSS JOIN` | Producto cartesiano: cada fila con todas las demás (rara vez útil por sí solo) |
| `INNER JOIN ... ON` | Solo las combinaciones que **cumplen** la condición |
| `LEFT JOIN` | Coincidencias **+ todas las filas de la izquierda** (NULL en las columnas de la derecha) |
| `RIGHT JOIN` | Coincidencias **+ todas las filas de la derecha** |
| `FULL JOIN` | Coincidencias + no coincidentes de **ambos** lados |
| SELF JOIN | Un INNER JOIN de una tabla **consigo misma** (FK que referencia su propia PK, p. ej. supervisor); obliga a usar un **alias distinto** por instancia |

```sql
-- múltiples tablas: proyectos y quién trabaja en ellos
select P.nombre, E.nombre, E.apellido1
from PROYECTO P
    inner join TRABAJA_EN T on P.numero = T.proyecto
    inner join EMPLEADO E  on E.dni = T.empleado;
```

- La sintaxis antigua `FROM A, B WHERE A.x = B.y` (producto cartesiano filtrado) produce el mismo resultado que un `INNER JOIN`, pero está **en desuso**: mezcla condiciones de combinación y de selección.
- `LEFT/RIGHT/FULL OUTER JOIN` ≡ `LEFT/RIGHT/FULL JOIN` (la palabra `OUTER` es opcional).
- **Anti-join** (patrón, no palabra clave): encontrar filas **sin** correspondencia en la otra tabla:

```sql
-- empleados SIN familiares
select E.nombre
from EMPLEADO E left join FAMILIAR F on E.dni = F.empleado
where F.empleado is null;      -- LEFT JOIN + IS NULL
```

### Dos errores clásicos con OUTER JOIN

**1. `COUNT(*)` con LEFT JOIN.** Las filas sin coincidencia también son filas (con NULLs), así que `count(*)` devuelve 1 en lugar de 0. Solución: contar una **columna de la tabla derecha**, porque `count(columna)` ignora los NULL.

```sql
select E.nombre, count(F.nombre) as num_hijos   -- ✓ 0 para quien no tiene
from EMPLEADO E
left join FAMILIAR F on E.dni = F.empleado and F.relacion in ('Hijo','Hija')
group by E.dni, E.nombre;    -- buena práctica: agrupar por la PK
```

**2. Condición en `WHERE` en vez de en `ON`.** El `WHERE` se aplica **después** del JOIN y elimina las filas con NULL, convirtiendo silenciosamente el LEFT JOIN en un INNER JOIN. Si la condición debe restringir solo la tabla derecha, ponla **en el `ON`**.

```sql
-- ✗ where T.proyecto in (1,2)  → desaparecen los empleados sin esas horas
-- ✓ on E.dni = T.empleado and T.proyecto in (1,2)  → todos, con 0 si procede
```

---

## 8. Subconsultas

Una **subconsulta** es una consulta anidada dentro de otra, usable en `SELECT`, `FROM`, `WHERE` o `HAVING`. Puede devolver **un solo valor** (tabla 1×1) o **un conjunto de valores**.

### No correlacionadas

Se ejecutan **una sola vez**, de forma independiente de la consulta principal:

```sql
-- un valor: comparar con un agregado global
select nombre, sueldo from EMPLEADO
where sueldo > (select avg(sueldo) from EMPLEADO where dpto = 5);

-- conjunto de valores: con IN
select nombre from EMPLEADO
where dni in (select empleado from FAMILIAR
              group by empleado having count(*) > 1);

-- en el FROM: tabla derivada
select D.nombre, S.sueldoMedio
from DEPARTAMENTO D
inner join (select dpto, avg(sueldo) as sueldoMedio
            from EMPLEADO group by dpto) as S
      on D.numero = S.dpto;
```

### Correlacionadas

**Referencian columnas de la consulta externa**, así que se evalúan **una vez por cada fila** de la consulta principal (más expresivas, más costosas):

```sql
-- sueldo de cada empleado frente a la media de SU departamento
select E1.nombre, E1.sueldo,
       (select avg(E2.sueldo)
        from EMPLEADO E2
        where E2.dpto = E1.dpto) as sueldoMedioDpto   -- ← correlación
from EMPLEADO E1;

-- EXISTS: ¿tiene este empleado más de un familiar?
select nombre from EMPLEADO E
where exists (select 1 from FAMILIAR F
              where F.empleado = E.dni
              group by F.empleado having count(*) > 1);
```

### Operadores para subconsultas

| Operador | Verdadero si… | ¿SQLite? |
| --- | --- | :-: |
| `IN` / `NOT IN` | el valor está / no está en el conjunto | ✅ |
| `EXISTS` / `NOT EXISTS` | la subconsulta devuelve / no devuelve alguna fila | ✅ |
| `ANY` / `ALL` | la comparación se cumple para alguno / todos | ❌ |

En SQLite, `ANY`/`ALL` se emulan con agregados: `> ANY` → `> (select min…)`, `> ALL` → `> (select max…)`, etc.

### Subconsultas en UPDATE y DELETE

```sql
-- UPDATE con subconsulta en WHERE (a quién) y/o en SET (qué valor)
update EMPLEADO
set sueldo = sueldo * 1.30
where dni in (select empleado from TRABAJA_EN
              group by empleado having count(distinct proyecto) > 2);

delete from EMPLEADO
where dni not in (select empleado from FAMILIAR);
-- ⚠️ puede fallar si viola una FK que referencia a EMPLEADO
```

### ¿Subconsulta o JOIN?

Muchas consultas admiten ambas formas. Regla práctica: **JOIN** cuando necesitas columnas de varias tablas en el resultado y para grandes volúmenes (mejor optimización); **subconsulta** cuando comparas con un valor agregado (`avg`, `max`…) o cuando la lógica por pasos resulta más legible. Las correlacionadas son las más costosas (una ejecución por fila).

---

## 9. Definir la base de datos (LDD)

### Crear, modificar y borrar tablas

```sql
create table EMPLEADO (
    nombre     TEXT NOT NULL,
    dni        TEXT PRIMARY KEY,                          -- PK ⇒ NOT NULL
    sexo       TEXT NOT NULL CHECK (sexo IN ('M','F','O')),  -- enumerado
    sueldo     REAL NOT NULL CHECK (sueldo > 0),             -- subrango
    supervisor TEXT,
    dpto       INTEGER DEFAULT 1 NOT NULL,
    FOREIGN KEY (supervisor) REFERENCES EMPLEADO (dni)
        ON UPDATE CASCADE ON DELETE SET NULL,
    FOREIGN KEY (dpto) REFERENCES DEPARTAMENTO (numero)
        ON UPDATE CASCADE ON DELETE SET DEFAULT
) STRICT;
```

- `NOT NULL`, `CHECK (condición)` → **restricciones de dominio**; se verifican en cada inserción/modificación.
- `DROP TABLE [IF EXISTS] t` elimina tabla y contenido. `CREATE TABLE IF NOT EXISTS` evita el error de tabla duplicada.
- `ALTER TABLE t ADD columna ...` / `DROP columna` / `RENAME columna` / `RENAME TO nuevo_nombre`.
- Extensiones SQLite: `STRICT` activa el control estricto de tipos (INT, INTEGER, REAL, TEXT, BLOB, ANY); el esquema se consulta en la tabla `sqlite_schema`.

### Claves

- **`PRIMARY KEY`**: identifica de forma única cada fila (implica `NOT NULL`). Puede ser una columna o varias: `PRIMARY KEY (dpto, ubicacion)`. Sin PK, SQL permite filas duplicadas.
- **`UNIQUE`**: para las demás claves candidatas (claves alternativas).
- **Clave sustituta** (*surrogate*): sin significado en el dominio, solo identifica. `INTEGER PRIMARY KEY AUTOINCREMENT` genera el valor automáticamente (secuencia visible en `sqlite_sequence`; no se reinicia al borrar filas).
- **`FOREIGN KEY (col) REFERENCES tabla (pk)`**: garantiza la **integridad referencial** (el valor debe existir como PK en la tabla referenciada). En SQLite hay que activarla: `PRAGMA foreign_keys = ON;`.

### Acciones de integridad referencial

Con `ON UPDATE ...` / `ON DELETE ...` se indica qué hacer si cambia o se borra la PK referenciada:

| Acción | Efecto |
| --- | --- |
| `NO ACTION` (defecto) / `RESTRICT` | Rechaza la operación que viola la FK |
| `CASCADE` | Propaga el cambio/borrado a las filas dependientes |
| `SET NULL` | Pone la FK a NULL (la columna debe admitir NULL) |
| `SET DEFAULT` | Pone la FK a su valor por defecto (debe estar definido) |

### Asociaciones entre tablas (cardinalidad y opcionalidad)

La FK codifica el tipo de asociación:

| Asociación | Implementación |
| --- | --- |
| **1:1** | FK con `UNIQUE` (+ `NOT NULL` si es obligatoria); va en la entidad "menos importante" |
| **1:N** | FK en el lado **muchos** (+ `NOT NULL` si la participación es obligatoria) |
| **N:M** | Tabla intermedia con dos FK (p. ej. `TRABAJA_EN(empleado, proyecto, horas)`) |

### Vistas

```sql
create view empleado_senior as
select dni, sueldo, dpto from empleado where sueldo > 40000;
```

Una **vista** es una consulta almacenada con nombre: se re-ejecuta cada vez que se usa y puede compartirse entre consultas. Se elimina con `DROP VIEW`.

---

## 10. Manipular los datos (LMD)

```sql
-- INSERT: con lista de columnas (recomendado) o sin ella (orden de definición)
insert into DEPARTAMENTO (nombre, numero, director, fechaIngresoDirector)
values ('Investigación', 5, '333445555', '1988-05-22'),
       ('Administración', 4, '987654321', '1995-01-01');

-- UPDATE: modifica columnas de las filas que cumplen la condición
update departamento set numero = 4 where nombre like 'Adm%';

-- DELETE: borra filas
delete from departamento where nombre like '%ión';
```

> ⚠️ **En `UPDATE` y `DELETE`, casi siempre necesitas `WHERE`.** Sin él, afectas a **todas** las filas de la tabla. Recuerda además que ambas operaciones pueden ser rechazadas (o propagadas) por las restricciones de clave externa.

---

## 11. Chuleta general

```sql
-- Esqueleto completo de una consulta
SELECT [DISTINCT] columnas / expresiones [AS alias]
FROM tabla
    [INNER|LEFT|RIGHT|FULL] JOIN otra ON condición
WHERE condición_por_fila
GROUP BY columnas
HAVING condición_por_grupo
ORDER BY columnas [ASC|DESC]
LIMIT n [OFFSET m];
```

| Quiero… | Uso… |
| --- | --- |
| Quitar duplicados | `select distinct` |
| Filtrar filas | `where` (comparadores, `and/or/not`, `between`, `in`, `like`, `is null`) |
| Clasificar valores | `case when ... then ... else ... end` |
| Valor por defecto si NULL | `coalesce(x, defecto)` |
| Evitar división por cero | `nullif(divisor, 0)` |
| Resumir muchos valores | `sum / avg / max / min / count` + `group by` |
| Contar sin/con NULL | `count(*)` vs `count(col)` vs `count(distinct col)` |
| Filtrar grupos | `having` |
| Combinar resultados de consultas | `union [all] / intersect / except` |
| Nombrar una consulta intermedia | `with nombre as (...)` |
| Combinar filas de tablas | `join ... on PK = FK` |
| Conservar filas sin pareja | `left / right / full join` |
| Filas SIN pareja | `left join` + `where fk is null` (anti-join) |
| Comparar con un agregado | subconsulta: `where x > (select avg(...) ...)` |
| ¿Existe alguna fila relacionada? | `[not] exists (subconsulta correlacionada)` |
| Definir estructura | `create / alter / drop table`, `create view` |
| Garantizar datos válidos | `not null`, `check`, `primary key`, `unique`, `foreign key` |
| Modificar datos | `insert into`, `update ... set ... where`, `delete from ... where` |

---

## 12. Autoevaluación rápida

1. ¿Por qué `where` no puede usar un alias definido en el `select` pero `order by` sí?
2. ¿Qué devuelve `select null = null;` y por qué? ¿Cómo se filtran correctamente los NULL?
3. Sobre la tabla `penguins`: ¿qué diferencia hay entre `count(*)`, `count(sex)` y `count(distinct sex)`?
4. ¿Qué devuelve `avg(col)` si todos los valores de la columna son NULL?
5. ¿Cuál es la diferencia entre `where` y `having`? ¿Y entre `having` y `filter`?
6. ¿En qué se diferencian `union` y `union all`? ¿Qué requisitos deben cumplir las consultas combinadas?
7. ¿Por qué `count(*)` con `left join` devuelve 1 en lugar de 0 para las filas sin coincidencia? ¿Cómo se corrige?
8. ¿Qué le ocurre a un `left join` si pones en el `where` una condición sobre la tabla derecha?
9. Escribe el patrón anti-join para "empleados sin familiares" y su equivalente con `not in`.
10. ¿Qué diferencia una subconsulta correlacionada de una no correlacionada? ¿Cuál se ejecuta más veces?
11. ¿Cómo emularías `> ALL (subconsulta)` en SQLite?
12. ¿Qué implica declarar una columna `primary key`? ¿Para qué sirve `unique` en otra columna?
13. Explica las cuatro acciones de integridad referencial (`no action/restrict`, `cascade`, `set null`, `set default`) con un ejemplo de `on delete`.
14. ¿Cómo se implementa en SQL una asociación 1:1 obligatoria? ¿Y una N:M?
15. ¿Qué pasa si ejecutas `update empleado set sueldo = 0;` (sin `where`)?

---

## Apéndice: decisiones editoriales

- **Reordenación**: la guía agrupa por temas (consulta → NULL → agregación → fechas → combinadas → JOIN → subconsultas → LDD → LMD) en lugar de seguir el orden de las sesiones. El LDD/LMD (LAB03) se ha movido al final para que todo el bloque de consulta quede contiguo, que es lo que más se ejercita en examen; si prefieres mantener el orden de sesiones, basta reubicar las secciones 9–10 tras la 4.
- **Condensado**: se han fusionado los ejemplos de pingüinos (LAB01–02) y de la BD Empresa (LAB03–06) en ejemplos mínimos; se omiten las tablas de resultados largas.
- **Omitido por complejidad o especificidad**: las cajas de "sintaxis en desuso" con `(+)` (es sintaxis propietaria de Oracle, no aporta al repaso), los detalles de `julianday()`/`unixepoch()`, la tabla `sqlite_sequence` en detalle, y la mecánica interna de `ALTER TABLE` en SQLite. La cláusula `filter` y las CTE recursivas se mantienen pero de forma breve, como contenido avanzado.
- **Añadido**: el problema de `NOT IN` con NULL no se cubre en los laboratorios (ver informe de erratas); no se ha añadido aquí para no introducir contenido no impartido, pero encajaría en la sección 3 u 8.