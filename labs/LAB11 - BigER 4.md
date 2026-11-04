# Uso de Entidades Asociativas

## Objetivos

1.- Familiarizarse con la **sintaxis** de las entidades asociativas en BigER.

2.- Distinguir las **entidades asociativas** de las entidades débiles, y saber cuándo usar cada una.

3.- Aplicar la notación para resolver el **Enunciado ER 11 Colegio de Enseñanza de Primaria**, que combina todo lo visto en los laboratorios anteriores.

---
## Contenidos

### Antes de empezar

### Notación BigER

- 6.- Entidades asociativas

### Resolución del Enunciado ER 11 Colegio de Enseñanza de Primaria

---
## Antes de empezar

En este laboratorio vamos a crear dos archivos nuevos, con **New Empty ER Model** y con la línea `notation=crowsfoot` justo debajo de la línea `erdiagram`:

1. **EjemplosAsociativasBigER.erd**, para los ejemplos y el primer ejercicio.
2. **ModeloColegioPrimaria.erd**, para el enunciado del colegio.

Recuerda que la vista del diagrama se abre con **Ctrl+Alt+E** (**Cmd+Alt+E** en macOS).

---
## Introducción a la notación textual de BigER (6) Entidades asociativas

El modelo Entidad-Relación tiene una regla fundamental e inquebrantable: **una relación solo puede conectar entidades; es imposible que una relación conecte con otra relación**. Un verbo no puede ser el sujeto de otra acción. Pero a veces nos encontramos con una relación **muchos a muchos (M:N) sin repetición** que es tan importante que necesitamos conectarla con otras entidades.

#### El problema guiado: competencias de los puestos de trabajo

**El escenario inicial**: en el departamento de Recursos Humanos de una empresa se definen los puestos de trabajo y un catálogo de competencias profesionales. La relación entre ellos es M:N:

- Un puesto de trabajo (por ejemplo, "Analista de Datos") requiere **varias** competencias (por ejemplo, "SQL Avanzado" y "Visualización de Datos").
- Una competencia (por ejemplo, "Liderazgo de Equipos") es requerida por **varios** puestos de trabajo.

La regla de negocio es que una competencia se asigna **una sola vez** a cada puesto: la pareja puesto-competencia no se repite. Abre el archivo EjemplosAsociativasBigER.erd y escribe este modelo inicial:

```erdiagram
entity Puesto_de_Trabajo {
    id_puesto key
    nombre_puesto
}

entity Competencia {
    id_competencia key
    nombre_competencia
}

relationship requiere {
    Puesto_de_Trabajo[0..N] -> Competencia[0..N]
}
```

**El nuevo requisito (el muro conceptual)**: ahora, Recursos Humanos necesita que cada una de estas asignaciones esté certificada. Es decir, se debe registrar qué **validador** (un directivo o un experto) ha aprobado que la competencia "SQL Avanzado" es necesaria para el puesto "Analista de Datos".

El validador no certifica el puesto en general, ni la competencia en general, sino **la pareja** puesto-competencia. Si intentamos conectar la entidad Validador directamente a la relación `requiere`, nos topamos con el muro conceptual del modelo E/R: **es imposible conectar una relación con otra relación**.

#### La solución: "cosificar" la relación en una entidad asociativa

La solución es transformar la relación `requiere` en una **entidad asociativa**, que nace de una relación M:N para que esta pueda conectarse con otras partes del modelo.

**Paso 1: convertir la relación en una entidad.** La relación M:N se convierte en la entidad asociativa **Requisito_Puesto**. Cada instancia representa el hecho único de que un puesto concreto requiere una competencia concreta.

**Paso 2: analizar su identidad.** Requisito_Puesto **no aporta ningún identificador propio**: su identidad es la combinación de las claves de las entidades que une, `{id_puesto, id_competencia}`. Y esa combinación **no se escribe** en BigER ni aparece en el diagrama: se deduce de las relaciones. Tampoco necesita clave parcial, porque la pareja no se repite.

