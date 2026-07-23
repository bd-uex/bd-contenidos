# Tema 5 · El Modelo Entidad-Relación Extendido — Guía de Repaso

---
tags: #database, #summary, #entity-relationship
origin: Versión condensada de los apuntes del Tema 5 (basados en Elmasri & Navathe, 5ª ed.)
---

> [!info] Cómo usar esta guía
> Este documento resume los conceptos fundamentales del Tema 5 para un repaso ágil. No sustituye a los apuntes completos: allí encontrarás los ejemplos desarrollados, la sintaxis detallada de BigER y los diagramas. Al final tienes una chuleta-resumen y las preguntas de autoevaluación del tema.

---

## 1. ¿Por qué existe el modelo ER?

El Modelo Relacional (Codd) resuelve **cómo almacenar** datos de forma consistente, pero no dice nada sobre **cómo diseñar** una base de datos a partir de la realidad de un negocio. Deja tres problemas abiertos:

1. **Semántica**: una tabla con columnas no dice si representa clientes, pacientes o pedidos.
2. **Comunicación**: un experto de negocio no puede validar un diseño hecho de tablas normalizadas y claves foráneas.
3. **Independencia tecnológica**: el "plano" conceptual de los datos no debería depender de si al final usamos una BD relacional u otra tecnología.

**La solución de Chen (1976)**: el Modelo Entidad-Relación, un modelo de alto nivel para el **diseño conceptual**. No compite con el relacional: es el paso previo.

### El flujo de trabajo en tres fases

| Fase | Qué hacemos | Resultado |
|---|---|---|
| **1. Conceptual** | Hablamos con los usuarios y modelamos la realidad | Diagrama ER |
| **2. Lógica** | Traducimos el diagrama ER con reglas sistemáticas | Esquema relacional |
| **3. Física** | Implementamos el esquema en un SGBD concreto | BD en MySQL, PostgreSQL... |

> [!tip] Notación que usamos
> En lugar de la notación clásica de Chen, usamos **BigER** (lenguaje textual: el modelo es código, versionable en Git) + notación **Crow's Foot** (estándar de la industria para visualizar). La lógica vive en el texto; el diagrama es solo su representación.

---

## 2. Entidades y Atributos: los sustantivos y sus adjetivos

| Concepto | Qué es | Analogía de la ficha |
|---|---|---|
| **Tipo de Entidad** | Plantilla/categoría de un objeto del minimundo (`EMPLEADO`) | Ficha en blanco |
| **Entidad** | Un objeto concreto ("José Pérez") | Ficha rellenada |
| **Atributos** | Propiedades que describen al tipo (`Nombre`, `DNI`) | Campos de la ficha |
| **Conjunto de entidades** | Todas las entidades de un tipo **en un momento dado** | El archivador con todas las fichas |

El tipo de entidad es la **intención** (el plano); el conjunto de entidades es la **extensión** (el edificio construido).

### 2.1 Tipos de atributos

- **Simple ⚛️**: no se puede dividir en partes con significado (`DNI`, `Sexo`).
- **Compuesto 🧩**: formado por otros atributos (`NombreCompleto` = `Nombre` + `Apellido1` + `Apellido2`). Regla práctica: si vamos a hacer búsquedas por las partes, las modelamos como atributos simples separados.
- **Multivalor 📇**: puede tener varios valores para la misma entidad (`Teléfono` de un empleado).

> [!warning] Regla clave
> Un atributo **multivalor siempre acabará convertido en una entidad débil** en la versión final del diagrama (ver sección 5).

### 2.2 Atributos clave 🔑

- **Atributo clave (clave primaria)**: valor **único** para cada entidad (`DNI`, `Matrícula`).
- **Claves candidatas**: todos los atributos que podrían ser clave (para `COCHE`: `VIN` y `Matrícula`).
- Elegimos una como **primaria**; el resto son **claves alternativas**. Se prefiere una clave simple si es posible.

### 2.3 Otras propiedades de los atributos

- **Obligatorio** vs. **opcional** (¿puede quedar en blanco?).
- **Almacenado** vs. **derivado 🔢**: un atributo derivado (`Edad`) **no se guarda**, se calcula (a partir de `FechaNacimiento`). Evita inconsistencias.
- **Dominio**: el conjunto de valores posibles del atributo (como el "tipo de dato").

---

## 3. Relaciones: los verbos del modelo

Las **relaciones** son las asociaciones con significado entre entidades: un `EMPLEADO` **trabaja en** un `PROYECTO`, un `EMPLEADO` **dirige** un `DEPARTAMENTO`.

