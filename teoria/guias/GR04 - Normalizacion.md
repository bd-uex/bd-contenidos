# Tema 4 — Diseño lógico y Normalización
## Guía de repaso condensada

> **Objetivo del tema**: aprender a detectar diseños relacionales problemáticos (redundancia, anomalías, tuplas espurias) y a corregirlos mediante la **normalización**, apoyándonos en el concepto de **dependencia funcional**.

---

## 1. ¿Qué es el diseño lógico?

El diseño lógico consiste en **construir el esquema de la base de datos usando un modelo de datos concreto** (el relacional, en nuestro caso), pero **independiente del SGBD** que se vaya a usar y de cualquier consideración física.

Ideas clave:
- Es un **proceso iterativo**: se parte de un primer esquema y se refina.
- Su resultado es el **esquema lógico**: el conjunto de relaciones (tablas) de la base de datos, que buscaremos tener **al menos en tercera forma normal (3FN)**.
- Un buen esquema lógico es la base de todo lo demás: vistas de usuario, integridad, rendimiento y capacidad de evolucionar ante cambios futuros.

---

## 2. Las 4 directrices de un buen diseño relacional

Antes de la teoría formal, cuatro reglas informales que debe cumplir todo buen esquema. Cada una responde a un problema concreto:

| # | Directriz | Problema que evita |
|---|-----------|--------------------|
| 1 | Cada relación representa **una sola entidad** | Semántica confusa |
| 2 | Cada hecho se almacena **una sola vez** | Redundancia y anomalías |
| 3 | **Minimizar los valores NULL** | Desperdicio, errores en agregaciones |
| 4 | Los JOIN no deben crear **tuplas espurias** | Datos falsos |

### Directriz 1 — Semántica clara de los atributos

Un esquema de relación debe corresponderse con **un solo tipo de entidad** (o relación entre entidades). No se deben mezclar atributos de EMPLEADOS, DEPARTAMENTOS y PROYECTOS en la misma tabla; solo las **claves externas** hacen referencia a otras entidades.

**Prueba rápida**: si no puedes explicar en una frase qué representa cada fila de la tabla, el diseño es sospechoso.

### Directriz 2 — Evitar redundancia y anomalías de actualización

La información redundante desperdicia espacio y, sobre todo, provoca **anomalías**. Considera la relación mal diseñada:

$EMP\_PROY$(<u>Dni, NumProyecto</u>, NombreE, NombreP, Horas)

donde el nombre del proyecto se repite en cada fila de cada empleado asignado:

- **Anomalía de actualización**: renombrar el proyecto 1 obliga a modificar las 100 filas de sus 100 empleados. Si se olvida una, la base de datos queda inconsistente.
- **Anomalía de inserción**: no se puede registrar un proyecto nuevo hasta que tenga algún empleado asignado (la clave no admite NULL).
- **Anomalía de eliminación**: si se borra al último empleado de un proyecto, desaparece también la información del proyecto.

**Regla de oro**: representar cada hecho **una sola vez**.

### Directriz 3 — Minimizar los valores NULL

Los NULL causan problemas prácticos: desperdician espacio, distorsionan las funciones de agregación (`COUNT`, `AVG`...) y producen comparaciones impredecibles en `SELECT` y `JOIN`. Además son ambiguos: ¿el valor *no aplica*, *se desconoce* o *existe pero no está disponible*?

**Solución**: si un atributo es NULL para la mayoría de las tuplas, sácalo a una relación aparte. Ejemplo: si solo el 10% de los empleados tiene oficina propia, no añadas `NumOficina` a EMPLEADO; crea $OFICINAS\_EMPS$(<u>DniEmpleado</u>, NumOficina) solo con los empleados que la tienen.

### Directriz 4 — Prohibir las tuplas espurias

Si descompones mal una relación, al reconstruirla con un JOIN puedes obtener **más filas de las que había**: combinaciones que nunca existieron (**tuplas espurias**).

**Ejemplo.** Tabla original:

| Empleado | Proyecto | Gerente |
|----------|----------|---------|
| Ana      | X        | Luis    |
| Ana      | Y        | María   |
| Carlos   | X        | Luis    |

Descomposición **incorrecta** en T1(Empleado, Proyecto) y T2(Empleado, Gerente). Al hacer el JOIN por `Empleado`:

