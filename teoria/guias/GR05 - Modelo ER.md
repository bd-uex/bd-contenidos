# Tema 5 · El Modelo Entidad-Relación Extendido — Guía de Repaso

---
tags: #database, #summary, #entity-relationship
 origin: Versión condensada de los apuntes del Tema 5 (basados en Elmasri y Navathe, 5ª ed.)
 last-update: 2026-10-24
---

> [!info] Cómo usar esta guía
> Este documento resume los conceptos fundamentales del Tema 5 para un repaso ágil. No sustituye a los apuntes completos: allí encontrarás los ejemplos desarrollados, todos los diagramas y el anexo con la notación BigER completa. Al final tienes una chuleta, el resumen de los criterios de evaluación y preguntas de autoevaluación.

---

## 1. ¿Por qué existe el modelo ER?

El Modelo Relacional (Codd) resuelve **cómo almacenar** datos de forma consistente, pero no dice nada sobre **cómo diseñar** una base de datos a partir de la realidad de un negocio. Deja tres problemas abiertos:

1. **Semántica**: una tabla con columnas no dice si representa clientes, pacientes o pedidos.
2. **Comunicación**: un experto de negocio no puede validar un diseño hecho de tablas normalizadas y claves foráneas.
3. **Independencia tecnológica**: el "plano" conceptual de los datos no debería depender de si al final usamos una BD relacional u otra tecnología.

**La solución de Chen (1976)**: el Modelo Entidad-Relación, un modelo de alto nivel para el **diseño conceptual**. No compite con el relacional: es el paso previo.

### El flujo de trabajo en tres fases

| Fase              | Qué hacemos                                       | Resultado                  |
| ----------------- | ------------------------------------------------- | -------------------------- |
| **1. Conceptual** | Hablamos con los usuarios y modelamos la realidad | Diagrama ER                |
| **2. Lógica**     | Traducimos el diagrama ER con reglas sistemáticas | Esquema relacional         |
| **3. Física**     | Implementamos el esquema en un SGBD concreto      | BD en MySQL, PostgreSQL... |

> [!tip] Notación que usamos
> En lugar de la notación clásica de Chen, usamos **BigER** (lenguaje textual: el modelo es código, versionable en Git) y la notación **Crow's Foot** para visualizarlo. La lógica vive en el texto; el diagrama es su representación.

### Convenciones de nombres

| Elemento | Convención | Ejemplo |
|---|---|---|
| Entidad | Cada palabra con mayúscula inicial, unidas con guion bajo | `Cuenta_Bancaria` |
| Atributo | Todo en minúsculas, unido con guion bajo | `fecha_nacimiento` |
| Relación | Todo en minúsculas, unido con guion bajo | `es_titular_de` |
| Jerarquía | En minúsculas, nombrada por el **criterio** de clasificación | `por_jornada` |

Los identificadores no admiten tildes ni la letra ñ: `anio`, `curso_academico`, `direccion`.

---

## 2. Entidades y Atributos: los sustantivos y sus adjetivos

| Concepto | Qué es | Analogía de la ficha |
|---|---|---|
| **Tipo de entidad** | Plantilla o categoría de un objeto del minimundo (`Empleado`) | Ficha en blanco |
| **Entidad** | Un objeto concreto ("José Pérez") | Ficha rellenada |
| **Atributos** | Propiedades que describen al tipo (`nombre`, `dni`) | Campos de la ficha |
| **Conjunto de entidades** | Todas las entidades de un tipo **en un momento dado** | El archivador con todas las fichas |

El tipo de entidad es la **intensión** (el plano); el conjunto de entidades es la **extensión** (el edificio construido).

### 2.1 Tipos de atributos

- **Simple ⚛️**: no se puede dividir en partes con significado (`dni`, `sexo`).
- **Compuesto 🧩**: formado por otros atributos (un nombre completo = `nombre` + `apellido_1` + `apellido_2`). 
- **Multivalor 📇**: puede tener varios valores para la misma entidad. Ejemplo: las `ediciones` de un `Curso`, ya que de un mismo curso pueden haberse impartido varias ediciones.

> [!warning] Regla clave
> Un atributo **multivalor siempre acabará convertido en una entidad débil** en la versión final del diagrama (ver el Caso 1 de la sección 5).

### 2.2 Atributos clave 🔑