- **Tipo de relación** (intención): la plantilla de la asociación (`TRABAJA_EN` conecta `EMPLEADO` y `PROYECTO`).
- **Conjunto/instancias de relación** (extensión): las conexiones reales (`(Laura, Investigación)` en `DIRIGE`).
- **Grado**: nº de tipos de entidad que conecta. Casi siempre **binarias** (grado 2).
- Entre dos mismas entidades puede haber **varias relaciones distintas** (`TRABAJA_PARA` y `DIRIGE` entre `EMPLEADO` y `DEPARTAMENTO`): es perfectamente válido, describen reglas de negocio diferentes.

> [!important] Efecto sobre los atributos
> Al crear una relación, **desaparecen los atributos que la "simulaban"**: si creamos `DIRIGE`, se elimina el atributo `Director` de `DEPARTAMENTO`. Ver sección 4.2.

### 3.1 Restricciones estructurales: las reglas del juego

Toda relación lleva **dos reglas** que responden a dos preguntas:

**A) Cardinalidad máxima (razón de cardinalidad)** — *¿con cuántas te puedes relacionar como máximo?*

| Tipo | Significado | Ejemplo |
|---|---|---|
| **1:1** | Máximo una en cada lado | `DIRIGE`: un empleado dirige un dpto., un dpto. tiene un director |
| **1:N** | El lado "1" se relaciona con muchas; el lado "N" con solo una | `TRABAJA_PARA`: un dpto. tiene muchos empleados; un empleado, un dpto. |
| **M:N** | Muchas por ambos lados | `TRABAJA_EN`: un empleado en varios proyectos y viceversa |

**B) Cardinalidad mínima (participación)** — *¿es obligatorio participar?*

| Tipo | Significado | Ejemplo |
|---|---|---|
| **Total (1..)** | Toda entidad **debe** participar | Todo empleado debe pertenecer a un departamento |
| **Parcial (0..)** | Puede existir sin participar | La mayoría de empleados no dirigen ningún departamento |

En BigER ambas se escriben juntas: `Entidad1[MIN..MAX] -> Entidad2[MIN..MAX]`. Por ejemplo, `Provincia[1..1] -> Municipio[1..N]`. Las combinaciones posibles (`0..1`, `1..1`, `0..N`, `1..N`) surgen de aplicar las dos preguntas a cada lado.

### 3.2 Relaciones recursivas 🎭

Una entidad puede relacionarse **consigo misma** (`EMPLEADO` `SUPERVISA` `EMPLEADO`). Para que tenga sentido, cada participación lleva un **rol**: un empleado actúa como "supervisor" y otro como "supervisado". Como los actores de un teatro: mismo grupo, papeles distintos.

### 3.3 Atributos en las relaciones

Cuando una característica no describe a una entidad sino a la **interacción entre ellas**, el atributo pertenece a la relación.

**Ejemplo**: las `Horas` semanales que un empleado dedica a un proyecto no son del `EMPLEADO` (varían por proyecto) ni del `PROYECTO` (varían por empleado): son de `TRABAJA_EN`.

**Regla general**:
- En **M:N** → el atributo **debe** ir en la relación.
- En **1:N** → se puede mover a la entidad del lado "N".
- En **1:1** → se puede mover a cualquiera de las dos.

---

## 4. Criterios de buen diseño (I): decisiones clave

### 4.1 ¿Entidad o atributo? 🤔

**Regla de oro**: es **entidad** si necesitas almacenar información descriptiva sobre el concepto; si solo necesitas su valor, es **atributo**.

- `Capital` de un `PAIS`: si solo quieres el nombre → atributo. Si quieres habitantes, extensión, etc. → entidad `CIUDAD` + relación `esCapital`.
- **Estandarizar con entidades**: si un valor se asocia a muchas entidades (ej. `Provincia` en `MUNICIPIO`), mejor entidad propia: evita errores de escritura.

### 4.2 El error más común 🚫

> [!danger] Nunca uses la clave de otra entidad como atributo
> El identificador de una entidad **NUNCA** aparece como atributo en otra entidad. La conexión siempre es una **relación**. Guardar el `NIF` del profesor como atributo `responsable` en `ASIGNATURA` mezcla el nivel conceptual con el de implementación. Lo correcto: relación `RESPONSABLE` entre `PROFESOR` y `ASIGNATURA`. (Las claves foráneas aparecerán después, en la fase lógica, no en la conceptual.)

### 4.3 Otros criterios