| Empleado | Proyecto | Gerente | |
|----------|----------|---------|---|
| Ana      | X        | Luis    | ✓ |
| Ana      | X        | María   | ✗ ESPURIA |
| Ana      | Y        | Luis    | ✗ ESPURIA |
| Ana      | Y        | María   | ✓ |
| Carlos   | X        | Luis    | ✓ |

**Condición para una descomposición sin pérdida** (*lossless join*): el **atributo común** entre las tablas resultantes debe ser **clave (o superclave) en al menos una de ellas**. En el ejemplo, `Empleado` no es clave ni en T1 ni en T2 → descomposición incorrecta.

---

## 3. Dependencias funcionales (DF)

Las dependencias funcionales son **el concepto central del tema**: las cuatro directrices anteriores se formalizan y se verifican con ellas.

### 3.1. Definición

$X \to Y$ (se lee "*X determina Y*") significa: **conocido el valor de X, queda determinado un único valor de Y**.

Formalmente: $X \to Y$ si y solo si $\forall t_1, t_2 \in r(R):\ t_1[X] = t_2[X] \Rightarrow t_1[Y] = t_2[Y]$

A $X$ se le llama **determinante**.

**Ejemplo.** En $EMP\_PROY$(<u>Dni, NumProyecto</u>, NombreE, NombreP, UbicacionP, Horas):
- DF1: $Dni \to NombreE$
- DF2: $NumProyecto \to \{NombreP, UbicacionP\}$
- DF3: $\{Dni, NumProyecto\} \to Horas$

### 3.2. Tres ideas importantes

1. **Las DF son semánticas, no estadísticas**: las define el significado de los datos y las reglas de negocio, no lo que casualmente contenga la tabla en un momento dado.
2. Si $K$ es **clave candidata** de $R$, entonces $K$ determina funcionalmente **todos** los atributos de $R$.
3. $X \to Y$ **no implica** $Y \to X$.

### 3.3. DF completa vs. DF parcial

- $X \to Y$ es **completa (o total)** si al quitar cualquier atributo de $X$ la dependencia deja de cumplirse. *Ejemplo*: $\{Dni, NumProyecto\} \to Horas$ (ni el Dni solo ni el proyecto solo determinan las horas).
- $X \to Y$ es **parcial** si se puede quitar algún atributo de $X$ y la dependencia se mantiene. *Ejemplo*: $\{Dni, NumProyecto\} \to NombreE$ es parcial, porque ya $Dni \to NombreE$.

### 3.4. ¿Qué DF se pueden deducir mirando los datos?

De una instancia concreta de la tabla **nunca se puede afirmar** que una DF existe (una tupla futura podría violarla). Lo que **sí** se puede afirmar con certeza es que **ciertas DF NO existen**, si hay tuplas que las violan.

**Ejemplo.** Relación $IMPARTIR$:

| Profesor | Curso                   | Texto    |
|----------|-------------------------|----------|
| Smith    | Estructuras de datos    | Bartram  |
| Smith    | Administración de datos | Martin   |
| Hall     | Compiladores            | Hoffman  |
| Brown    | Estructuras de datos    | Horowitz |

- $Profesor \to Curso$: **descartada** (Smith imparte dos cursos).
- $Curso \to Texto$: **descartada** (Estructuras de datos aparece con dos textos).
- $Texto \to Curso$: **puede existir** (ninguna tupla la viola... por ahora).

> 💡 **Truco para el examen**: para descartar $X \to Y$ busca dos filas con el mismo valor de $X$ y distinto valor de $Y$. Si no las hay, la DF *puede* existir (pero no está garantizada).

---

## 4. Repaso previo: claves y atributos primos

Estas definiciones son imprescindibles para entender las formas normales:

- **Superclave**: conjunto de atributos que identifica unívocamente cada tupla (no hay dos tuplas con los mismos valores en ella).
- **Clave (candidata)**: superclave **mínima** (si le quitas un atributo, deja de ser superclave). Puede haber varias.
- **Clave primaria**: la clave candidata elegida; las demás son **claves alternativas**.
- **Atributo primo**: pertenece a **alguna** clave candidata.
- **Atributo no primo**: no pertenece a ninguna clave candidata.

---

## 5. Formas normales