- **Clave primaria**: atributo con valor **único** para cada entidad. En BigER se marca con `key` y se dibuja subrayado.
- **Claves candidatas**: todos los atributos que podrían ser clave. Para un `Coche`: `vin` y `matricula`.
- Elegimos una como **primaria** (en BigER pondremos `key` a continuación y en el diagrama la subrayamos); el resto son **claves alternativas** (en BigER pondremos  `unique` y se dibujan con subrayado discontinuo). Se prefiere una clave simple si es posible.

```erdiagram
entity Coche {
    vin key
    matricula unique
    modelo
}
```
![250](../../imgs/BD%20-%20BigER%20Entidad%20Coche.png)

### 2.3 Otras propiedades de los atributos

- **Obligatorio** frente a **opcional**: ¿puede quedar en blanco?
- **Almacenado** frente a **derivado 🔢**: un atributo derivado (`edad`) **no se guarda**, se calcula a partir de otro (`fecha_nacimiento`). Así evitamos inconsistencias.
- **Dominio**: el conjunto de valores posibles del atributo, como el "tipo de dato".

---

## 3. Relaciones: los verbos del modelo

Las **relaciones** son las asociaciones con significado entre entidades: un `Empleado` **trabaja en** un `Proyecto`, un `Empleado` **dirige** un `Departamento`.

- **Tipo de relación** (intensión): la plantilla de la asociación (`trabaja_en` conecta `Empleado` y `Proyecto`).
- **Conjunto de relaciones** (extensión): las conexiones reales, como `(Laura Martínez, Investigación)` en `dirige`.
- **Grado**: número de tipos de entidad que conecta **una misma relación**. Casi siempre son **binarias** (grado 2).
- Entre dos mismas entidades puede haber **varias relaciones distintas** (`trabaja_para` y `dirige` entre `Empleado` y `Departamento`): es perfectamente válido, porque describen reglas de negocio diferentes.

> [!important] Efecto sobre los atributos
> Al crear una relación, **desaparecen los atributos que la "simulaban"**: si creamos `dirige`, se elimina el atributo `director` de `Departamento`. Ver la sección 4.2.

### 3.1 Restricciones estructurales: las reglas del juego

Toda relación lleva **dos reglas** en cada extremo, que responden a dos preguntas:

**A) Cardinalidad máxima** — *¿con cuántas te puedes relacionar como máximo?*

| Tipo | Significado | Ejemplo |
|---|---|---|
| **1:1** | Máximo una en cada lado | `dirige`: un empleado dirige un departamento y un departamento tiene un director |
| **1:N** | Una del lado "1" se relaciona con muchas del lado "N"; una del lado "N", con una sola del lado "1" | `trabaja_para`: un departamento tiene muchos empleados; un empleado, un solo departamento |
| **M:N** | Muchas por ambos lados | `trabaja_en`: un empleado en varios proyectos y un proyecto con varios empleados |

**B) Cardinalidad mínima (participación)** — *¿es obligatorio participar?*

| Tipo | Significado | Ejemplo |
|---|---|---|
| **Total (1..)** | Toda entidad **debe** participar | Todo empleado debe pertenecer a un departamento |
| **Parcial (0..)** | Puede existir sin participar | La mayoría de los empleados no dirigen ningún departamento |

En BigER ambas se escriben juntas: `Entidad_1[MIN..MAX] -> Entidad_2[MIN..MAX]`, con `MIN` igual a 0 o 1 y `MAX` igual a 1 o N.

> [!important] Cómo se lee una cardinalidad en BigER
> La cardinalidad escrita **junto a una entidad** indica con cuántas instancias **de esa entidad** se relaciona **una** instancia de la otra.
> En `Provincia[1..1] -> Municipio[1..N]`:
> - `Municipio[1..N]`: una provincia contiene entre 1 y N municipios.
> - `Provincia[1..1]`: un municipio pertenece exactamente a una provincia.

**En el diagrama Crow's Foot**, cada extremo lleva dos marcas. La más cercana a la entidad indica la **máxima** (barra = 1, pata de gallo = N) y la más alejada, la **mínima** (círculo = 0, barra = 1).

![\|180](../../imgs/BD%20-%20BigER%20Relación%2011%201N.png)  

### 3.2 Relaciones recursivas 🎭

Una entidad puede relacionarse **consigo misma** (`Empleado` `supervisa` `Empleado`). Para que tenga sentido, cada participación lleva un **rol**: un empleado actúa como supervisor y otro como supervisado. Como los actores de un teatro: mismo grupo, papeles distintos. En BigER el rol se escribe tras la cardinalidad:

```erdiagram
relationship supervisa {
    Empleado[0..1 | "Supervisor"] -> Empleado[0..N | "Supervisado"]
}
```

### 3.3 Atributos en las relaciones

Cuando una característica no describe a una entidad sino a la **interacción entre ellas**, el atributo pertenece a la relación.

**Ejemplo**: las `horas` semanales que un empleado dedica a un proyecto no son del `Empleado` (varían según el proyecto) ni del `Proyecto` (varían según el empleado): son de `trabaja_en`.

```erdiagram
relationship trabaja_en {
    Empleado[1..N] -> Proyecto[0..N]
    horas
}
```

**Regla general**:
- En **M:N**, el atributo **debe** ir en la relación.
- En **1:N**, se puede mover a la entidad del lado "N".
- En **1:1**, se puede mover a cualquiera de las dos.

![\|380](../../imgs/BD%20-%20BigER%20Atributo%20de%20Relacion.png)`

---

## 4. Criterios de buen diseño: decisiones clave

### 4.1 ¿Entidad o atributo? 🤔

**Regla de oro**: es **entidad** si necesitas almacenar información descriptiva sobre el concepto; si solo necesitas su valor, es **atributo**.

- La `capital` de un `Pais`: si solo quieres su nombre, es un atributo. Si quieres sus habitantes, su extensión, etc., es una entidad `Ciudad` con una relación `es_capital_de`.
- **Estandarizar con entidades**: si un valor se repite en muchas entidades (el `genero` de una `Pelicula`), conviene una entidad propia. Evita errores de escritura.

### 4.2 El error más común 🚫

> [!danger] Nunca uses la clave de otra entidad como atributo
> El identificador de una entidad **nunca** aparece  en el modelo Entidad Relación como atributo en otra entidad. La conexión siempre es una **relación**. Guardar el `nif` del profesor como atributo `responsable` en `Asignatura` mezcla el nivel conceptual con el de implementación. Lo correcto es una relación `es_responsable_de` entre `Profesor` y `Asignatura`. Las claves foráneas aparecerán después, en la fase lógica, nunca en la conceptual. Es el fallo **G2**.

### 4.3 Otros criterios

- **Evita la "super entidad"**: no modeles toda la organización como una única entidad gigante (`Empresa`). Descompón el minimundo en sus partes significativas.
- **La claridad es la reina**: nombres legibles y autoexplicativos. Piensa en quien leerá el modelo dentro de seis meses (probablemente tú).

---

## 5. Entidades Débiles 🤝

### 5.1 Fuertes y débiles

- **Entidad fuerte**: tiene clave propia que la identifica de forma única (`Empleado` con su `dni`).
- **Entidad débil**: **no puede identificarse solo con sus atributos**. Un `Familiar` con su `nombre` no basta: puede haber muchos familiares llamados "María García". Su identidad completa es el `dni` del empleado más el `nombre` del familiar.

### 5.2 Las dos condiciones (ambas necesarias)

1. **Dependencia de existencia**: si desaparece la entidad de la que depende, la débil deja de tener sentido (si borramos al empleado, sus familiares sobran). ⚠️ Esta condición **sola no basta**.
2. **Dependencia de identificación** (la decisiva): necesita "tomar prestada" la clave de su entidad fuerte para identificarse de forma única.

### 5.3 Anatomía de una entidad débil

| Elemento | Qué es | En el ejemplo |
|---|---|---|
| **Entidad propietaria** | La entidad fuerte de la que depende | `Empleado` |
| **Relación débil (de identificación)** | Siempre con `[1..1]` en el lado de la fuerte y `[0..N]` o `[1..N]` en el de la débil | `familiar_de` |
| **Clave parcial (discriminante)** | Atributo que distingue a la débil **entre sus "hermanas"**, las que dependen del mismo propietario | `nombre` |
| **Clave primaria completa** | Clave del propietario + clave parcial | `{dni, nombre}` |

```erdiagram
weak entity Familiar {
    nombre partial-key
    sexo
    fecha_nacimiento
    relacion
}

