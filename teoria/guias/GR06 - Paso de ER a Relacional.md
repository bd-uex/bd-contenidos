<<<<<<< HEAD
# T06 (Resumen) — Mapeo E/R a Relacional: Guía de Repaso

---
tags: #database, #summary, #entity-relationship #relational
origin: Versión condensada de los apuntes del T06 (basados en Elmasri & Navathe, 5ª ed.)
---

## 0. La idea en una frase

Transformar el diagrama E/R (plano conceptual) en un **esquema relacional** (relaciones, atributos, PKs y FKs) aplicando un **algoritmo sistemático de 8 pasos**. No es SQL todavía: es el diseño lógico.

**Objetivos del mapeo:**
1. **Conservar toda la información** (ningún atributo ni relación se pierde).
2. **Mantener las restricciones** (claves, cardinalidades) en la medida de lo posible.
3. **Minimizar valores `NULL`**.

---

## 1. Notación que usaremos (¡léela antes de seguir!)

| Notación | Significado |
|---|---|
| <u>atributo</u> | Clave Primaria (PK) |
| ==atributo== | Clave Alternativa (`UNIQUE`) |
| `atributo -> RELACION(pk)` | Clave Externa (FK) que referencia a esa relación |
| `NOT NULL` | La FK no admite nulos (participación total) |
| `[B:x, M:x]` | Opción de **B**orrado y **M**odificación de la FK: **R**estringido, **C**ascada, **N**ulos, **D**efecto |

> 💡 En papel: PK subrayada continua, alternativa subrayada discontinua, FKs con flechas (grafo relacional).

---

## 2. Chuleta: ¿qué regla aplico?

| Veo en el E/R... | Paso | Estrategia |
|---|---|---|
| Entidad fuerte | 1 | Nueva relación con sus atributos |
| Entidad débil | 2 | Nueva relación + PK compuesta (PK propietaria + clave parcial) |
| Binaria **1:1** | 3 | Depende de la **participación** (ver tabla del Paso 3) |
| Binaria **1:N** | 4 | FK en el lado N (caso general) |
| Binaria **M:N** | 5 | Siempre relación adicional (referencia cruzada) |
| Unaria (recursiva) | 6 | Igual que 3/4/5, renombrando FKs con los roles |
| Ternaria (n>2) | 7 | Relación adicional; PK según cardinalidades máximas |
| Jerarquía | 8 | 4 opciones según total/parcial y disjunta/solapada |

Los dos enfoques que se repiten una y otra vez:
- **Enfoque de Clave Externa**: la PK de una entidad "viaja" como FK a la relación de la otra.
- **Enfoque de Relación Adicional**: se crea una relación nueva que contiene las PKs de ambas entidades como FKs.

---

## 3. El algoritmo paso a paso

### Paso 1 — Entidades fuertes

Por cada entidad fuerte → una **relación** con:
- Todos sus atributos (los **compuestos se descomponen**: `NombreCompleto` → `nombre`, `apellido1`, `apellido2`).
- Su clave como <u>PK</u>; las claves alternativas ==resaltadas==.

**EMPRESA:**
- EMPLEADO(<u>dni</u>, nombre, apellido1, apellido2, fechaNac, direccion, sexo, sueldo)
- DEPARTAMENTO(<u>numero_departamento</u>, ==nombre==)
- PROYECTO(<u>numero_proyecto</u>, ==nombre==, ubicacion)

---

### Paso 2 — Entidades débiles

Por cada entidad débil `D` con propietaria `P` → una **relación** con:
- Los atributos de `D`.
- La PK de `P` como **FK** (si hay varias propietarias, todas).
- **PK compuesta** = PK de la(s) propietaria(s) + clave parcial de `D`.

**EMPRESA:**
- FAMILIAR(<u>empleado</u>, <u>nombre</u>, sexo, fechaNac, relacion)
	- empleado -> EMPLEADO(dni) [B:C,M:C]
- UBICACION_DPTO(<u>dpto</u>, <u>nombre</u>)
	- dpto -> DEPARTAMENTO(numero_departamento) [B:C,M:C]

> 💡 El borrado suele ser **en cascada (C)**: si desaparece el propietario, desaparecen sus débiles.

---

### Paso 3 — Binarias 1:1 → decide la PARTICIPACIÓN

| Cardinalidades | Caso | Estrategia |
|---|---|---|
| `E1[1..1]` ↔ `E2[0..1]` | **A** (el más común) | **Clave Externa**: la PK del lado opcional (`E1`) migra como FK al lado obligado (`E2`). Así no hay `NULL`s. FK con `NOT NULL` + `UNIQUE` |
| `E1[0..1]` ↔ `E2[0..1]` | **B** | **Relación Adicional**: nueva relación con ambas PKs como FKs; una es <u>PK</u> y la otra ==UNIQUE== |
| `E1[1..1]` ↔ `E2[1..1]` | **C** (raro) | **Clave Externa** (en cualquiera de los dos sentidos, con `NOT NULL`+`UNIQUE`) o, si son conceptualmente muy cercanas, **fusionar** ambas en una sola relación |