La **normalización** (Codd, 1972) es una técnica de diseño *de abajo a arriba*: se comprueba que cada tabla cumple una serie de reglas basadas en las claves y las DF. Si una tabla **no cumple** una regla, se **descompone** en tablas que sí la cumplan. Cada forma normal es más estricta que la anterior y hace la tabla menos vulnerable a anomalías.

**Resumen mnemotécnico** — todos los atributos deben depender de:

| Forma normal | *"la clave..."* |
|:---:|---|
| **1FN** | ...la clave (valores atómicos, dependen de la clave) |
| **2FN** | ...la clave **completa** (sin dependencias parciales) |
| **3FN** | ...**nada más que** la clave (sin dependencias transitivas) |
| **FNBC** | ...todo determinante **es** superclave |

> En inglés se resume como: *"the key, the whole key, and nothing but the key"*.

### 5.1. Primera forma normal (1FN)

**Regla**: cada atributo contiene un **valor atómico** (ni listas, ni conjuntos, ni atributos compuestos o multivaluados) y con un único significado.

La 1FN forma parte de la **propia definición de relación** en el modelo relacional: los SGBDR solo permiten tablas en 1FN.

**Ejemplo.** $DEPARTAMENTO$(<u>NumeroDpto</u>, NombreDpto, UbicacionesDpto), donde un departamento puede tener **varias ubicaciones** → viola 1FN.

**Solución recomendada**: extraer el atributo multivaluado a una **relación independiente** junto con la clave:

$LOCALIZACIONES\_DPTO$(<u>NumeroDpto, Ubicacion</u>)

Otras alternativas (expandir la clave duplicando filas, o crear atributos `Ubicacion1`, `Ubicacion2`, `Ubicacion3`...) introducen redundancia o valores NULL, y **deben evitarse**.

### 5.2. Segunda forma normal (2FN)

**Regla**: está en 1FN y **ningún atributo no primo depende parcialmente de la clave primaria** (todos dependen de la clave *completa*).

> ⚡ **Atajo**: la 2FN solo puede violarse con **claves primarias compuestas**. Si la clave primaria tiene un solo atributo, la tabla en 1FN ya está en 2FN.

**Ejemplo.** $EMP\_PROY$(<u>Dni, NumProyecto</u>, NombreE, NombreP, Horas):
- $\{Dni, NumProyecto\} \to Horas$ → completa ✓
- $\{Dni, NumProyecto\} \to NombreE$ → **parcial** ✗ (basta con $Dni$)
- $\{Dni, NumProyecto\} \to NombreP$ → **parcial** ✗ (basta con $NumProyecto$)

**Receta de descomposición**: sacar los atributos con dependencia parcial a una nueva tabla **junto con la parte de la clave de la que dependen**:

- $EP1$(<u>Dni, NumProyecto</u>, Horas)
- $EP2$(<u>Dni</u>, NombreE)
- $EP3$(<u>NumProyecto</u>, NombreP)

### 5.3. Tercera forma normal (3FN)

**Regla**: está en 2FN y **ningún atributo no primo depende transitivamente de la clave primaria**.

$X \to Z$ es **transitiva** si existen $X \to Y$ e $Y \to Z$, siendo $Y$ un conjunto de atributos que **no es clave ni parte de una clave candidata**.

**Ejemplo.** $EMP\_DEPT$(<u>Dni</u>, NombreE, NumeroDpto, NombreDpto, DniDirector):
- $Dni \to NumeroDpto$ y $NumeroDpto \to DniDirector$ → $Dni \to DniDirector$ es **transitiva** ✗ (y lo mismo para NombreDpto).
- $Dni \to NombreE$ **no** es transitiva ✓ (no hay atributo intermedio).

**Receta de descomposición**: sacar los atributos transitivamente dependientes a una nueva tabla **junto con su determinante**:

- $ED1$(<u>Dni</u>, NombreE, NumeroDpto)
- $ED2$(<u>NumeroDpto</u>, NombreDpto, DniDirector)

Intuición: ED1 habla de *empleados* y ED2 de *departamentos* — cada tabla vuelve a representar una sola entidad (¡directriz 1!). El `NATURAL JOIN` de ambas recupera la original **sin tuplas espurias**.

