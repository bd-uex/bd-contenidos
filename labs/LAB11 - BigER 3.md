# Uso de BigER

## Objetivos

1.-Familiarizarse con la **sintaxis** de Entidad Asociativa en BigER.

2.-Distinguir **entidades asociativas** de las entidades débiles, representandolas de forma correcta en la notación BigER

3.-Aplicar la notación para solucionar problemas de diseño E/R

---
## Contenidos

### Notación BigER

- 1.- Entidades Asociativas
### Resolución del Enunciado ER 11 Colegio de Enseñanza de Primaria

---
## Introducción a la notación textual de BigER (6) Entidades Asociativas

El modelo Entidad-Relación tiene una regla fundamental e inquebrantable: **es imposible conectar usando una relación a una otra relación directamente con una entidad**. Un verbo no puede ser el sujeto de otra acción. Pero, en el modelado de datos, a veces nos encontramos que una interacción de **muchos-a-muchos (M:N) sin repetición** es tan importante que necesitamos conectarla con otras entidades.

#### El Problema Guiado: Asignación de Competencias a Puestos de Trabajo

**El Escenario Inicial:** En el departamento de Recursos Humanos, se definen los `PUESTOS_DE_TRABAJO` y un catálogo de `COMPETENCIAS` profesionales. La relación entre ellos es de muchos-a-muchos (M:N):

- Un `PUESTO_DE_TRABAJO` (ej: "Analista de Datos") requiere **varias** `COMPETENCIAS` (ej: "SQL Avanzado", "Visualización de Datos").
    
- Una `COMPETENCIA` (ej: "Liderazgo de Equipos") es requerida por **varios** `PUESTOS_DE_TRABAJO`.
    

La regla de negocio es que una competencia se asigna una sola vez a cada puesto. Este es el modelo inicial:

**El Nuevo Requisito (El Muro Conceptual):** Ahora, RRHH necesita que cada una de estas asignaciones sea certificada. Es decir, se debe registrar qué `VALIDADOR` (un directivo o experto) ha aprobado que la competencia "SQL Avanzado" es necesaria para el puesto "Analista de Datos".

El `VALIDADOR` no certifica el puesto en general, ni la competencia en general, sino la **regla de asignación** entre ambos. Si intentamos conectar la entidad `VALIDADOR` directamente a la relación "REQUIERE", nos topamos con el muro conceptual del modelo E/R: **es imposible conectar una relación con otra relación **.

---
#### La Solución: "Cosificar" la Relación en una Entidad Asociativa

La solución es transformar la relación "REQUIERE" en una entidad asociativa que actúe como un conector.

Una entidad asociativa es, por tanto, un híbrido: nace de una relación M:N para darle cuerpo y permitir que se conecte con otras partes del modelo. Se comporta como una **tabla `join` en el mundo real**, existiendo únicamente para conectar dos conceptos y cuya identidad es la combinación de las dos cosas que conecta.

**Paso 1: Convertir la Relación en una Entidad (el Puente)** Transformamos la relación en una entidad. Es decir, la relación M:N se convierte en la entidad asociativa `REQUISITO_DE_PUESTO`. Cada instancia de esta nueva entidad representa el hecho único y no repetible de que un puesto específico requiere una competencia específica.

**Paso 2: Analizar la Identidad del Puente (La Clave Compuesta)** Esta nueva entidad `REQUISITO_DE_PUESTO` **no aporta ningún identificador propio**. Es, por naturaleza, una entidad débil. Su única identidad es la combinación de las claves de las entidades que une. No necesita una clave parcial propia porque la relación no se repite; un puesto solo requiere una competencia una vez.

- **Clave Primaria 🔑 = {id_puesto (FK), id_competencia (FK)}**
    

**Paso 3: Usar el Puente para Conectar** Ahora que `REQUISITO_DE_PUESTO` es una entidad, ya podemos conectarla con `VALIDADOR` a través de una nueva relación binaria, `ES_VALIDADO_POR`.

**El Modelo Final y su Significado:** El diagrama final representa la realidad de forma lógica y correcta:

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: Se define un `REQUISITO_DE_PUESTO` (un puesto de trabajo necesita una competencia).
    
2. **Hecho 2**: Ese `REQUISITO` específico es certificado por un `VALIDADOR`.

La notación utilizada en BigER es la siguiente:

_// Entidad asociativa que "cosifica" la relación M:N  
// Su clave primaria es la combinación de id_1 e id_2  
//**No es soportada por BigER, lo que haremos es comenzar siempre el nombre de este tipo de entidades con Asoc_ y pondremos los dos atributos claves de las entidades que asocia como claves de la entidad asociativa*_  
`entity` ==Asoc_Entidad1_Entidad2== {  
==id_1_ key==  
==id_2 key==  
resto de atributos
}