> 🔑 El `UNIQUE` en la FK es lo que garantiza que la cardinalidad máxima sea 1 (y no N). Los atributos de la relación E/R migran siempre con la FK (o a la relación adicional).

**Ejemplo Caso A:** una `PROVINCIA` debe tener un municipio capital `[1..1]`; un `MUNICIPIO` puede ser capital o no `[0..1]`:
- MUNICIPIO(<u>id_municipio</u>, ...)
- PROVINCIA(<u>id_provincia</u>, ..., id_municipio)
	- id_municipio `NOT NULL`,`UNIQUE` -> MUNICIPIO(id_municipio) [B:R,M:C]

**Ejemplo Caso B:** `TRABAJADOR[0..1]` ↔ `PLAZA_APARCAMIENTO[0..1]`:
- TIENE_ASIGNADA(<u>id_trabajador</u>, ==id_plaza==)
	- id_trabajador -> TRABAJADOR(id_trabajador) [B:C,M:C]
	- id_plaza `NOT NULL`,`UNIQUE` -> PLAZA_APARCAMIENTO(id_plaza) [B:C,M:C]

**EMPRESA:** `EMPLEADO[1..1]` DIRIGE `DEPARTAMENTO[0..1]` → Caso A:
- DEPARTAMENTO(<u>numero_departamento</u>, ==nombre==, **director**, fechaIngresoDirector)
	- director `NOT NULL`,`UNIQUE` -> EMPLEADO(dni) [B:R,M:C]

---

### Paso 4 — Binarias 1:N → FK en el lado N

**Caso general (Enfoque de Clave Externa):** la PK del lado 1 migra como FK al lado N, junto con los atributos de la relación.

La **cardinalidad mínima del lado N** decide la nulidad de la FK:

| Participación del lado N | FK |
|---|---|
| Total `[1..N] ↔ [1..1]` en el lado N | `NOT NULL` |
| Opcional `[0..1]` en su lado | admite `NULL` |

**Ejemplo:** `PROVINCIA[1..N]` ↔ `MUNICIPIO[1..1]`:
- MUNICIPIO(<u>id_municipio</u>, ..., id_provincia)
	- id_provincia `NOT NULL` -> PROVINCIA(id_provincia) [B:R,M:C]

**Caso excepcional (Enfoque de Relación Adicional):** crear una relación aparte con ambas PKs, siendo <u>PK</u> la FK del lado N. Úsalo solo si:
1. La opcionalidad es muy alta (la FK tendría **muchos `NULL`s**), o
2. Se prevé que la relación **evolucione a M:N**, o
3. La relación tiene **muchos atributos propios**.

**Ejemplo (biblioteca con histórico previsto):**
- PRESTAMOS(id_socio, <u>id_ejemplar</u>, fecha, ...)
	- id_socio `NOT NULL` -> SOCIO_BIBLIOTECA(id_socio) [B:C,M:C]
	- id_ejemplar -> EJEMPLAR_LIBRO(id_ejemplar) [B:C,M:C]

**EMPRESA** (caso general en ambas):
- EMPLEADO(<u>dni</u>, ..., **dpto**)
	- dpto `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
- PROYECTO(<u>numero_proyecto</u>, ==nombre==, ubicacion, **dpto**)
	- dpto `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]

---

### Paso 5 — Binarias M:N → SIEMPRE relación adicional

No hay alternativa: se crea una **relación de referencia cruzada** con:
- Las PKs de ambas entidades como FKs.
- Los atributos propios de la relación.
- **PK compuesta** = (id_E1, id_E2).

**EMPRESA:** `EMPLEADO[1..N]` TRABAJA_EN `PROYECTO[0..N]`:
- TRABAJA_EN(<u>empleado</u>, <u>proyecto</u>, horas)
	- empleado -> EMPLEADO(dni) [B:C,M:C]
	- proyecto -> PROYECTO(numero_proyecto) [B:C,M:C]

**Paso 5b — Entidades asociativas:** si la M:N se modeló como entidad asociativa (para poder relacionarse con otras entidades), se mapea en dos etapas:
1. La entidad asociativa se convierte en relación **exactamente igual que en el Paso 5**.
2. Sus relaciones "puente" con otras entidades se mapean con las reglas normales de los **Pasos 3 o 4**, tratándola como una relación más.

---

### Paso 6 — Unarias (recursivas)

**No hay reglas nuevas**: se aplica el Paso 3, 4 o 5 según la cardinalidad. La única particularidad es **renombrar las FKs usando los roles** del E/R para que se entiendan (una FK llamada `dni` apuntando a `dni` no dice nada; `supervisor` sí).