> ⚠️ **Matiz habitual en exámenes**: una tabla en 3FN **sí puede tener dependencias transitivas** si el atributo intermedio es clave candidata. En $EMP$(<u>Dni</u>, Emp\#, Sueldo) con Emp\# clave alternativa, la cadena $Dni \to Emp\# \to Sueldo$ **no** viola 3FN porque Emp\# es clave candidata.

### 5.4. Definiciones generales (todas las claves candidatas)

Las definiciones anteriores solo miran la **clave primaria**. Las definiciones generales consideran **todas las claves candidatas**:

- **2FN (general)**: todo atributo no primo depende **funcionalmente por completo de cada clave** de $R$.
- **3FN (general)**: para toda DF $X \to Y$ de $R$, se cumple que **$X$ es superclave** o **$Y$ es atributo primo**.

**Ejemplo.** $PARCELAS$(<u>IdPropiedad</u>, NombreMunicipio, NumParcela, Area, Precio, Impuesto), con dos claves candidatas: {IdPropiedad} y {NombreMunicipio, NumParcela}.

1. $NombreMunicipio \to Impuesto$ **viola 2FN** (dependencia parcial de la clave candidata {NombreMunicipio, NumParcela}) → se descompone:
   - $PARCELAS1$(<u>IdPropiedad</u>, NombreMunicipio, NumParcela, Area, Precio)
   - $PARCELAS2$(<u>NombreMunicipio</u>, Impuesto)
2. En PARCELAS1, $Area \to Precio$ **viola 3FN** (Area no es superclave y Precio no es primo) → se descompone:
   - $PARCELAS1A$(<u>IdPropiedad</u>, NombreMunicipio, NumParcela, Area)
   - $PARCELAS1B$(<u>Area</u>, Precio)

### 5.5. Forma normal de Boyce-Codd (FNBC)

**Regla**: para **toda** DF $X \to Y$ de $R$, **$X$ es superclave**.

Es la versión "estricta" de la 3FN: desaparece la excepción de "$Y$ es atributo primo". Toda relación en FNBC está en 3FN, pero no al revés.

> 🧭 **Cómo comprobarlo rápido**: haz la lista de todas las DF y pregunta por cada una: *¿el lado izquierdo es superclave?* Si todas responden sí → FNBC.

### 5.6. Propiedades que debe cumplir la descomposición

Comprobar que cada tabla está en 3FN/FNBC **no basta**: el conjunto de tablas resultante debe cumplir además:

1. **Descomposición sin pérdida** (*lossless join*): el JOIN de las tablas recupera exactamente la relación original, sin tuplas espurias. **Es crítica y debe garantizarse siempre.**
2. **Preservación de dependencias**: cada DF queda representada en alguna de las tablas resultantes. **Es deseable, pero a veces se sacrifica** (típicamente al forzar FNBC).

---

## 6. Desnormalización

Proceso **intencional e inverso**: introducir redundancia en un esquema ya normalizado (p. ej., pre-calculando el JOIN de varias tablas en una sola) para **mejorar el rendimiento de las consultas**, a costa de reabrir la puerta a las anomalías.

Criterio práctico actual:
- El hardware y los SGBD modernos (vistas materializadas, índices) han reducido mucho el coste de los JOIN.
- **Solo tiene sentido en almacenes de datos** (data warehouses, consultas analíticas). **Nunca en un sistema OLTP de producción**: el código extra necesario para mantener la integridad de los datos duplicados no compensa.

---

## 7. Chuleta final

**Procedimiento de normalización paso a paso:**

1. Identifica la **clave primaria** y todas las **claves candidatas**.
2. Lista las **dependencias funcionales** (a partir de la semántica, no solo de los datos).
3. **1FN**: ¿algún atributo no atómico o multivaluado? → sácalo a otra tabla con la clave.
4. **2FN**: ¿algún atributo no primo depende de *parte* de una clave? → sácalo con esa parte de la clave.
5. **3FN**: ¿algún atributo no primo depende de otro atributo no clave? → sácalo con su determinante.
6. **FNBC**: ¿algún determinante que no sea superclave? → descompón.
7. Verifica que la descomposición es **sin pérdida** (el atributo común es clave en alguna de las tablas) y, si es posible, que **preserva las dependencias**.

**Errores típicos a evitar:**
- Deducir DF solo de los datos de ejemplo (los datos solo sirven para **descartar** DF).
- Olvidar que la 2FN solo se viola con claves **compuestas**.
- Creer que 3FN prohíbe *toda* dependencia transitiva (no, si el intermedio es clave candidata).
- Descomponer por un atributo común que no es clave en ninguna tabla → tuplas espurias.