- **Evita la "super entidad"**: no modelar toda la organización como una única entidad gigante (`EMPRESA`). Descompón el minimundo en sus partes significativas.
- **La claridad es la reina**: nombres legibles y autoexplicativos. Piensa en quien leerá el modelo dentro de seis meses (probablemente tú).

---

## 5. Entidades Débiles 🤝

### 5.1 Fuertes vs. débiles

- **Entidad fuerte**: tiene clave propia que la identifica de forma única (`EMPLEADO` con su `dni`).
- **Entidad débil**: **no puede identificarse solo con sus atributos**. `FAMILIAR` con `nombre` no basta: puede haber muchos familiares llamados "María García". Su identidad completa es `dni` del empleado + `nombre` del familiar.

### 5.2 Las DOS condiciones (ambas necesarias)

1. **Dependencia de existencia**: si desaparece la entidad de la que depende, la débil deja de tener sentido (borramos al empleado → sus familiares sobran). ⚠️ Esta condición **sola no basta**.
2. **Dependencia de identificación** (la decisiva): necesita "tomar prestada" la clave de su entidad fuerte para identificarse de forma única.

### 5.3 Anatomía de una entidad débil

| Elemento                               | Qué es                                                                                              | En el ejemplo   |
| -------------------------------------- | --------------------------------------------------------------------------------------------------- | --------------- |
| **Entidad propietaria**                | La entidad fuerte de la que depende                                                                 | `Empleado`      |
| **Relación débil (de identificación)** | Siempre con cardinalidad `1..1` en el lado fuerte y `0..N` o `1..N` en el débil                     | `familiar_de`   |
| **Clave parcial (discriminante)**      | Atributo que distingue a la débil **entre sus "hermanas"** (las que dependen del mismo propietario) | `nombre`        |
| **Clave primaria completa**            | Clave del propietario + clave parcial                                                               | `{dni, nombre}` |

En BigER: `weak entity`, `partial-key` y `weak relationship`.

### 5.4 Los casos de uso (¡solo estos!)

**Caso 1 — Atributos multivalor de una entidad.**
`CURSO` tiene varias `ediciones`, y cada edición tiene sus propios datos (`fechaInicio`, `fechaFin`, `aula`). El atributo multivalor se convierte en la entidad débil `EDICION` que depende de `CURSO`, con clave parcial `fechaInicio`.

**Caso 2 — Relaciones M:N "con historia" (con repetición).**
Un `PROFESOR` imparte una `ASIGNATURA` en varios cursos académicos: el mismo par (profesor, asignatura) se repite, así que el atributo `curso` de la relación sería multivaluado. **Solución**: "reificar" la relación en una entidad débil histórica (`DOCENCIA`, con clave parcial `curso`). La pregunta clave es: **¿de quién debe depender la nueva entidad?** La respuesta la da la cardinalidad de la relación **en un instante de tiempo**:

| Caso                       | Regla de negocio en una fecha dada                                             | Dependencia                                                                     | Ejemplo                                                                                                                                                                    |
| -------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **2.1 Doble**              | Una instancia de E1 con **varias** de E2, y viceversa (M:N en el instante)     | De **ambas** entidades                                                          | `Matricula`: un estudiante cursa varias asignaturas ese año, y una asignatura tiene varios estudiantes. Clave: `{numero_expediente, codigo, curso}`                        |
| **2.2 Simple a elegir**    | Una de E1 con **una** de E2, y viceversa (1:1 en el instante)                  | De **una u otra**, según el foco del negocio                                    | Vehículo de empresa: en una fecha, un empleado tiene un vehículo y un vehículo un empleado. Clave: `{id_empleado, fecha_asignacion}` **o** `{matricula, fecha_asignacion}` |
| **2.3 Simple obligatoria** | Una de E1 con **una** de E2, pero E2 con **varias** de E1 (1:N en el instante) | **Obligatoriamente** del lado "N" (el que solo tiene una relación en esa fecha) | Historial de departamentos: `{id_empleado, fecha_inicio}` es único; `{id_dpto, fecha_inicio}` no (varios empleados pueden empezar el mismo día)                            |

**El test de unicidad** que hay que hacer siempre: ¿qué combinación `(clave de entidad, atributo de tiempo)` identifica de forma inequívoca cada evento? La entidad débil depende de la(s) entidad(es) que hagan única esa combinación.

### 5.5 Entidades asociativas: relacionar una entidad con una relación M:N

**Regla inquebrantable del modelo ER**: no se puede conectar una relación con otra relación. Un verbo no puede ser sujeto de otra acción.