**EMPRESA:** `EMPLEADO[0..1] (Supervisor)` SUPERVISA `EMPLEADO[0..N]` → es una 1:N (Paso 4, caso general):
- EMPLEADO(<u>dni</u>, ..., dpto, **supervisor**)
	- supervisor -> EMPLEADO(dni) [B:N,M:C]

> 💡 Fíjate en la opción de borrado **N** (puesta a nulos): si se borra el supervisor, sus supervisados se quedan sin supervisor, no se borran en cascada.

---

### Paso 7 — Relaciones de grado n>2 (ternarias)

Como una M:N: **Enfoque de Relación Adicional** con las PKs de las tres (o más) entidades como FKs + atributos propios.

Lo único que cambia es cómo elegir la **PK compuesta**, según las **cardinalidades máximas**:

| Cardinalidades máximas | PK de la relación adicional |
|---|---|
| N, N, N | Las tres claves: (id_E1, id_E2, id_E3) |
| N, N, 1 | Las dos claves de los lados N: (id_E1, id_E2) |
| N, 1, 1 | La clave del lado N + una cualquiera de las otras (hay varias PKs válidas) |
| 1, 1, 1 | Cualquier par de claves (hay varias PKs válidas) |

> 🔑 Regla mnemotécnica: la clave de un lado con cardinalidad máxima 1 "sobra" en la PK, porque ese lado ya queda determinado por el resto.

**Ejemplo:** `PROVEEDOR[1..N]` — SUMINISTRA — `REPUESTO[1..N]` — `PROYECTO[1..N]` (N en todos los lados):
- SUMINISTRA(<u>id_proveedor</u>, <u>id_proyecto</u>, <u>id_repuesto</u>, cantidad)
	- id_proveedor -> PROVEEDOR(id_proveedor) [B:C,M:C]
	- id_proyecto -> PROYECTO(id_proyecto) [B:C,M:C]
	- id_repuesto -> REPUESTO(id_repuesto) [B:C,M:C]

---

### Paso 8 — Jerarquías (Especialización/Generalización)

Antes de elegir, responde dos preguntas: **¿total o parcial? ¿disjunta o solapada?** Y decide qué prefieres penalizar: ¿`JOIN`s o `NULL`s?

| Opción | Relaciones creadas | ¿Cuándo es válida? | Ventaja | Coste |
|---|---|---|---|---|
| **8A** Superclase + subclases | 1 por superclase + 1 por subclase (misma PK, que en las subclases es también FK) | **SIEMPRE** ✅ | Sin redundancia, sin `NULL`s | Requiere `JOIN`s |
| **8B** Solo subclases | 1 por subclase (con los atributos comunes repetidos) | Solo **TOTAL y DISJUNTA** | Consultas rápidas por subclase | Redundancia; consultar "toda la superclase" exige `UNION` |
| **8C** Relación única + discriminante | 1 sola, con atributo de tipo (`tipo_trabajo`) | Solo **DISJUNTA** | Sin `JOIN`s | Muchos `NULL`s |
| **8D** Relación única + booleanos | 1 sola, con un flag por subclase | Cualquiera; la única de relación única válida para **SOLAPADAS** | Sin `JOIN`s | Muchos `NULL`s |

**Ejemplo 8A** (la recomendada por defecto), jerarquía EMPLEADO → {ADMINISTRATIVO, TECNICO, INGENIERO}:
- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono)
- ADMINISTRATIVO(<u>dni</u>, categoria)
	- dni -> EMPLEADO(dni) [B:C,M:C]
- TECNICO(<u>dni</u>, nivel)
	- dni -> EMPLEADO(dni) [B:C,M:C]
- INGENIERO(<u>dni</u>, tipoIng)
	- dni -> EMPLEADO(dni) [B:C,M:C]

**Ejemplo 8C** (mismo caso, en una sola relación):
- EMPLEADO(<u>dni</u>, nombre, ..., **tipo_trabajo**, categoria, nivel, tipoIng)

**Ejemplo 8D** (jerarquía solapada PERSONA → {ESTUDIANTE, EMPLEADO}):
- PERSONA(<u>dni</u>, nombre, apellido1, apellido2, **estudiante**, **empleado**, notaSelectividad, puesto, salario)

---

## 4. Resultado final: esquema relacional de EMPRESA

- EMPLEADO(<u>dni</u>, nombre, apellido1, apellido2, fechaNac, direccion, sexo, sueldo, dpto, supervisor)
	- dpto `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
	- supervisor -> EMPLEADO(dni) [B:N,M:C]
- DEPARTAMENTO(<u>numero_departamento</u>, ==nombre==, director, fechaIngresoDirector)
	- director `NOT NULL`,`UNIQUE` -> EMPLEADO(dni) [B:R,M:C]
- PROYECTO(<u>numero_proyecto</u>, ==nombre==, ubicacion, dpto)
	- dpto `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