Donde:

- palabra reservada `entity`
- ==Asoc_Entidad1_Entidad2== es el nombre que le damos a la entidad asociativa:
	- siempre comenzaremos con Asoc_ o ASOC_ 
	- nombre de la Entidad1
	- _
	- nombre de la Entidad2
- ==id_1_ key==  clave de la Entidad1
- ==id_2 key== clave de la Entidad2
- resto de atributos específicos de la entidad asociativa

Ahora ya podemos modelar correctamente el ejemplo:

entity PUESTO_DE_TRABAJO {  
id_puesto key 
nombre_puesto  
}

entity COMPETENCIA {  
id_competencia key  
nombre_competencia  
}

// Entidad asociativa que "cosifica" la relación M:N
// Su clave primaria es la combinación de id_puesto e id_competencia
//**No es soportada por BigER, lo que haremos es comenzar siempre el nombre de este tipo de entidades con ASOC_ y pondremos los dos atributos claves de las entidades que asocia como claves de la entidad asociativa**
entity ASOC_REQUISITO_DE_PUESTO {  
id_puesto key  
id_competencia key  
}

// Relaciones que forman la entidad asociativa
relationship requiere {  
PUESTO_DE_TRABAJO[1..1] -> ASOC_REQUISITO_DE_PUESTO[1..N]  
}  

relationship es_requerida_por {  
COMPETENCIA[1..1] -> ASOC_REQUISITO_DE_PUESTO[1..N]  
}  

// La entidad asociativa ahora puede relacionarse con otras entidades

entity VALIDADOR {  
id_validador key  
rol_empresa  
}  

```relationship``` validada_por {  
ASOC_REQUISITO_DE_PUESTO[0..N] -> VALIDADOR[1..1]  
}  

**En el diagrama generado por BigER lo veremos gráficamente como una entidad normal**
![](../resources/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Crows%20Foot.png)

**Cuando lo dibujemos a mano, pondremos un rombo dentro del rectángulo de la entidad asociativa**
![](../resources/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Rombo%20Crows%20Foot.png)

**El Modelo Final y su Significado:** El diagrama final representa la realidad de forma lógica y correcta:

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: Se define un `REQUISITO_DE_PUESTO` (un puesto de trabajo necesita una competencia).
    
2. **Hecho 2**: Ese `REQUISITO` específico es certificado por un `VALIDADOR`.

#### Nota Importante: La Ausencia de Repetición en el Tiempo

Es crucial entender que este patrón funciona porque asumimos que la relación M:N **no se repite**. Un puesto requiere una competencia una sola vez. Si la relación pudiera repetirse (por ejemplo, si se revisaran las competencias de los puestos cada año y quisiéramos guardar el histórico), la entidad `REQUISITO_DE_PUESTO` necesitaría un atributo adicional en su clave (`Año`) para distinguir las repeticiones. En ese momento, estaríamos en el caso de **modelar una relación M:N con repetición de relaciones entre instancias**, donde en este caso aplicaríamos lo que hemos visto sobre entidades y relaciones débiles que hemos visto anteriormente.

---
### Ejercicio 01 - Entidad Asociativa (1)

Crea el archivo EntidadAsociativa.erd y modela el ejemplo que hemos visto antes y que se muestra en la siguiente imagen.

![](../resources/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Rombo%20Crows%20Foot.png)

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
## Resolución del Enunciado ER 11 Colegio de Enseñanza de Primaria