**El problema**: un `PUESTO_DE_TRABAJO` requiere varias `COMPETENCIAS` (M:N sin repetición), y ahora un `VALIDADOR` debe certificar **cada asignación** puesto-competencia. El validador no certifica el puesto ni la competencia, sino **la relación entre ambos**... y no podemos conectarlo a la relación.

**La solución en 3 pasos** — "cosificar" la relación:

1. **Convertir la relación M:N en una entidad asociativa** (`ASOC_REQUISITO_DE_PUESTO`): cada instancia representa el hecho único de que un puesto requiere una competencia.
2. **Su clave es compuesta**: no aporta identificador propio; su identidad es la combinación de las claves que une: `{id_puesto, id_competencia}`. La relación M:N original se sustituye por **dos relaciones 1:N** hacia la asociativa.
3. **Conectar**: ahora que es una entidad, puede relacionarse con `VALIDADOR` mediante una relación binaria normal.

> [!note] Diferencia con el Caso 2 de entidades débiles
> La entidad asociativa funciona porque la relación M:N **no se repite en el tiempo** (un puesto requiere una competencia una sola vez). Si se repitiera (revisión anual con histórico), añadiríamos un atributo temporal a la clave y estaríamos en el Caso 2 anterior.
>
> Convención en BigER: nombre con prefijo `ASOC_` y las dos claves de las entidades unidas marcadas como `key`.

---

## 6. Relaciones Ternarias (N-arias)

### 6.1 El concepto: el "hecho indivisible" 📞

Una relación ternaria **NO** es un atajo para tres relaciones binarias. **No son equivalentes.**

> **Analogía de la videoconferencia**: tres llamadas separadas entre tú-Ana, Ana-Carlos y tú-Carlos son tres eventos distintos. Una videollamada con los tres a la vez es **un único evento indivisible**. La ternaria solo modela el segundo caso.

### 6.2 ¿Cuándo usar cada una? Los dos tests

**Test del atributo (→ ternaria ✅)**: hay un atributo que solo tiene sentido conociendo a los tres participantes a la vez.
- Ejemplo `SUMINISTRA`: la `Cantidad` de repuestos no pertenece a (proveedor, repuesto), ni a (proyecto, repuesto), ni a (proveedor, proyecto). Solo tiene sentido en el hecho completo: "Acme suministró 5000 tornillos M8 al Puente A". Si lo descompusiéramos en binarias, **perderíamos el hecho central**.

**Test de la descomposición (→ binarias ✅)**: el "hecho" se puede separar en reglas de negocio independientes que tienen sentido por sí solas.
- Contraejemplo `IMPARTE` (profesor, asignatura, aula): "La profesora Sanz es responsable de BD" es un hecho válido sin conocer el aula, y "BD se imparte en el Aula 101" lo es sin conocer al profesor. → **Dos relaciones binarias** (típicamente con una entidad asociativa `ASOC_DOCENCIA` conectada a `AULA`). Además, es más flexible: permite asignar profesor antes que aula, o asignaturas sin aula ("Prácticas Externas").

> [!important] La gran lección
> Pregúntate: **¿el hecho es un evento único e indivisible, o se puede descomponer en reglas más pequeñas con sentido propio?** Indivisible → ternaria. Descomponible → binarias.

---

## 7. El Modelo Extendido (EER)

El EER incluye **todo el ER básico** y lo amplía con conceptos de la orientación a objetos: subclases/superclases, herencia, restricciones y jerarquías.

### 7.1 Subclases y superclases: la relación "ES UN" (IS-A)

Dentro de una entidad general (superclase `EMPLEADO`) hay subgrupos con características particulares (subclases `INGENIERO`, `ADMINISTRATIVO`, `GERENTE`...).

**Principios fundamentales**:
1. **Misma entidad, rol específico**: un ingeniero concreto es la **misma instancia** que ese empleado.
2. **Existencia dependiente**: nadie puede estar solo en la subclase; debe estar en la superclase.
3. **Pertenencia opcional (por defecto)**: una instancia puede estar en ninguna, una o varias subclases (según las restricciones que definamos).

### 7.2 Herencia 🧬 y el criterio fundamental de diseño 💡

Una subclase hereda automáticamente **todos los atributos** de su superclase (incluida la clave) y **todas sus relaciones** (si `EMPLEADO` `TRABAJA_PARA` un dpto., `INGENIERO` también).

> [!important] ¿Cuándo crear una subclase? SOLO si aporta algo propio
> Una subclase se justifica únicamente si tiene **atributos específicos** (`tipoIng` solo para `INGENIERO`) o **participa en relaciones específicas** (`afiliadoA SINDICATO` solo para `TIEMPO_PARCIAL`). Una subclase "vacía" solo añade complejidad: esa distinción ya está en un atributo de la superclase.