- FAMILIAR(<u>empleado</u>, <u>nombre</u>, sexo, fechaNac, relacion)
	- empleado -> EMPLEADO(dni) [B:C,M:C]
- UBICACION_DPTO(<u>dpto</u>, <u>nombre</u>)
	- dpto -> DEPARTAMENTO(numero_departamento) [B:C,M:C]
- TRABAJA_EN(<u>empleado</u>, <u>proyecto</u>, horas)
	- empleado -> EMPLEADO(dni) [B:C,M:C]
	- proyecto -> PROYECTO(numero_proyecto) [B:C,M:C]

---

## 5. Autoevaluación rápida

1. ¿Por qué en una 1:1 con participación `[1..1]`↔`[0..1]` la FK va al lado obligado y no al opcional?
2. ¿Qué restricción convierte una FK "normal" en una FK que respeta cardinalidad máxima 1?
3. ¿Cuándo compensa usar relación adicional en una 1:N en vez de la FK directa? (3 motivos)
4. En una ternaria con cardinalidades máximas N, N, 1, ¿qué claves forman la PK?
5. ¿Qué opción de mapeo de jerarquías funciona siempre? ¿Cuál es la única de relación única válida para jerarquías solapadas?
6. ¿Por qué la FK `supervisor` de EMPLEADO tiene opción de borrado N (nulos) y no C (cascada)?

<details><summary>Respuestas</summary>

1. Para evitar `NULL`s: el lado obligado siempre tendrá valor en la FK.
2. `UNIQUE` (y `NOT NULL` si además la participación es total).
3. Muchos `NULL`s esperados, evolución prevista a M:N, o muchos atributos propios de la relación.
4. Las claves de los dos lados con cardinalidad máxima N.
5. La 8A (superclase + subclases). Para solapadas con relación única, solo la 8D (booleanos).
6. Porque al borrar un supervisor no queremos borrar a sus supervisados, solo dejarlos temporalmente sin supervisor.

</details>
=======
# T06 (Resumen) — Mapeo E/R a Relacional: Guía de Repaso

---
tags: #database, #summary, #entity-relationship #relational
origin: Versión condensada de los apuntes del T06 realizada por Claude Opus 5.5. Refinado y editado por aeprieto
last-update: 2026-11-10
 
---

## 0. La idea en una frase

Transformar el diagrama E/R (plano conceptual) en un **esquema relacional** (relaciones, atributos, PKs y FKs) aplicando un **algoritmo sistemático de 8 pasos**. No es SQL todavía: es el diseño lógico.

**Objetivos del mapeo:**
1. **Conservar toda la información** (ningún atributo ni relación se pierde).
2. **Mantener las restricciones** (claves, cardinalidades) en la medida de lo posible.
3. **Minimizar valores `NULL`**.

---

## 1. Notación que usaremos (¡léela antes de seguir!)

| Notación | Significado |
|---|---|
| <u>atributo</u> | Clave Primaria (PK) |
| <span style="text-decoration: underline dashed; text-underline-offset: 3px;">atributo</span> | Clave Alternativa |
| ***atributo*** | Clave Externa (FK) |
| ***atributo*** -> RELACION(clave) [B:x,M:y] | Debajo de cada relación: a qué relación hace referencia cada FK |
| `NOT NULL` | La FK no admite nulos |
| `UNIQUE` | La FK no admite repetidos |
| [B:x,M:y] | Opción de **B**orrado y **M**odificación de la clave referenciada: **R**estringido, **C**ascada, **N**ulos, **D**efecto |

> 💡 En el **grafo relacional**, además, las FKs se dibujan con flechas hacia la clave a la que hacen referencia, y los atributos que admiten nulos llevan un asterisco (`supervisor*`).

> 💡 **Nombra las FKs por su rol**: no hace falta que una FK se llame igual que la clave a la que apunta. `capital` dice mucho más que `id_municipio`, y si una relación tiene dos FKs hacia la misma tabla (`capital` y `municipio_mas_poblado`), es la única forma de distinguirlas.

---

## 2. Chuleta: ¿qué regla aplico?

| Veo en el E/R... | Paso | Estrategia |
|---|---|---|
| Entidad fuerte | 1 | Nueva relación con sus atributos |
| Entidad débil | 2 | Nueva relación + PK compuesta (PK propietaria + clave parcial) |
| Binaria **1:1** | 3 | Depende de la **participación** (ver tabla del Paso 3) |
| Binaria **1:N** | 4 | FK en el lado N (caso general) |
| Binaria **M:N** | 5 | Siempre relación adicional (referencia cruzada) |
| Entidad asociativa | 5b | Como una M:N, y después sus relaciones con otras entidades |
| Unaria (recursiva) | 6 | Igual que 3/4/5, renombrando FKs con los roles |
| Ternaria (n>2) | 7 | Relación adicional; PK según cardinalidades máximas |
| Jerarquía | 8 | 4 opciones según total/parcial y disjunta/solapada |