weak relationship familiar_de {
    Empleado[1..1] -> Familiar[0..N]
}
```

En el diagrama, tanto la entidad débil como su relación débil se dibujan con **borde grueso**.

![\|220](../../imgs/BD%20-%20BigER%20Relación%20debil.png)

### 5.4 Los casos de uso (¡solo estos!)

**Caso 1 — Atributos multivalor de una entidad.**
Un `Curso` tiene varias ediciones, y cada edición tiene sus propios datos (`fecha_inicio`, `fecha_fin`, `aula`). El atributo multivalor se convierte en la entidad débil `Edicion`, que depende de `Curso`, con clave parcial `fecha_inicio`.

![](../../imgs/BD%20-%20BigER%20Debil%20Curso%20Edicion.png)

**Caso 2 — Relaciones M:N "con historia" (con repetición).**
Un `Profesor` imparte una `Asignatura` en varios cursos académicos: la misma pareja profesor-asignatura se repite, así que el atributo `curso_academico` de la relación sería multivalor. **Solución**: "reificar" la relación en una **entidad débil histórica** (`Docencia`, con clave parcial `curso_academico`).

![](../../imgs/BD%20-%20BigER%20Debil%20Docencia.png)

La pregunta clave es **de quién debe depender la nueva entidad**, y la respuesta la da la cardinalidad de la relación **en un instante de tiempo**:

| Caso                       | En una fecha dada...                                                                      | Depende de...                                                                                | Ejemplo y clave                                                                                                                                                                                                                                                     |
| -------------------------- | ----------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **2.1 Doble**              | Una instancia de cada entidad se relaciona con **varias** de la otra (M:N en el instante) | **Ambas** entidades                                                                          | `Matricula`: un estudiante cursa varias asignaturas ese año y una asignatura tiene varios estudiantes. Clave: `{numero_expediente, codigo, curso_academico}`<br>![](../../imgs/BD%20-%20BigER%20Debil%20Matricula.png)                                              |
| **2.2 Simple a elegir**    | Una de cada entidad con **una** de la otra (1:1 en el instante)                           | **Una u otra**, según el foco del negocio                                                    | Vehículo de empresa: en una fecha, un empleado tiene un vehículo y un vehículo un empleado. Clave: `{id_empleado, fecha_asignacion}` **o** `{matricula, fecha_asignacion}`<br>![](../../imgs/BD%20-%20BigER%20Debil%20Asignacion%20Historica%20Foco%20Empleado.png) |
| **2.3 Simple obligatoria** | Una de E1 con **una** de E2, pero una de E2 con **varias** de E1 (1:N en el instante)     | **Obligatoriamente** de E1, la entidad que en esa fecha solo se relaciona con una de la otra | Historial de departamentos: `{id_empleado, fecha_inicio}` es único; `{id_departamento, fecha_inicio}` no, porque varios empleados pueden incorporarse el mismo día<br>![](../../imgs/BD%20-%20BigER%20Debil%20Historial%20Departamento.png)                         |

**El test de unicidad** que hay que hacer siempre: ¿qué combinación de clave de entidad y atributo de tiempo identifica de forma inequívoca cada evento? La entidad débil depende de la entidad o entidades que hacen única esa combinación, **y solo de ellas** (depender de más es el fallo L15).

### 5.5 Entidades asociativas: relacionar una entidad con una relación M:N

**Regla inquebrantable del modelo ER**: no se puede conectar una relación con otra relación. Un verbo no puede ser sujeto de otra acción.

**El problema**: un `Puesto_de_Trabajo` requiere varias `Competencia` (M:N **sin repetición**), y ahora un `Validador` debe certificar **cada requisito** puesto-competencia. El validador no certifica el puesto ni la competencia, sino **la pareja**... y no podemos conectarlo a la relación.

**La solución — "cosificar" la relación**:

1. **Convertir la relación M:N en una entidad asociativa** (`Requisito_Puesto`): cada instancia representa el hecho único de que un puesto requiere una competencia.
2. **No declarar ninguna clave**: su identidad es la combinación de las claves de las entidades que asocia, `{id_puesto, id_competencia}`, y no se vuelve a escribir. La relación M:N original se sustituye por **dos relaciones asociativas**, con `[1..1]` en el lado de cada entidad.
3. **Conectar**: ahora que es una entidad, puede relacionarse con `Validador` mediante una relación normal.

```erdiagram
entity Puesto_de_Trabajo {
id_puesto key
nombre_puesto
}

entity Competencia {
id_competencia key
nombre_competencia
}

entity Validador {
id_validador key
rol_empresa
}

associative entity Requisito_Puesto {
}