> [!warning] Una entidad asociativa no es una entidad débil
> Las dos dependen de otras entidades para identificarse, pero de forma distinta. La débil tiene una **clave parcial** propia que, combinada con la clave de su propietaria, la identifica. La asociativa **no declara ninguna clave**: su identidad es exactamente la pareja que une. BigER no permite que una entidad sea asociativa y débil a la vez.

**Paso 3: usar la entidad para conectar.** Ahora que Requisito_Puesto es una entidad, podemos conectarla con Validador mediante una relación normal, **validado_por**.

La notación utilizada en BigER es la siguiente:

```erdiagram
// La relación M:N se "cosifica" en una entidad asociativa, sin ninguna clave.
associative entity Entidad_Asociativa {
    atributo_1_si_lo_tiene
}

// La relación M:N original se sustituye por dos relaciones asociativas,
// una con cada entidad, con [1..1] en el lado de la entidad.
associative relationship entidad1_participa_en {
    Entidad_1[1..1] -> Entidad_Asociativa[1..N]
}

associative relationship entidad2_participa_en {
    Entidad_2[1..1] -> Entidad_Asociativa[1..N]
}

// Ahora la entidad asociativa, al ser una entidad, puede participar en otras relaciones.
relationship relacion_con_entidad_externa {
    Entidad_Asociativa[0..N] -> Entidad_Externa[1..1]
}
```

Donde:

- Palabras reservadas `associative entity`, que declaran la entidad asociativa.
- **Entidad_Asociativa**: el nombre que le damos, el que mejor represente el hecho que recoge.
- Palabras reservadas `associative relationship`, que declaran cada una de las relaciones que unen la entidad asociativa con las entidades de la relación original.

> [!important] Reglas que valida BigER
> - Una entidad asociativa **no puede declarar claves** (`key` ni `partial-key`), ni ser débil a la vez.
> - Debe participar **al menos en dos** relaciones asociativas.
> - En cada relación asociativa, el extremo de la entidad normal **debe ser `[1..1]`**: cada requisito es de exactamente un puesto y de exactamente una competencia.
> - Puede tener atributos descriptivos, o ninguno. Una entidad asociativa vacía **no** es un fallo M1.

Sustituye el modelo inicial de EjemplosAsociativasBigER.erd por el modelo completo del ejemplo:

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

En el diagrama, la entidad asociativa se dibuja como un **rectángulo con un rombo dentro**, y sus relaciones asociativas como líneas sin rombo:

![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo.png)

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: existe un Requisito_Puesto (un puesto de trabajo requiere una competencia).
2. **Hecho 2**: ese Requisito_Puesto concreto lo ha certificado un Validador.

#### Nota importante: la ausencia de repetición en el tiempo

Este patrón funciona porque la pareja puesto-competencia **no se repite**: un puesto requiere una competencia una sola vez. Si pudiera repetirse (por ejemplo, si las competencias de los puestos se revisaran cada año y quisiéramos guardar el histórico), necesitaríamos un atributo de tiempo, como `anio`, para distinguir las repeticiones. Y como una entidad asociativa no puede tener claves, **dejaría de ser asociativa**: sería una **entidad débil histórica**, con `anio` como clave parcial, que dependería de Puesto_de_Trabajo y de Competencia, como vimos en el laboratorio 2.

| Situación de la pareja | Solución |
|---|---|
| No se repite y no participa en otras relaciones | Relación M:N |
| No se repite y participa en otras relaciones | Entidad asociativa |
| Se repite en el tiempo | Entidad débil histórica |

---
### Ejercicio 01 - Entidad asociativa

Vamos a resolver el ejemplo del **falso ternario** que vimos en el Tema 5. En el mismo archivo EjemplosAsociativasBigER.erd, modela lo siguiente:

- De cada **profesor** se guarda su NIF, su nombre y sus dos apellidos.
- De cada **asignatura**, su código y su nombre.
- De cada **aula**, un identificador, un nombre que también es único y su ubicación.
- Un profesor puede impartir varias asignaturas, y una asignatura puede tener varios profesores, cada uno con su grupo. La pareja profesor-asignatura no se repite.
- Cada uno de estos **encargos docentes** (un profesor en una asignatura) se imparte en un aula como máximo: puede no tener aula todavía, o no necesitarla, como las Prácticas Externas. En un aula se pueden impartir varios encargos, o ninguno.

Antes de escribir nada, piensa: ¿con qué se relaciona el aula? ¿Con el profesor? ¿Con la asignatura? ¿O con la pareja?

Modela el encargo docente como una entidad asociativa llamada **Encargo_Docente**, con sus dos relaciones asociativas, y relaciónala con **Aula** mediante la relación **se_imparte_en**, de forma que se visualice así:

![](../imgs/BD%20-%20BigER%20Ternaria%20Falsa.png)

Escribe el texto BigER necesario y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender por qué no es una relación ternaria.

---
## Resolución del Enunciado ER 11 Colegio de Enseñanza de Primaria

Los ejercicios a continuación están orientados a resolver el Enunciado ER 11 Colegio de Enseñanza de Primaria, disponible [aquí](../ejercicios/entidad-relacion/Enunciado%20ER%2011%20Colegio%20de%20Ense%C3%B1anza%20de%20Primaria.md).

Este enunciado combina todo lo que hemos visto: entidades, relaciones, una entidad débil, jerarquías y una entidad asociativa. Abre el archivo **ModeloColegioPrimaria.erd**, que será donde vamos a ir modelándolo.

---
### Ejercicio 02 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (1)

Vamos a comenzar centrándonos en los 3 primeros párrafos:

---
Un pequeño colegio de un pueblo, que solo imparte enseñanza de primaria desea organizar toda la información de que dispone para lograr una eficiente gestión de estudiantes, cursos, docentes y asignaturas. Los supuestos que quieren recoger inicialmente se especifican a continuación.

Cuando un estudiante ingresa en el colegio se deben facilitar sus datos personales (nombre, apellidos, sexo y fecha de nacimiento); el colegio le asigna un número de expediente único que se mantendrá a lo largo de toda su etapa escolar. Así mismo, dado que los estudiantes son habitualmente menores de edad, se debe recoger información de, al menos, uno de los tutores (puede ser la madre, el padre o cualquier otra persona a la que está a cargo) del estudiante. Para los tutores debe conocerse su nif, nombre y apellidos, profesión, dirección completa y un teléfono de contacto. También debe conocerse el tipo de relación que le une al estudiante, es decir, si la persona es la madre o el padre o cualquier otra relación. Además de la información anterior, si un estudiante sufre algún tipo de enfermedad, alergia o tiene algún grado de discapacidad debe reflejarse también, especificando una descripción de esta y si se requiere algún tipo de atención especial en el colegio.

Con respecto a los docentes, se debe mantener información personal del mismo (dni, nombre y apellidos, dirección y teléfono), el salario y la cuenta corriente para el ingreso del salario. Hay que tener en cuenta que casi todos los docentes llevan a sus hijos a estudiar a este mismo colegio en el que trabajan.

---

En primer lugar, parece claro que tenemos la entidad fuerte **Estudiante**, con número de expediente (candidato idóneo a ser clave primaria), nombre, apellido_1, apellido_2, sexo y fecha de nacimiento.

Además, nos dicen que, si un estudiante tiene alguna necesidad especial, guardemos su descripción y la atención especial que necesita. Es decir, tenemos una subclase de Estudiante, **Estudiante_Especial**, con atributos propios que no tienen todos los estudiantes. Como es la única subclase, ya sabes qué restricción tiene su jerarquía.

También vamos a tener a los **tutores** (la madre, el padre o cualquier otra persona que tenga al estudiante a su cargo) y a los **docentes**. Si analizamos estas dos posibles entidades, vemos que tienen algunas similitudes y algunas diferencias.