Los dos enfoques que se repiten una y otra vez:
- **Enfoque de Clave Externa**: la PK de una entidad "viaja" como FK a la relación de la otra.
- **Enfoque de Relación Adicional**: se crea una relación nueva que contiene las PKs de ambas entidades como FKs.

---

## 3. El algoritmo paso a paso

### Paso 1 — Entidades fuertes

Por cada entidad fuerte → una **relación** con:
- Todos sus atributos (los **compuestos se descomponen**: `nombre_completo` → `nombre`, `apellido_1`, `apellido_2`).
- Su clave como <u>PK</u> y las claves alternativas con <span style="text-decoration: underline dashed; text-underline-offset: 3px;">subrayado discontinuo</span>.

**EMPRESA:**
- EMPLEADO(<u>dni</u>, nombre, apellido_1, apellido_2, fecha_nacimiento, direccion, sexo, sueldo)
- DEPARTAMENTO(<u>numero_departamento</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>)
- PROYECTO(<u>numero_proyecto</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ubicacion)

---

### Paso 2 — Entidades débiles

Por cada entidad débil `D` con propietaria `P` → una **relación** con:
- Los atributos de `D`.
- La PK de `P` como **FK** (si hay varias propietarias, todas).
- **PK compuesta** = PK de la(s) propietaria(s) + clave parcial de `D`.

**EMPRESA:**
- FAMILIAR(<u>***empleado***, nombre</u>, sexo, fecha_nacimiento, relacion)
	- ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
- UBICACION_DEPARTAMENTO(<u>***dpto***, nombre</u>)
	- ***dpto*** -> DEPARTAMENTO(numero_departamento) [B:C,M:C]

> 💡 El borrado suele ser **en cascada (C)**: si desaparece el propietario, desaparecen sus débiles.

---

### Paso 3 — Binarias 1:1 → decide la PARTICIPACIÓN

| Cardinalidades | Caso | Estrategia |
|---|---|---|
| `E_1[1..1]` ↔ `E_2[0..1]` | **A** (el más común) | **Clave Externa**: la PK de `E_1` (que no está obligada a relacionarse) migra como FK a `E_2` (que sí lo está). Así no hay `NULL`s. FK con `NOT NULL` y `UNIQUE` |
| `E_1[0..1]` ↔ `E_2[0..1]` | **B** | **Relación Adicional**: nueva relación con ambas PKs como FKs; una es la <u>PK</u> y la otra, clave <span style="text-decoration: underline dashed; text-underline-offset: 3px;">alternativa</span> (`NOT NULL` y `UNIQUE`) |
| `E_1[1..1]` ↔ `E_2[1..1]` | **C** (raro) | **Clave Externa** en cualquiera de los dos sentidos, con `NOT NULL` y `UNIQUE`, o, si son conceptualmente muy cercanas, **fusionar** ambas en una sola relación |

> 🔑 El `UNIQUE` en la FK es lo que garantiza que la cardinalidad máxima sea 1 (y no N). Los atributos de la relación E/R migran siempre con la FK (o a la relación adicional).

**Ejemplo Caso A:** una `PROVINCIA` debe tener de capital **un** `MUNICIPIO` (`[1..1]`); un `MUNICIPIO` puede ser capital o no de **una** `PROVINCIA` (`[0..1]`):
- MUNICIPIO(<u>id_municipio</u>, resto_atributos_municipio)
- PROVINCIA(<u>id_provincia</u>, resto_atributos_provincia, ***capital***)
	- ***capital*** `NOT NULL`, `UNIQUE` -> MUNICIPIO(id_municipio) [B:R,M:C]

**Ejemplo Caso B:** un `TRABAJADOR` puede tener asignada (o no) **una** `PLAZA_APARCAMIENTO` (`[0..1]`), y una plaza puede estar asignada (o no) a **un** trabajador (`[0..1]`):
- TIENE_ASIGNADA(<u>***trabajador***</u>, <span style="font-weight: bold; font-style: italic; text-decoration: underline dashed; text-underline-offset: 3px;">plaza</span>, atributos_relacion)
	- ***trabajador*** -> TRABAJADOR(id_trabajador) [B:C,M:C]
	- ***plaza*** `NOT NULL`, `UNIQUE` -> PLAZA_APARCAMIENTO(id_plaza) [B:C,M:C]

**EMPRESA:** `Empleado[1..1]` `dirige` `Departamento[0..1]` → Caso A:
- DEPARTAMENTO(<u>numero_departamento</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ***director***, fecha_ingreso_director)
	- ***director*** `NOT NULL`, `UNIQUE` -> EMPLEADO(dni) [B:R,M:C]