associative relationship requiere {
    Puesto_de_Trabajo[1..1] -> Requisito_Puesto[1..N]
}

associative relationship es_requerida_por {
    Competencia[1..1] -> Requisito_Puesto[1..N]
}

relationship validado_por {
    Requisito_Puesto[0..N] -> Validador[1..1]
}
```

En el diagrama, la entidad asociativa se dibuja como un **rectángulo con un rombo dentro**, y sus relaciones asociativas como líneas sin rombo.

> [!warning] Reglas que valida BigER
> - Una entidad asociativa **no puede declarar claves** (`key` ni `partial-key`), ni ser débil a la vez.
> - Debe participar **al menos en dos** relaciones asociativas.
> - En cada relación asociativa, el extremo de la entidad normal **debe ser `[1..1]`**.
> - Puede tener atributos descriptivos, o ninguno. Una asociativa vacía no es un fallo M1.

![](../../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo.png)

### 5.6 ¿Qué hago con una relación M:N? Resumen de decisión

| Situación | Solución | Si te equivocas |
|---|---|---|
| La pareja **no se repite** y **no participa** en otras relaciones | Relación M:N, con sus atributos si los tiene | — |
| La pareja **no se repite** pero **participa** en otras relaciones | Entidad asociativa, **sin** atributo de tiempo | Añadir tiempo a la clave es el fallo **L13** |
| La pareja **se repite** en el tiempo | Entidad débil histórica, con el tiempo en la clave parcial | Dejarla como M:N es el fallo **L14** |

---

## 6. Relaciones Ternarias (N-arias)

### 6.1 El concepto: el "hecho indivisible" 📞

Una relación ternaria **no** es un atajo para tres relaciones binarias. **No son equivalentes.**

> **Analogía de la videoconferencia**: tres llamadas separadas entre tú y Ana, Ana y Carlos, y tú y Carlos son tres eventos distintos. Una videollamada con los tres a la vez es **un único evento indivisible**. La ternaria solo modela el segundo caso.

### 6.2 ¿Cuándo usar cada una? Los dos tests

**Test del atributo (→ ternaria ✅)**: hay un atributo que solo tiene sentido conociendo a los tres participantes a la vez.

Ejemplo `suministra`: la `cantidad` no pertenece a la pareja proveedor-repuesto, ni a proyecto-repuesto, ni a proveedor-proyecto. Solo tiene sentido en el hecho completo: "Acme suministró 5000 tornillos M8 al Puente A". Si lo descompusiéramos en binarias, **perderíamos el hecho central**.

```erdiagram
relationship suministra {
    Proveedor[1..N] -> Proyecto[1..N] -> Repuesto[1..N]
    cantidad
}
```

![](../../imgs/BD%20-%20BigER%20Ternaria%20Proveedor%20Proyecto%20Suministro.png)

**Test de la descomposición (→ binarias ✅)**: el "hecho" se puede separar en hechos más pequeños que tienen sentido por sí solos.

Contraejemplo `imparte` (profesor, asignatura, aula):
- **Hecho 1**: "La profesora Sanz imparte Matemáticas" es válido aunque todavía no se sepa el aula. Es el **encargo docente**, una M:N entre `Profesor` y `Asignatura`: Matemáticas tiene dos grupos, uno con la profesora Sanz y otro con el profesor Gil.
- **Hecho 2**: el aula **se apoya en el encargo**. La profesora Sanz da su grupo en el Aula 101 y el profesor Gil el suyo en el Aula 204: el aula no es un dato de la asignatura ni del profesor, sino de la pareja.

El hecho es **divisible**, así que no es ternaria. La solución: la entidad asociativa `Encargo_Docente`, relacionada con `Aula`. Son tres relaciones binarias: dos asociativas y una normal.

```erdiagram

entity Profesor {
nif key
nombre
apellido_1
apellido_2
}

entity Asignatura {
codigo key
nombre
}

entity Aula {
id_aula key
nombre unique
ubicacion
}

associative entity Encargo_Docente {
}

associative relationship imparte {
    Profesor[1..1] -> Encargo_Docente[1..N]
}

associative relationship corresponde_a {
    Asignatura[1..1] -> Encargo_Docente[1..N]
}