### 7.3 Restricciones de la jerarquía

Dos preguntas independientes que se combinan:

| Restricción | Pregunta | Opciones |
|---|---|---|
| **Completitud** | ¿Toda instancia de la superclase debe estar en alguna subclase? | **Total (t)**: sí, sin "huecos" (∪ subtipos = supertipo). Ej.: `{TIEMPO_COMPLETO, TIEMPO_PARCIAL}` · **Parcial (p)**: puede no estar en ninguna. Ej.: `{ADMIN, TÉCNICO, INGENIERO}` |
| **Disyunción** | ¿Puede una instancia estar en varias subclases? | **Disjunta (d)**: máximo en una (∩ subtipos = ∅) · **Solapada (s)**: en varias a la vez. Ej.: `{INVESTIGADOR, DOCENTE}` |

Las combinaciones dan 4 tipos: **(t,d)**, **(t,s)**, **(p,d)**, **(p,s)**. En los diagramas a mano se anotan las letras junto a la especialización.

### 7.4 Especialización y generalización: dos caminos, mismo destino

- **Especialización (Top-Down 🔽)**: partimos de la superclase y detectamos subgrupos. De `EMPLEADO` derivamos `{ADMIN, TÉCNICO, INGENIERO}` por tipo de puesto.
- **Generalización (Bottom-Up 🔼)**: partimos de entidades separadas (`COCHE`, `CAMIÓN`), vemos atributos comunes (`matricula`, `precio`) y creamos la superclase `VEHÍCULO`; las originales quedan como subclases con solo sus atributos específicos.

En la práctica se combinan ambos. Además, la pertenencia a una subclase puede estar **definida por atributo** (automática: `TipoTrabajo = 'Ingeniero'` → subclase `INGENIERO`) o **definida por el usuario** (manual: quién es `GERENTE` lo decide el usuario).

### 7.5 Jerarquías vs. redes

- **Jerarquía (árbol 🌳)**: cada subclase tiene **una única** superclase directa (herencia simple).
- **Red/entramado (malla 🕸️)**: una subclase puede tener **varias** superclases (herencia múltiple; la subclase se llama *compartida*, ej. `INGENIERO_JEFE` hereda de `INGENIERO`, `GERENTE` y `TIEMPO_COMPLETO`). El EER lo permite, pero muchos sistemas (incluido BigER) no lo soportan. **En este curso trabajaremos con jerarquías.**

---

## 8. Chuleta final 📋

### El proceso de modelado, paso a paso

1. **Sustantivos** → Tipos de entidad (con sus atributos y claves).
2. **Verbos** → Relaciones (eliminando los atributos que las simulaban).
3. **Reglas de negocio** → Cardinalidades mín. y máx. en cada lado.
4. **Refinar**:
   - ¿Atributo multivalor? → entidad débil (Caso 1).
   - ¿Relación M:N con repetición temporal? → entidad débil histórica (Caso 2: analiza de quién depende).
   - ¿Conectar algo a una relación M:N sin repetición? → entidad asociativa (`ASOC_`).
   - ¿Tres entidades? → aplica el test del atributo / test de la descomposición.
   - ¿Subgrupos con atributos o relaciones propias? → especialización EER (anota t/p y d/s).

### Errores capitales a evitar 🚫

| Error | Corrección |
|---|---|
| Clave de otra entidad como atributo | Siempre una relación |
| Atributo multivalor en el diagrama final | Convertir en entidad débil |
| Ternaria para hechos descomponibles ("falso ternario") | Relaciones binarias (+ asociativa si hace falta) |
| Subclases sin atributos ni relaciones propias | Un simple atributo en la superclase |
| Una "super entidad" que lo modela todo | Descomponer el minimundo |
| Nombres crípticos | Nombres autoexplicativos |

### Preguntas de autoevaluación (del tema)

1. ¿Cuál sería la clave del tipo de entidad `FAMILIAR`?
2. ¿Qué pasa con el atributo `Horas` (de `Trabaja_en` en `EMPLEADO`) y con `FechaInicioDirector` (en `DEPARTAMENTO`) al introducir las relaciones?
3. ¿Es correcto `nombre` como clave primaria de `FAMILIAR`? ¿Y el atributo multivaluado `ubicaciones` en `DEPARTAMENTO`?
4. Explica el concepto de herencia en el EER. ¿Qué heredan las subclases?
5. ¿Cuándo modelaremos subclases?