---

### Paso 4 — Binarias 1:N → FK en el lado N

Partimos de `E_1[X..1]` ↔ `E_2[X..N]`. El `1` máximo escrito junto a `E_1` indica que cada instancia de `E_2` se relaciona como mucho con **una** de `E_1`: por eso la relación cabe en la propia `E_2`.

**Caso general (Enfoque de Clave Externa):** la PK de `E_1` migra como FK a `E_2`, junto con los atributos de la relación.

Si la FK admite `NULL`s lo decide la **cardinalidad mínima escrita junto a `E_1`**, que indica si cada instancia de `E_2` está obligada a relacionarse:

| Cardinalidades | FK en `E_2` | Borrado |
|---|---|---|
| `E_1[1..1]` ↔ `E_2[X..N]` | `NOT NULL` | [B:R,M:C] |
| `E_1[0..1]` ↔ `E_2[X..N]` | admite `NULL` | [B:N,M:C] |

**Ejemplo obligatorio:** una `PROVINCIA` **debe** tener al menos **un** `MUNICIPIO` (`[1..N]`), y un `MUNICIPIO` **debe** pertenecer a **una** `PROVINCIA` (`[1..1]`):
- MUNICIPIO(<u>id_municipio</u>, resto_atributos_municipio, ***provincia***)
	- ***provincia*** `NOT NULL` -> PROVINCIA(id_provincia) [B:R,M:C]

**Ejemplo opcional:** un `SOCIO` puede tener (o no) **muchos** ejemplares en préstamo (`[0..N]`), y un `EJEMPLAR` puede estar (o no) prestado a **un** `SOCIO` (`[0..1]`). Se espera que la mayoría de los ejemplares estén prestados:
- EJEMPLAR_DE_LIBRO(<u>id_ejemplar</u>, resto_atributos_ejemplar, ***socio***, fecha_prestamo)
	- ***socio*** -> SOCIO_BIBLIOTECA(id_socio) [B:N,M:C]

**Caso excepcional (Enfoque de Relación Adicional):** se crea una relación aparte con ambas PKs como FKs, siendo la <u>PK</u> la FK del lado N, y la otra FK `NOT NULL`. Úsalo solo si:
1. La opcionalidad es muy alta (la FK tendría **muchos `NULL`s**), o
2. Se prevé que la relación **evolucione a M:N**, o
3. La relación tiene **muchos atributos propios**.

**Ejemplo:** el mismo préstamo, pero ahora se espera que la mayoría de los ejemplares **no** estén prestados, la relación tiene varios atributos y se prevé que **en el futuro** se quiera guardar el histórico:
- PRESTAMO(***socio***, <u>***ejemplar***</u>, fecha_prestamo, fecha_devolucion_prevista, observaciones)
	- ***socio*** `NOT NULL` -> SOCIO_BIBLIOTECA(id_socio) [B:C,M:C]
	- ***ejemplar*** -> EJEMPLAR_DE_LIBRO(id_ejemplar) [B:C,M:C]

**EMPRESA** (caso general en ambas): `trabaja_para` (`Empleado[1..N]` ↔ `Departamento[1..1]`) y `controla` (`Departamento[1..1]` ↔ `Proyecto[0..N]`):
- EMPLEADO(<u>dni</u>, ..., ***dpto***)
	- ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
- PROYECTO(<u>numero_proyecto</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ubicacion, ***dpto***)
	- ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]

---

### Paso 5 — Binarias M:N → SIEMPRE relación adicional

No hay alternativa: como cada instancia de `E_1` puede relacionarse con muchas de `E_2` y al revés, la relación no cabe en ninguna de las dos. Se crea una **relación de referencia cruzada** con:
- Las PKs de ambas entidades como FKs.
- Los atributos propios de la relación.
- **PK compuesta** = (`id_e_1`, `id_e_2`).

**EMPRESA:** `Empleado[1..N]` `trabaja_en` `Proyecto[0..N]`:
- TRABAJA_EN(<u>***empleado***, ***proyecto***</u>, horas)
	- ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
	- ***proyecto*** -> PROYECTO(numero_proyecto) [B:C,M:C]

**Paso 5b — Entidades asociativas:** si la M:N se modeló como entidad asociativa (para poder relacionarse con otras entidades), se mapea en dos etapas:
1. La entidad asociativa se convierte en relación **exactamente igual que en el Paso 5**. Sus dos relaciones asociativas no generan relaciones propias: son las dos FKs que forman su PK.
2. Sus relaciones con **otras** entidades se mapean con las reglas normales de los **Pasos 3 o 4**, tratándola como una relación más.