relationship se_imparte_en {
    Encargo_Docente[0..N] -> Aula[0..1]
}
```

El `Aula[0..1]` permite registrar un encargo antes de asignarle aula, o asignaturas que no la tienen ("Prácticas Externas"). Con una ternaria no se podría, porque cada instancia exige los tres participantes.

![](../../imgs/BD%20-%20BigER%20Ternaria%20Falsa.png)

> [!important] La gran lección
> Pregúntate: **¿el hecho es un evento único e indivisible, o se puede descomponer en hechos más pequeños con sentido propio?** Indivisible → ternaria. Descomponible → binarias.
> Y recuerda: "ternaria" se refiere al **grado de una relación**. Que en el modelo aparezcan tres entidades no lo convierte en ternario si ninguna relación las conecta a las tres a la vez.

---

## 7. El Modelo Extendido (EER)

El EER incluye **todo el ER básico** y lo amplía con conceptos de la orientación a objetos: subclases y superclases, herencia, restricciones y jerarquías.

### 7.1 Subclases y superclases: la relación "ES UN" (IS-A)

Dentro de una entidad general (la superclase `Empleado`) hay subgrupos con características particulares (las subclases `Ingeniero`, `Administrativo`, `Gerente`...).

**Principios fundamentales**:
1. **Misma entidad, rol específico**: un ingeniero concreto es la **misma instancia** que ese empleado.
2. **Existencia dependiente**: nadie puede estar solo en la subclase; debe estar también en la superclase.
3. **Pertenencia opcional (por defecto)**: una instancia puede estar en ninguna, una o varias subclases, según las restricciones que definamos.

### 7.2 Herencia 🧬 y el criterio fundamental de diseño 💡

Una subclase hereda automáticamente **todos los atributos** de su superclase, incluida la clave, y **todas sus relaciones**: si `Empleado` `trabaja_para` un departamento, `Ingeniero` también.

> [!important] ¿Cuándo crear una subclase? SOLO si aporta algo propio
> Una subclase se justifica únicamente si tiene **atributos específicos** (`tipo_ingeniero` solo para `Ingeniero`) o **participa en relaciones específicas** (`afiliado_a` con `Sindicato` solo para `Empleado_Tiempo_Parcial`). Una subclase "vacía" solo añade complejidad: esa distinción cabe en un atributo de la superclase (fallo **L17**).

### 7.3 Restricciones de la jerarquía

Dos preguntas independientes que se combinan:

| Restricción     | Pregunta                                                         | Opción   | En BigER      | Significado                                                                   |
| --------------- | ---------------------------------------------------------------- | -------- | ------------- | ----------------------------------------------------------------------------- |
| **Completitud** | ¿Toda instancia de la superclase debe estar en alguna subclase?  | Total    | `total`       | Toda instancia está al menos en una subclase. Ej.: jornada completa o parcial |
|                 |                                                                  | Parcial  | `partial`     | **Puede** no estar en ninguna. Ej.: administrativo, técnico o ingeniero       |
| **Disyunción**  | ¿Puede una instancia de la superclase estar en varias subclases? | Disjunta | `disjoint`    | Como máximo en una                                                            |
|                 |                                                                  | Solapada | `overlapping` | **Puede** estar en varias. Ej.: investigador y docente                        |

Se combinan en cuatro tipos: `total disjoint`, `total overlapping`, `partial disjoint` y `partial overlapping`. **Con una sola subclase** solo cabe `partial`, sin restricción de disyunción: no hay nada con lo que solaparse.

### 7.4 Las jerarquías en BigER

La jerarquía es un **elemento propio del modelo, con nombre**. Se declara una vez, con su superclase y sus restricciones, y cada subclase la extiende:

```erdiagram
hierarchy por_jornada Empleado total disjoint

entity Empleado_Tiempo_Completo extends por_jornada {
    dni key
    salario
}