Las similitudes son:

- Son personas adultas de las que hay que guardar un NIF (candidato idóneo a ser clave primaria; el enunciado lo llama DNI en los docentes, pero es el mismo dato), nombre, apellido_1, apellido_2, teléfono y dirección.

Las diferencias son:

- De las personas **tutoras** hay que guardar su profesión, y además tienen una relación, **a_cargo_de**, con los estudiantes. Fíjate en que el tipo de relación (madre, padre...) no es un dato del tutor ni del estudiante, sino de la pareja.
- De las personas **docentes** hay que guardar su salario y su cuenta. Además, tendrán algunas relaciones específicas que modelaremos cuando lleguemos a los párrafos correspondientes.

Con todo esto, ya podemos suponer que vamos a crear una jerarquía con la superclase **Persona** y las subclases **Tutora** y **Docente**. Para decidir sus restricciones, piensa si puede haber personas que no sean ni tutoras ni docentes, y fíjate en la última frase del tercer párrafo.

Escribe el texto BigER necesario para modelar todas estas entidades y la relación a_cargo_de, y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 03 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (2)

Vamos a continuar centrándonos en los 2 siguientes párrafos:

---
La educación en el colegio se organiza en torno a los distintos cursos de primaria, identificados por un número de curso; además se guarda una descripción general del curso. Algunos docentes son responsables de un curso (como máximo de uno) y esta información debe conocerse; téngase en cuenta que un curso sólo tiene como responsable a un docente.

Para cada curso se desea tener almacenados una serie de objetivos de aprendizaje que deben alcanzarse en ese curso (por ej. escribir las vocales, conocer los números del 1 al 10 o aprender a sumar con una cifra); estos objetivos son particulares de cada curso y aunque pueden parecerse, no pueden repetirse para distintos cursos. Cada objetivo tiene un código único interno consecutivo de objetivo dentro del curso concreto, (es decir, el primer objetivo para el curso 1º tendrá el valor 1 para este campo, el segundo el 2, etc… mientras que el primer objetivo del curso 2º también tendrá el valor 1, el segundo el 2, etc..). Para cada objetivo, habrá de almacenarse una descripción.

---

En primer lugar, parece claro que tenemos la entidad fuerte **Curso**, con número de curso (candidato idóneo a ser clave primaria) y descripción.

Además, hay que establecer la relación **responsable_de** entre Docente y Curso. Las cardinalidades de los dos lados se deducen directamente del enunciado.

Vamos ahora a modelar los objetivos de aprendizaje. Tal y como nos dice el enunciado, un objetivo no tiene ningún atributo que lo distinga de los objetivos de los demás cursos: tiene una descripción y un código que solo es único **dentro de cada curso**. Además, ¿qué pasaría con la información de los objetivos si desapareciera el curso al que pertenecen?

Todo esto nos tiene que llevar a modelar **Objetivo_Aprendizaje** como **entidad débil** que depende de Curso, con el código del objetivo como clave parcial, y a unirla con Curso mediante la **relación débil** **tiene**.

Escribe el texto BigER necesario para modelar estas dos entidades y estas dos relaciones, y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 04 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (3)

Vamos a continuar centrándonos en los 2 siguientes párrafos:

---
Desde el punto de vista de la docencia, los docentes en el colegio están agrupados en departamentos (matemáticas, lengua y literatura, idiomas, etc.) y cada docente sólo puede pertenecer a uno de los departamentos. Cada departamento se encarga de organizar un grupo de asignaturas, de las que se conoce un código único y el nombre. Es necesario aclarar que lo que entiende el colegio como asignaturas son Matemáticas, Lengua, Educación Física, etc., lo que implica que la misma asignatura pueda impartirse en varios cursos: por ejemplo, la asignatura de nombre Matemáticas se imparte tanto en 1º de primaria como en 5º de primaria.