**Ejemplo** (Tema 5, sección 5.5): `Requisito_Puesto` entre `Puesto_de_Trabajo` y `Competencia`, validado por un `Validador` (`Requisito_Puesto[0..N]` ↔ `Validador[1..1]`):
- REQUISITO_PUESTO(<u>***puesto***, ***competencia***</u>, ***validador***)
	- ***puesto*** -> PUESTO_DE_TRABAJO(id_puesto) [B:C,M:C]
	- ***competencia*** -> COMPETENCIA(id_competencia) [B:C,M:C]
	- ***validador*** `NOT NULL` -> VALIDADOR(id_validador) [B:R,M:C]

---

### Paso 6 — Unarias (recursivas)

**No hay reglas nuevas**: se aplica el Paso 3, 4 o 5 según la cardinalidad. La única particularidad es **renombrar las FKs usando los roles** del E/R para que se entiendan (una FK llamada `dni` apuntando a `dni` no dice nada; `supervisor` sí):
- Con **Enfoque de Clave Externa**, se renombra la FK con el rol de su lado.
- Con **Enfoque de Relación Adicional**, se renombran las dos FKs, cada una con su rol.

**EMPRESA:** `Empleado[0..1 | "Supervisor"]` `supervisa` `Empleado[0..N | "Supervisado"]` → es una 1:N (Paso 4, caso general):
- EMPLEADO(<u>dni</u>, ..., ***dpto***, ***supervisor***)
	- ***supervisor*** -> EMPLEADO(dni) [B:N,M:C]

> 💡 Fíjate en la opción de borrado **N** (puesta a nulos): si se borra el supervisor, sus supervisados se quedan sin supervisor, no se borran en cascada.

**Ejemplo M:N** (The Expanse): `Mineral[0..N | "Mineral_A"]` `relacionado_con` `Mineral[0..N | "Mineral_B"]` → Paso 5, con las dos FKs renombradas:
- MINERAL_RELACIONADO(<u>***mineral_a***, ***mineral_b***</u>)
	- ***mineral_a*** -> MINERAL(codigo) [B:C,M:C]
	- ***mineral_b*** -> MINERAL(codigo) [B:C,M:C]

---

### Paso 7 — Relaciones de grado n>2 (ternarias)

Como una M:N: **Enfoque de Relación Adicional** con las PKs de las tres (o más) entidades como FKs + atributos propios.

Lo único que cambia es cómo elegir la **PK compuesta**, según las **cardinalidades máximas**:

| Cardinalidades máximas | PK de la relación adicional |
|---|---|
| N, N, N | Las tres claves: (`id_e_1`, `id_e_2`, `id_e_3`) |
| N, N, 1 | Las dos claves de los lados N: (`id_e_1`, `id_e_2`) |
| N, 1, 1 | La clave del lado N + una cualquiera de las otras dos (hay varias PKs válidas) |
| 1, 1, 1 | Cualquier par de claves (hay varias PKs válidas) |

> 🔑 Regla mnemotécnica: la clave de **un** lado con cardinalidad máxima 1 puede quedar fuera de la PK, porque ese lado ya queda determinado por el resto. Pero solo la de uno: si hay dos o tres lados con máxima 1, la PK sigue necesitando dos claves.

**Ejemplo:** `Proveedor[1..N]` — `suministra` — `Repuesto[1..N]` — `Proyecto[1..N]` (N en todos los lados):
- SUMINISTRA(<u>***proveedor***, ***proyecto***, ***repuesto***</u>, cantidad)
	- ***proveedor*** -> PROVEEDOR(id_proveedor) [B:C,M:C]
	- ***proyecto*** -> PROYECTO(id_proyecto) [B:C,M:C]
	- ***repuesto*** -> REPUESTO(id_repuesto) [B:C,M:C]

---

### Paso 8 — Jerarquías (Especialización/Generalización)

Antes de elegir, responde dos preguntas: **¿total o parcial? ¿disjunta o solapada?** Y decide qué prefieres penalizar: ¿`JOIN`s o `NULL`s?

| Opción | Relaciones creadas | ¿Cuándo es válida? | Ventaja | Coste |
|---|---|---|---|---|
| **8A** Superclase + subclases | 1 por superclase + 1 por subclase (misma PK, que en las subclases es también FK) | **SIEMPRE** ✅ | Sin redundancia, sin `NULL`s | Requiere `JOIN`s |
| **8B** Solo subclases | 1 por subclase (con los atributos comunes repetidos) | Solo **total y disjunta** | Consultas rápidas por subclase | Redundancia; consultar "toda la superclase" exige `UNION` |
| **8C** Relación única + atributo de tipo | 1 sola, con un atributo discriminante (`puesto`) | Solo **disjunta** (total o parcial) | Sin `JOIN`s | Muchos `NULL`s |
| **8D** Relación única + booleanos | 1 sola, con un atributo booleano por subclase | Cualquiera; la única de relación única válida para **solapadas** | Sin `JOIN`s | Muchos `NULL`s |