entity Empleado_Tiempo_Parcial extends por_jornada {
    dni key
    horas_semanales
}
```

> [!warning] Los cuatro detalles que más se equivocan
> 1. El `extends` apunta al **nombre de la jerarquía**, no al de la superclase.
> 2. La jerarquía se nombra por el **criterio** de clasificación (`por_jornada`, `por_puesto`), no por la superclase.
> 3. Cada subclase **repite la clave** de la superclase, y debe ser la misma (fallo **M8** si no lo es). Es el único atributo que se repite: repetir otros atributos comunes es el fallo **M9**.
> 4. Una entidad solo puede tener **un** `extends`: BigER no admite herencia múltiple.

**Varias jerarquías sobre la misma superclase**: una entidad puede clasificarse por criterios distintos, cada uno con sus propias restricciones. Por eso el nombre por criterio es importante:

```erdiagram
hierarchy por_puesto Empleado partial disjoint
hierarchy por_responsabilidad Empleado partial
hierarchy por_jornada Empleado total disjoint
```

**Jerarquías multinivel**: una subclase puede ser a su vez superclase de otra jerarquía.

En el diagrama, cada subclase apunta con una flecha a una caja con las restricciones (por ejemplo, `total disjoint`), y esa caja apunta a la superclase.

![450](../../imgs/BD%20-%20BigER%20Jerarquia%20Empleado.png)

### 7.5 Especialización y generalización: dos caminos, mismo destino

- **Especialización (Top-Down 🔽)**: partimos de la superclase y detectamos subgrupos. De `Empleado` derivamos `Administrativo`, `Tecnico` e `Ingeniero` por el tipo de puesto.
- **Generalización (Bottom-Up 🔼)**: partimos de entidades separadas (`Coche`, `Camion`), vemos atributos comunes (`matricula`, `precio`) y creamos la superclase `Vehiculo`; las originales quedan como subclases con solo sus atributos específicos.

En la práctica se combinan ambos. Además, la pertenencia a una subclase puede estar **definida por atributo** (automática: `tipo_trabajo = 'Ingeniero'` implica la subclase `Ingeniero`) o **definida por el usuario** (manual: quién es `Gerente` lo decide el usuario).

### 7.6 Jerarquías y redes

- **Jerarquía (árbol 🌳)**: cada subclase tiene **una única** superclase directa (herencia simple).
- **Red o entramado (malla 🕸️)**: una subclase puede tener **varias** superclases (herencia múltiple; la subclase se llama *compartida*, como un `Ingeniero_Jefe` que hereda de `Ingeniero`, `Gerente` y `Empleado_Tiempo_Completo`). El EER lo permite, pero BigER no lo soporta. **En esta asignatura SOLO trabajaremos con jerarquías.**

![](../../imgs/BD%20-%20BigER%20Jerarquia%20Universidad.png)

---

## 8. Criterios de evaluación del diseño

Cada fallo resta puntos según su gravedad. Conocerlos es la mejor forma de saber qué revisar en tu diagrama.

### Fallos graves (G) 🚨 — 6 puntos por fallo

| Código | Fallo |
|---|---|
| **G1** | El diseño global está muy lejos de la solución correcta |
| **G2** | Incluir un atributo para representar lo que en realidad es una relación con otra entidad (la clave de otra entidad como atributo) |
| **G3** | Definir una jerarquía donde el subtipo no "ES UN" tipo del supertipo |
| **G4** | Usar una relación normal (por ejemplo, llamada `es`) cuando lo correcto es una jerarquía |
| **G5** | Crear un subtipo que a la vez es entidad débil de otra entidad distinta |

### Fallos medios (M) ⚠️ — 2 puntos por fallo

| Código | Fallo                                                                                   |
| ------ | --------------------------------------------------------------------------------------- |
| **M1** | Entidad fuerte sin atributos que la describan más allá de su clave                      |
| **M2** | Incluir entidades innecesarias                                                          |
| M3     | No incluir entidades necesarias                                                         |
| **M4** | Indicar incorrectamente la clave parcial                                                |
| **M5** | Cardinalidad distinta de `[1..1]` en el lado de la entidad fuerte de una relación débil |
| **M6** | Cardinalidad distinta de `[0..N]` o `[1..N]` en el lado de la entidad débil             |
| **M7** | No incluir una relación requerida por el enunciado                                      |
| **M8** | Claves primarias distintas en entidades de la misma jerarquía                           |
| **M9** | Repetir en los subtipos atributos comunes (no clave) del supertipo                      |

### Fallos leves (L) — 1 punto por fallo

| Tema | Códigos |
|---|---|
| **Claves** | **L1** clave primaria incorrecta · **L2** no indicar las claves alternativas |
| **Cardinalidades y relaciones** | **L3** cardinalidades distintas a las requeridas · **L4** omitir la relación actual cuando es inferible de un histórico · **L5** no indicar los roles de una recursiva · **L6** atributo redundante con una relación · **L7** no incluir los atributos de una relación · **L8** poner en una relación atributos de una entidad · **L9** relaciones innecesarias o redundantes |
| **Entidades débiles** | **L10** débil sin dependencia de identificación · **L11** débil sin dependencia de existencia · **L12** fuerte que debería ser débil · **L13** débil histórica sin repetición · **L14** no usar débil histórica habiendo repetición · **L15** depender de más entidades de las necesarias · **L16** no marcar la relación débil |
| **Jerarquías** | **L17** subtipo vacío · **L18** no crear un subtipo que sí tiene contenido propio · **L19** relacionar con el supertipo lo que es de un subtipo · **L20** relacionar con un subtipo lo que es del supertipo · **L21** restricciones de la jerarquía incorrectas |

---

## 9. Chuleta final 📋

### El proceso de modelado, paso a paso

1. **Sustantivos** → tipos de entidad, con sus atributos y claves (`key`, `unique`).
2. **Verbos** → relaciones, eliminando los atributos que las simulaban.
3. **Reglas de negocio** → cardinalidades mínima y máxima en cada extremo.
4. **Refinar**:
   - ¿Atributo multivalor? → entidad débil (Caso 1).
   - ¿Relación M:N cuya pareja se repite en el tiempo? → entidad débil histórica (Caso 2: analiza de quién depende).
   - ¿Relación M:N sin repetición que debe relacionarse con otra entidad? → entidad asociativa.
   - ¿Tres entidades? → aplica el test del atributo y el de la descomposición.
   - ¿Subgrupos con atributos o relaciones propias? → jerarquía, nombrada por su criterio, con sus restricciones.

### Palabras clave de BigER

| Para...                               | Se escribe                                                      |
| ------------------------------------- | --------------------------------------------------------------- |
| Entidad fuerte, débil o asociativa    | `entity`, `weak entity`, `associative entity`                   |
| Relación normal, débil o asociativa   | `relationship`, `weak relationship`, `associative relationship` |
| Clave primaria, parcial o alternativa | `key`, `partial-key`, `unique`                                  |
| Cardinalidad                          | `Entidad[0..1]`, `[1..1]`, `[0..N]`, `[1..N]`                   |
| Jerarquía                             | `hierarchy nombre Superclase total disjoint`                    |
| Subclase                              | `entity Subclase extends nombre_jerarquia`                      |
| Restricciones jerarquía               | `total` / `partial`, `disjoint` / `overlapping`                 |

### Errores capitales a evitar 🚫

| Error | Corrección |
|---|---|
| Clave de otra entidad como atributo (**G2**) | Siempre una relación |
| Atributo multivalor en el diagrama final | Convertirlo en entidad débil |
| Ternaria para hechos descomponibles ("falso ternario") | Relaciones binarias, con una asociativa si hace falta |
| Añadir tiempo a la clave de una M:N que no se repite (**L13**) | Relación M:N, o asociativa si participa en otras relaciones |
| `extends` apuntando a la superclase | `extends` apunta a la jerarquía |
| Subclases sin atributos ni relaciones propias (**L17**) | Un simple atributo en la superclase |
| Una "super entidad" que lo modela todo | Descomponer el minimundo |
| Nombres crípticos | Nombres autoexplicativos |

---

## 10. Preguntas de autoevaluación

**Del tema:**

1. ¿Cuál sería la clave del tipo de entidad `Familiar`?
2. ¿Qué ha pasado con el atributo `horas` en `Empleado` y con `fecha_ingreso_director` en `Departamento` al introducir las relaciones?
3. ¿Es correcto `nombre` como clave primaria de `Familiar`? ¿Y el atributo multivalor `ubicaciones` en `Departamento`?
4. Explica el concepto de herencia en el EER. ¿Qué heredan las subclases?
5. ¿Cuándo modelaremos subclases?

**De repaso:**

6. Un estudiante puede matricularse de la misma asignatura en varios cursos. ¿Relación M:N, entidad asociativa o entidad débil histórica? ¿De qué entidades depende y cuál es su clave?
7. ¿Por qué una entidad asociativa no declara ninguna clave? ¿En qué se diferencia de una entidad débil?
8. En el ejemplo del encargo docente, ¿por qué el aula no se relaciona directamente con la asignatura?
9. Una universidad clasifica a su personal por tipo de contrato (fijo o temporal) y, por separado, por su función (docente, investigador o ambas cosas). ¿Cuántas jerarquías declararías, cómo las llamarías y qué restricciones tendría cada una?
10. ¿Qué cardinalidad debe tener siempre el extremo de la entidad fuerte en una relación débil? ¿Y el de la entidad normal en una relación asociativa?