Con el fin de organizar la docencia, debe mantenerse información de qué asignaturas hay en cada curso, cuántas horas tiene asignadas en cada curso. Es norma en el colegio que una asignatura en un curso sea impartida por un único docente aunque un docente podría impartir varias asignaturas en el mismo o distinto curso.

---

En primer lugar, tenemos dos nuevas entidades fuertes:

- **Departamento**: aunque el texto no da atributos explícitos (solo ejemplos), necesitaremos una clave primaria, `codigo_departamento`, y un atributo `nombre_departamento`.
- **Asignatura**: de forma similar, tendremos una clave primaria, `codigo_asignatura`, y un atributo `nombre_asignatura`.

El primer párrafo también define dos nuevas relaciones:

- Docente **pertenece_a** Departamento.
- Departamento **organiza** Asignatura.

Las cardinalidades de los dos lados de estas relaciones se deducen directamente del enunciado.

El segundo párrafo tiene una estructura más compleja. Nos pide "mantener información de qué asignaturas hay en cada curso, cuántas horas tiene asignadas en cada curso":

- El atributo `horas` no pertenece a Curso (¿horas de qué?) ni a Asignatura (las horas dependen del curso). Pertenece a la **combinación** de ambos.
- La cardinalidad máxima es N en los dos lados: un curso tiene varias asignaturas y una asignatura está en varios cursos. Es una relación M:N con el atributo `horas`.

Pero el final del párrafo dice que "una asignatura en un curso sea impartida por un único docente" y que "un docente podría impartir varias". Es decir, el docente no imparte la asignatura en general, ni el curso en general, sino **la pareja** asignatura-curso. Si intentamos conectar Docente directamente con la relación entre Curso y Asignatura, nos topamos con el muro conceptual del modelo E/R: **es imposible conectar una relación con otra relación**.

La solución es transformar esa relación en una **entidad asociativa**, como en el ejemplo de los puestos de trabajo:

- La relación M:N se convierte en la entidad asociativa **Asignatura_Curso**. Cada instancia representa el hecho de que una asignatura concreta se imparte en un curso concreto.
    - No aporta ningún identificador propio: su identidad es la combinación de las claves de Curso y Asignatura, que no se escribe.
    - No necesita clave parcial porque la pareja **no se repite**: cada asignatura aparece una sola vez en cada curso.
    - Tiene como atributo `horas`.

Además, necesitamos crear las dos **relaciones asociativas** que la unen con las entidades de la relación original, con `[1..1]` en el lado de cada entidad:

- Curso **consta_de** Asignatura_Curso.
- Asignatura **se_imparte_en** Asignatura_Curso.

Ahora ya podemos modelar que "una asignatura en un curso sea impartida por un único docente" y que "un docente podría impartir varias". Para ello crearemos la relación **imparte** entre Docente y Asignatura_Curso.

Escribe el texto BigER necesario para modelar estas 3 nuevas entidades y las 5 relaciones indicadas, y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---

### Ejercicio 05 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (4)

Llegados a este punto, solo nos falta modelar este párrafo:

---
Para poder elaborar las actas a partir de la base de datos, el colegio quiere mantener información de los estudiantes matriculados en cada asignatura de un curso, la fecha de matriculación, así como de las calificaciones que ha ido obteniendo en las tres evaluaciones distintas de cada curso. Hay que tener en cuenta que, aunque de manera excepcional se puede repetir un curso en educación primaria, el colegio nos ha pedido que no contemplemos este caso.

---

Este párrafo final ya deberíamos poder modelarlo sin más pistas. Aun así, fíjate en dos detalles:

- Los estudiantes no se matriculan de una asignatura en general, sino de **cada asignatura de un curso**. ¿Qué entidad del modelo representa eso?
- El enunciado dice que, aunque se puede repetir curso, **no hay que contemplarlo**. ¿Qué cambiaría en el modelo si sí hubiera que contemplarlo?

Escribe el texto BigER necesario para modelar lo indicado en ese párrafo y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Fin

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el enunciado Enunciado 11 Colegio de Enseñanza de Primaria.

![](../imgs/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---