Los ejercicios a continuación están orientados a resolver el Enunciado ER 11 Colegio de Enseñanza de Primaria disponible [aquí](https://github.com/bd-uex/bd-contenidos/blob/main/Enunciados%20ER/Enunciado%20ER%2011%20Colegio%20de%20Ense%C3%B1anza%20de%20Primaria.md)

Crea el archivo EnunciadoER11ColegioEnseñanzaPrimaria.erd que será donde vamos a ir modelando el Enunciado 11 Colegio de Enseñanza de Primaria.

---
### Ejercicio 02 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (1)

Vamos, a comenzar centrándonos en los 3 primeros párrafos:

---
Un pequeño colegio de un pueblo, que solo imparte enseñanza de primaria desea organizar toda la información de que dispone para lograr una eficiente gestión de estudiantes, cursos, docentes y asignaturas. Los supuestos que quieren recoger inicialmente se especifican a continuación. 

Cuando un estudiante ingresa en el colegio se deben facilitar sus datos personales (nombre, apellidos, sexo y fecha de nacimiento); el colegio le asigna un número de expediente único que se mantendrá a lo largo de toda su etapa escolar. Así mismo, dado que los estudiantes son habitualmente menores de edad, se debe recoger información de, al menos, uno de los tutores (puede ser la madre, el padre o cualquier otra persona a la que está a cargo) del estudiante. Para los tutores debe conocerse su nif, nombre y apellidos, profesión, dirección completa y un teléfono de contacto. También debe conocerse el tipo de relación que le une al estudiante, es decir, si la persona es la madre o el padre o cualquier otra relación.  Además de la información anterior, si un estudiante sufre algún tipo de enfermedad, alergia o tiene algún grado de discapacidad debe reflejarse también, especificando una descripción de esta y si se requiere algún tipo de atención especial en el colegio. 

Con respecto a los docentes, se debe mantener información personal del mismo (dni, nombre y apellidos, fecha de nacimiento, dirección y teléfono), el salario y la cuenta corriente para el ingreso del salario. Hay que tener en cuenta que casi todos los docentes llevan a sus hijos a estudiar a este mismo colegio en el que trabajan. 

---

En primer lugar, parece claro que tenemos la entidad fuerte Estudiante:
 - Estudiante, con número de expediente, (candidato idóneo a ser Clave Primaria), nombre, apellido1, apellido2, sexo y fecha de nacimiento.

Además, nos dicen que si un estudiante tiene alguna necesidad especial que guardemos la descripción del motivo y la atención especial que necesita. Es decir tenemos un subtipo de Estudiante con atributos propios que no tienen todos los Estudiantes.

También parece que vamos a tener las entidades tutores(madre, padre o cualquier otra persona de la que esté a cargo) y docentes. Si analizamos estas dos posibles entidades, vemos que tienen algunas similitudes y algunas diferencias.
Las similitudes son:
	- Son personas adultas de las que hay que guardar nif (candidato idóneo a ser Clave Primaria), nombre, apellido1, apellido2, teléfono y dirección.

Las diferencias son :
	- De las Personas Tutoras hay que guardar su profesión y además van a tener una relación (a_cargo_de) con los Estudiantes.
	- De los Personas Docentes hay que guardar su salario y su cuenta. Además, van a tener algunas relaciones específicas que modelaremos cuando lleguemos a los párrafos correspondientes.
	
Con todo esto ya podemos suponer que vamos a crear una jerarquía para Persona, Tutora y Docente. Con los datos que nos han dado deberíamos ser capaces de indicar correctamente sus características.

Escribe el texto BigER necesario para modelar todas estas entidades y la relación a_cargo_de y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 03 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (2)

Vamos, a continuar centrándonos en los 2 siguientes párrafos:

---
La educación en el colegio se organiza en torno a los distintos cursos de primaria, identificados por un número de curso; además se guarda una descripción general del curso. Algunos docentes son responsables de un curso (como máximo de uno) y esta información debe conocerse; téngase en cuenta que un curso sólo tiene como responsable a un docente. 

Para cada curso se desea tener almacenados una serie de objetivos de aprendizaje que deben alcanzarse en ese curso (por ej. escribir las vocales, conocer los números del 1 al 10 o aprender a sumar con una cifra); estos objetivos son particulares de cada curso y aunque pueden parecerse, no pueden repetirse para distintos cursos. Cada objetivo tiene un código único interno consecutivo de objetivo dentro del curso concreto, (es decir, el primer objetivo para el curso 1º tendrá el valor 1 para este campo, el segundo el 2, etc… mientras que el primer objetivo del curso 2º también tendrá el valor 1, el segundo el 2, etc..). Para cada objetivo, habrá de almacenarse una descripción.  

---

En primer lugar, parece claro que tenemos la entidad fuerte Curso:
 - Curso, con número de curso, (candidato idóneo a ser Clave Primaria) y descripción.

Además, hay que establecer la relación **responsable_de** entre esta nueva entidad y la entidad Docentes. El inferir correctamente las cardinalidades de ambos lados de la relación a partir del enunciado ya no debería suponernos un problema.

Vamos ahora a tratar de modelar la entidad Objetivo. Tal y como nos dice el enunciado, un Objetivo no tiene un atributo que permita distinguir entre los distintos objetivos de los distintos cursos ya que tiene una descripción y un código interno único es solo único para cada curso. Además, ¿qué pasaría con la información de los Objetivos si desapareciera el Curso que **tiene** dichos objetivos?

Todo esto nos tiene que llevar a modelar Objetivo como **Entidad débil** que va a depender de Curso. 

Escribe el texto BigER necesario para modelar ambas entidades y ambas relaciones y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 04 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (3)

Vamos, a continuar centrándonos en los 2 siguientes párrafos:

---
Desde el punto de vista de la docencia, los docentes en el colegio están agrupados en departamentos (matemáticas, lengua y literatura, idiomas, etc.) y cada docente sólo puede pertenecer a uno de los departamentos. Cada departamento se encarga de organizar un grupo de asignaturas, de las que se conoce un código único y el nombre. Es necesario aclarar que lo que entiende el colegio como asignaturas son Matemáticas, Lengua, Educación Física, etc., lo que implica que la misma asignatura pueda impartirse en varios cursos: por ejemplo, la asignatura de nombre Matemáticas se imparte tanto en 1º de primaria como en 5º de primaria.  

Con el fin de organizar la docencia, debe mantenerse información de qué asignaturas hay en cada curso, cuántas horas tiene asignadas en cada curso. Es norma en el colegio que una asignatura en un curso sea impartida por un único docente aunque un docente podría impartir varias asignaturas en el mismo o distinto curso.  

---
En primer lugar, parece que tenemos dos nuevas entidades fuertes:

- Departamento: aunque el texto no da atributos explícitos (solo ejemplos), necesitaremos una Clave Primaria como codigo_departamento y un atributo nombre_departamento.
    
- Asignatura: de forma similar a Departamento, tendremos una Clave Primaria como codigo_asignatura y un atributo nombre_asignatura.
    
El primer párrafo también define dos nuevas relaciones:
- Una relación para modelar que  Docente **pertenece_a** Departamento.
- Una relación para modelar que cada Departamento **organiza** Asignatura.
    
El inferir correctamente las cardinalidades de ambos lados de estas relaciones a partir del enunciado ya no debería suponernos un problema.

El inicio del segundo párrafo ya sí tiene una estructura más compleja. Así, nos pide "mantener información de qué asignaturas hay en cada curso, cuántas horas tiene asignadas en cada curso".

El atributo horas no pertenece a Curso (¿horas de qué?) ni a Asignatura (las horas dependen del curso). Pertenece a la _combinación_ de ambos. Además, podemos ver que la cardinalidad máxima es N en ambos lados, (N:M o muchos a muchos). Por tanto, esta relación tendría su propio atributo: horas.

Pero vayamos ahora al final del segundo párrafo "una asignatura en un curso sea impartida por un único docente" y "un docente podría impartir varias...". Esto significa que el Docente no imparte la asignatura en general, ni el curso en general, sino la asociación entre ambos. Si intentamos conectar la entidad Docente directamente a la relación que creemos entre Curso y Asignatura, nos topamos con el muro conceptual del modelo E/R: **es imposible conectar una relación con otra relación **.

La solución es transformar la relación entre Curso y Asignatura en una entidad asociativa que actúe como un conector:
	- la relación con cardinalidad máxima M:N se convierte en la entidad asociativa Asoc_Asignaturas_Curso. Cada instancia de esta nueva entidad representa el hecho único y no repetible de que una asignatura específica está en un curso específico.
		- Esta nueva entidad **no aporta ningún identificador propio**. Su única identidad es la combinación de las claves de las entidades que une. No necesita una clave parcial propia porque la relación no se repite; una asignatura solo la hay en un curso a la vez.
			- **Clave Primaria 🔑 = {numero_curso, cod_asignatura}**
		- Tendrá además como atributo a horas

Además, necesitamos crear las dos relaciones con las entidades que estaba uniendo mientras estábamos considerándola como relación:
	- Curso **consta_de** Asoc_Asignaturas_Curso
	- Asignatura **se_imparten_en** Asoc_Asignaturas_Curso

Deberíamos poder inferir correctamente las cardinalidades de ambos lados de estas relaciones casi de forma mecánica.

Ahora ya sí podemos modelar lo indicado en "una asignatura en un curso sea impartida por un único docente" y "un docente podría impartir varias...". Para ello crearemos la relación **imparte** con Docente y también deberíamos poder inferir fácilmente las cardinalidades de ambas relaciones.

Escribe el texto BigER necesario para modelar estas 3 nuevas entidades y las 5 relaciones indicadas y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 05 - Enunciado ER 11 Colegio de Enseñanza de Primaria  (4)

Llegados a este punto, ya solo nos falta modelar lo indicado en este párrafo::

---
Para poder elaborar las actas a partir de la base de datos, el colegio quiere mantener información de los estudiantes matriculados en cada asignatura de un curso, la fecha de matriculación, así como de las calificaciones que ha ido obteniendo en las tres evaluaciones distintas de cada curso. Hay que tener en cuenta que, aunque de manera excepcional se puede repetir un curso en educación primaria, el colegio nos ha pedido que no contemplemos este caso.

---

Este párrafo final ya deberíamos poder modelarlo sin más pistas. Aún así, solo resaltar donde dice: "aunque de manera excepcional se puede repetir un curso en educación primaria, el colegio nos ha pedido que no contemplemos este caso."

Escribe el texto BigER necesario para modelar lo indicado en ese párrafo. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el enunciado Enunciado 11 Colegio de Enseñanza de Primaria.

![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---