**Ejemplo 8A** (la recomendada por defecto), jerarquía `por_puesto` de `Empleado` → {`Administrativo`, `Tecnico`, `Ingeniero`}:
- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono)
- ADMINISTRATIVO(<u>***dni***</u>, categoria)
	- ***dni*** -> EMPLEADO(dni) [B:C,M:C]
- TECNICO(<u>***dni***</u>, nivel)
	- ***dni*** -> EMPLEADO(dni) [B:C,M:C]
- INGENIERO(<u>***dni***</u>, tipo_ingeniero)
	- ***dni*** -> EMPLEADO(dni) [B:C,M:C]

**Ejemplo 8B** (jerarquía total y disjunta de `Vehiculo` → {`Coche`, `Camion`}):
- COCHE(<u>id_vehiculo</u>, matricula, precio, max_velocidad, numero_pasajeros)
- CAMION(<u>id_vehiculo</u>, matricula, precio, tonelaje, numero_ejes)

**Ejemplo 8C** (la jerarquía `por_puesto`, en una sola relación):
- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono, **puesto**, categoria, nivel, tipo_ingeniero)

**Ejemplo 8D** (jerarquía total y solapada de `Persona_UEX` → {`Estudiante`, `Empleado`}):
- PERSONA_UEX(<u>dni</u>, nombre, apellido_1, apellido_2, **es_estudiante**, **es_empleado**, nota_selectividad, puesto, salario)

---

## 4. Resultado final: esquema relacional de EMPRESA

- EMPLEADO(<u>dni</u>, nombre, apellido_1, apellido_2, fecha_nacimiento, direccion, sexo, sueldo, ***dpto***, ***supervisor***)
	- ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
	- ***supervisor*** -> EMPLEADO(dni) [B:N,M:C]
- DEPARTAMENTO(<u>numero_departamento</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ***director***, fecha_ingreso_director)
	- ***director*** `NOT NULL`, `UNIQUE` -> EMPLEADO(dni) [B:R,M:C]
- PROYECTO(<u>numero_proyecto</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ubicacion, ***dpto***)
	- ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
- FAMILIAR(<u>***empleado***, nombre</u>, sexo, fecha_nacimiento, relacion)
	- ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
- UBICACION_DEPARTAMENTO(<u>***dpto***, nombre</u>)
	- ***dpto*** -> DEPARTAMENTO(numero_departamento) [B:C,M:C]
- TRABAJA_EN(<u>***empleado***, ***proyecto***</u>, horas)
	- ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
	- ***proyecto*** -> PROYECTO(numero_proyecto) [B:C,M:C]

---

## 5. Autoevaluación rápida

1. ¿Por qué en una 1:1 con participación `E_1[1..1]` ↔ `E_2[0..1]` la FK va a `E_2` y no a `E_1`?
2. ¿Qué restricción convierte una FK "normal" en una FK que respeta cardinalidad máxima 1?
3. En una 1:N `E_1[X..1]` ↔ `E_2[X..N]`, ¿qué cardinalidad mínima decide si la FK de `E_2` admite `NULL`?
4. ¿Cuándo compensa usar relación adicional en una 1:N en vez de la FK directa? (3 motivos)
5. En una ternaria con cardinalidades máximas N, N, 1, ¿qué claves forman la PK? ¿Y con N, 1, 1?
6. ¿Qué opción de mapeo de jerarquías funciona siempre? ¿Cuál es la única de relación única válida para jerarquías solapadas?
7. ¿Por qué la FK `supervisor` de EMPLEADO tiene opción de borrado N (nulos) y no C (cascada)?
8. ¿Por qué conviene llamar `capital` a la FK de `PROVINCIA` en lugar de `id_municipio`?

<details><summary>Respuestas</summary>

1. Para evitar `NULL`s: `E_2` está obligada a relacionarse, así que siempre tendrá valor en la FK; en `E_1` habría filas sin capital asociada.
2. `UNIQUE` (y `NOT NULL` si además la participación es total).
3. La escrita junto a `E_1`: con `[1..1]`, toda instancia de `E_2` está relacionada y la FK es `NOT NULL`; con `[0..1]`, puede no estarlo y la FK admite `NULL`.
4. Muchos `NULL`s esperados, evolución prevista a M:N, o muchos atributos propios de la relación.
5. Con N, N, 1: las claves de los dos lados N. Con N, 1, 1: la del lado N y una cualquiera de las otras dos.
6. La 8A (superclase + subclases). Para solapadas con relación única, solo la 8D (booleanos).
7. Porque al borrar un supervisor no queremos borrar a sus supervisados, solo dejarlos temporalmente sin supervisor.
8. Porque deja claro el papel de la referencia y evita colisiones si la relación necesita otra FK hacia `MUNICIPIO`, como `municipio_mas_poblado`.

</details>
>>>>>>> upstream/main
