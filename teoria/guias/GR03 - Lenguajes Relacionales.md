# Tema 3 · Álgebra Relacional — Guía de repaso

> **Objetivo de esta guía**: repasar de forma ágil los conceptos fundamentales del álgebra relacional y su correspondencia con SQL. Para practicar, usa la [calculadora RelaX con la BD Empresa](https://dbis-uibk.github.io/relax/calc/gist/5e3b094713c94df48e314477b0b1945b).

---

## 1. ¿Qué es el álgebra relacional y por qué estudiarla?

El **álgebra relacional** es el conjunto básico de operaciones del modelo relacional: métodos simples pero potentes para **construir nuevas relaciones a partir de relaciones existentes**.

Tres ideas clave:

1. **Es un álgebra cerrada**: toda operación toma relaciones como entrada y devuelve **una relación** como resultado. Por eso los resultados pueden encadenarse en nuevas operaciones.
2. **Una secuencia de operaciones forma una expresión** de álgebra relacional, cuyo resultado también es una relación (el resultado de la consulta).
3. **SQL la lleva en su núcleo**: los SGBD no ejecutan SQL directamente; primero traducen cada consulta a álgebra relacional (o una representación interna equivalente). Entender el álgebra es entender cómo "piensa" un SGBD.

### Mapa de operaciones

| Grupo | Operación | Símbolo | En SQL (esencia) |
|---|---|---|---|
| **Unarias** | Selección | σ (sigma) | `WHERE` |
| | Proyección | π (pi) | `SELECT DISTINCT` |
| | Renombrado | ρ (rho) | `AS` / alias |
| **Teoría de conjuntos** | Unión | ∪ | `UNION` |
| | Intersección | ∩ | `INTERSECT` |
| | Diferencia | − | `EXCEPT` |
| | Producto cartesiano | × | `CROSS JOIN` |
| **Binarias** | Concatenación (join) | ⋈ | `INNER JOIN` |
| | Concatenación externa | ⟕ ⟖ ⟗ | `LEFT/RIGHT/FULL OUTER JOIN` |
| | División | ÷ | (no directa; subconsultas) |
| **Adicionales** | Agrupamiento | γ (gamma) | `GROUP BY` + agregadas |
| | Ordenación | τ (tau) | `ORDER BY` |

---

## 2. Operaciones unarias (una sola relación)

### 2.1. Selección — σ (filtra FILAS)

$$\sigma_{<condición>}(R)$$

Devuelve las **tuplas de R que cumplen la condición** (partición **horizontal**). Las que no la cumplen se descartan.

**Ejemplo**: empleados del departamento 4 → `sigma Dno=4 (Empleado)`

```sql
SELECT * FROM Empleado WHERE Dno = 4;
```

**Propiedades que debes recordar**:
- El resultado tiene el **mismo esquema** que R (mismas columnas).
- Es **conmutativa**: σ₁(σ₂(R)) = σ₂(σ₁(R)).
- Una cadena de selecciones equivale a **una sola con AND**: σ₁(σ₂(R)) = σ₍₁ ∧ ₂₎(R).
- El resultado tiene **≤ tuplas** que R.

### 2.2. Proyección — π (selecciona COLUMNAS)

$$\pi_{lista\ de\ atributos}(R)$$

Devuelve **solo los atributos indicados** (partición **vertical**) y **elimina duplicados** (una relación es un conjunto, no admite tuplas repetidas).

**Ejemplo**: nombre, apellido y sueldo → `pi Apellido1, Nombre, Sueldo (Empleado)`

```sql
SELECT DISTINCT Apellido1, Nombre, Sueldo FROM Empleado;
```

**Propiedades**:
- Resultado con **≤ tuplas** que R. Si la lista incluye una **clave** de R → mismo número de tuplas (no puede haber duplicados).
- **No es conmutativa**.

> ⚠️ **Conjuntos vs. multiconjuntos**: en teoría, las relaciones son *conjuntos* (sin duplicados). En la práctica, **SQL trabaja con multiconjuntos (bags)** por eficiencia: `SELECT` no elimina duplicados salvo que uses `DISTINCT`. Este matiz cae mucho en exámenes.

### 2.3. Renombrado — ρ

Cambia el nombre de la **relación**, de sus **atributos**, o de ambos.

| Caso | Álgebra | RelaX | SQL |
|---|---|---|---|
| Relación | ρ_S(R) | `rho S (R)` | `FROM R S` |
| Atributos | ρ_{b1,…,bn}(R) | `rho b1<-a1, b2<-a2 (R)` | `SELECT a1 AS b1, …` |

Es imprescindible cuando combinamos **una relación consigo misma** (p. ej., empleados y sus supervisores) para distinguir las dos copias.

### Encadenando operaciones: resultados intermedios

Puedes escribir una expresión anidada o dividirla en pasos con nombre (en RelaX, la asignación se hace con `=`):

$$\pi_{Nombre, Apellido1, Sueldo}(\sigma_{Dno=5}(Empleado))$$

```
Dep5_Emps = sigma Dno=5 (Empleado)
Resultado = pi Nombre, Apellido1, Sueldo (Dep5_Emps)
```

Los pasos intermedios hacen las consultas complejas mucho más legibles.

---

## 3. Operaciones de la teoría de conjuntos

### 3.1. Unión, intersección y diferencia

| Operación | Resultado de R op S | ¿Conmutativa? |
|---|---|---|
| **R ∪ S** | tuplas en R, en S o en ambas (sin duplicados) | Sí (y asociativa) |
| **R ∩ S** | tuplas que están en **ambas** | Sí (y asociativa) |
| **R − S** | tuplas en R que **no** están en S | **No** (R−S ≠ S−R) |

**Requisito**: R y S deben ser **compatibles en tipos**: mismo número de atributos y dominios compatibles atributo a atributo. El resultado toma los nombres de atributos de R (el primer operando).

```sql
SELECT * FROM R  UNION      SELECT * FROM S;
SELECT * FROM R  INTERSECT  SELECT * FROM S;
SELECT * FROM R  EXCEPT     SELECT * FROM S;
```

**Ejemplo clásico**: DNI de quienes trabajan en el departamento 5 **o** supervisan a alguien de ese departamento:

```
Dep5 = sigma Dno=5 (Empleado)
pi Dni (Dep5) union pi SuperDni (Dep5)
```

💡 **Truco de repaso**: la diferencia sirve para expresar negaciones del tipo "los que NO…". Ejemplo: *empleados que no dirigen ningún departamento* → π_Dni(Empleado) − π_DniDirector(Departamento).

### 3.2. Producto cartesiano — ×

$$R(A_1,\dots,A_n) \times S(B_1,\dots,B_m)$$

- Combina **cada tupla de R con cada tupla de S**: si |R| = nR y |S| = nS, el resultado tiene **nR × nS tuplas** y **n + m atributos**.
- **No requiere** compatibilidad de tipos.
- Por sí solo **no suele ser significativo** (mezcla tuplas sin criterio): casi siempre va seguido de una **selección** que filtra las combinaciones que realmente están relacionadas.

```sql
SELECT R.*, S.* FROM R CROSS JOIN S;
```

Esa pareja "× seguido de σ" es tan frecuente que tiene nombre propio: **JOIN**. 👇

---

## 4. Operaciones relacionales binarias

### 4.1. Concatenación (INNER JOIN) — ⋈

$$R \Join_{<condición>} S \;=\; \sigma_{<condición>}(R \times S)$$

Combina **solo** las parejas de tuplas que **satisfacen la condición de unión**. Es la operación esencial para consultar bases de datos con varias tablas.

**Ejemplo**: nombre del director de cada departamento:

```
Temp = Departamento join DniDirector=Dni Empleado
pi NombreDpto, Nombre (Temp)
```

```sql
SELECT D.NombreDpto, E.Nombre
FROM Departamento D INNER JOIN Empleado E ON (D.DniDirector = E.Dni);
```

**Variantes que debes distinguir**:

| Variante | Condición | Particularidad |
|---|---|---|
| **Theta-join** | Cualquier expresión booleana (=, <, >, …) | Caso general |
| **Equijoin** | Solo igualdades | El resultado repite columnas con valores idénticos |
| **Natural join** (R ∗ S) | Igualdad implícita sobre los atributos con el **mismo nombre** | Elimina la columna duplicada del resultado |

```sql
-- Natural join, dos formas equivalentes:
SELECT * FROM Departamento NATURAL JOIN Localizaciones_Dpto;
SELECT * FROM Departamento INNER JOIN Localizaciones_Dpto USING (NumeroDpto);
```

> 📌 **Conjunto completo**: {σ, π, ρ, ∪, −, ×} basta para expresar cualquier otra operación. Por ejemplo:
> - R ∩ S = (R ∪ S) − ((R − S) ∪ (S − R))
> - R ⋈_c S = σ_c(R × S)

### 4.2. Concatenación externa (OUTER JOIN) — ⟕ ⟖ ⟗

Un INNER JOIN **descarta** las tuplas sin pareja (o con nulos en los atributos de unión) → pérdida de información. Los OUTER JOIN la evitan **rellenando con NULL** donde no hay coincidencia:

| Operación | Conserva todas las tuplas de… | SQL |
|---|---|---|
| **LEFT** ⟕ | la relación **izquierda** (R) | `R LEFT [OUTER] JOIN S` |
| **RIGHT** ⟖ | la relación **derecha** (S) | `R RIGHT [OUTER] JOIN S` |
| **FULL** ⟗ | **ambas** | `R FULL [OUTER] JOIN S` |

**Ejemplo**: listar **todos** los empleados y, si dirigen algo, el nombre del departamento:

```
Temp = Empleado left outer join Dni=DniDirector Departamento
pi Nombre, Apellido1, NombreDpto (Temp)
```

Los empleados que no son directores aparecen con `NombreDpto = null`, en vez de desaparecer del resultado.

### 4.3. División — ÷

$$R(Z) \div S(X), \quad X \subset Z$$

Responde a consultas del tipo **"…para TODOS los…"**: una tupla t (sobre los atributos Y = Z − X) aparece en el resultado si en R está combinada con **todas y cada una** de las tuplas de S.

**Ejemplo**: empleados que trabajan en **todos** los proyectos en los que trabaja algún 'Pérez':

```
DNI_PNOS   = pi DniEmpleado, NumProy (Trabaja_En)
PEREZ_PNOS = pi NumProy (Trabaja_En join DniEmpleado=Dni (sigma Apellido1='Perez' (Empleado)))
DNI_PNOS division PEREZ_PNOS
```

💡 Si en el enunciado aparece "todos/todas", piensa en división.

---

## 5. Operaciones adicionales

El álgebra básica no puede expresar cálculos matemáticos sobre colecciones de valores. Para eso existen:

### 5.1. Funciones de agregación

| Función | Números | Cadenas | Fechas |
|---|:---:|:---:|:---:|
| COUNT(*) / COUNT(atr) | ✔ | ✔ | ✔ |
| MIN / MAX | ✔ | ✔ | ✔ |
| SUM / AVG | ✔ | ✘ | ✘ |

> ⚠️ `COUNT(*)` cuenta **todas las tuplas**; `COUNT(atributo)` cuenta solo los valores **no NULL** de ese atributo. Otra pregunta clásica de examen.

### 5.2. Agrupamiento — γ

$$\gamma_{<atributos\ agrupación>;\ <funciones\ agregación>}(R)$$

Agrupa las tuplas por los atributos indicados y aplica funciones agregadas a cada grupo. Si no se indican atributos de agrupación, **toda la relación es un único grupo**.

**Ejemplo**: número de empleados y sueldo medio por departamento:

```
gamma Dno; COUNT(Dni) -> num_emps, AVG(Sueldo) -> sueldo_medio (Empleado)
```

```sql
SELECT Dno, COUNT(Dni) AS num_emps, AVG(Sueldo) AS sueldo_medio
FROM Empleado
GROUP BY Dno;
```

### 5.3. Ordenación — τ

$$\tau_{atributo_1\ [ASC|DESC],\ \dots}(R)$$

**Ejemplo**: `tau NombreDpto DESC (Departamento)` ≡ `SELECT * FROM Departamento ORDER BY NombreDpto DESC;`

---

## 6. Expresando restricciones con álgebra relacional

El álgebra no solo sirve para consultar: también permite **especificar restricciones de integridad**. Hay dos formas equivalentes:

1. **R = ∅** → "el resultado de R debe estar vacío" (no debe existir ninguna tupla que viole la regla).
2. **R ⊆ S** → "toda tupla de R debe estar también en S".

Son intercambiables: R ⊆ S equivale a R − S = ∅.

### 6.1. Integridad referencial

"Todo valor del atributo A de R debe existir como valor del atributo B de S":

$$\pi_A(R) \subseteq \pi_B(S) \quad\Longleftrightarrow\quad \pi_A(R) - \pi_B(S) = \emptyset$$

**Ejemplo**: todo `DniEmpleado` de Familiar debe existir como `Dni` en Empleado.

### 6.2. Restricciones de clave

"Si dos tuplas coinciden en la clave, coinciden en todo". Para `numero` clave de Departamento, no puede haber dos tuplas con igual `numero` y distinto `nombre`:

$$\sigma_{D1.numero=D2.numero \,\land\, D1.nombre \neq D2.nombre}(D1 \times D2) = \emptyset$$

donde D1 y D2 son **renombrados** (ρ) de Departamento — necesarios porque hacemos el producto de la relación **consigo misma**.

### 6.3. Otras restricciones

- **Dominio enumerado**: los únicos valores válidos de `sexo` son 'F', 'M', 'O':
  $$\sigma_{sexo \neq 'F' \land sexo \neq 'M' \land sexo \neq 'O'}(Empleado) = \emptyset$$
- **Regla de negocio**: todo director debe cobrar más de 30000:
  $$\sigma_{Sueldo<30000}(Empleado \Join_{Dni=DniDirector} Departamento) = \emptyset$$
  o, de forma equivalente:
  $$\pi_{DniDirector}(Departamento) \subseteq \pi_{Dni}(\sigma_{Sueldo \geq 30000}(Empleado))$$

---

## ✅ Checklist de autoevaluación

Antes del examen deberías poder responder sin dudar:

- [ ] ¿Por qué se dice que el álgebra relacional es *cerrada* y qué permite eso?
- [ ] ¿Qué diferencia hay entre σ (filas) y π (columnas)? ¿Cuál elimina duplicados y por qué?
- [ ] ¿Por qué SQL trabaja con multiconjuntos y qué implica en `SELECT` sin `DISTINCT`?
- [ ] ¿Qué exige la compatibilidad de tipos y qué operaciones la requieren?
- [ ] ¿Por qué R − S ≠ S − R, mientras que ∪ e ∩ sí son conmutativas?
- [ ] ¿Cómo se define un JOIN a partir de × y σ? ¿Qué distingue theta-join, equijoin y natural join?
- [ ] ¿Cuándo necesitas un OUTER JOIN en lugar de un INNER JOIN? ¿Qué papel juegan los NULL?
- [ ] ¿Qué tipo de consulta resuelve la división? (pista: "para **todos** los…")
- [ ] ¿Qué diferencia hay entre COUNT(*) y COUNT(atributo)?
- [ ] ¿Cómo se expresa una restricción de integridad referencial con π y ⊆ (o −)?

## 🎯 Chuleta final: del enunciado a la operación

| Si el enunciado dice… | Piensa en… |
|---|---|
| "los empleados que cumplen…" | σ (selección) |
| "muestra solo el nombre y…" | π (proyección) |
| "…o…" (unir resultados) | ∪ |
| "…que NO…" | − (diferencia) |
| "combinando datos de dos tablas relacionadas" | ⋈ (join) |
| "todos los X, aunque no tengan Y" | outer join |
| "…que participan en TODOS los…" | ÷ (división) |
| "cuántos / media / total … por cada…" | γ (agrupamiento) |
| "ordenados por…" | τ (ordenación) |