# Uso de BigER

## Objetivos

1.-Instalar y aprender a usar la extensión BigER de VsCode para modelado Entidad Relación

2.-Familiarizarse con la **sintaxis** de la notación BigER, comprendiendo cómo se describen las entidades, atributos, relaciones y cardinalidades.

3.-Modelar **entidades débiles** que dependen de otras entidades para existir, comprendiendo cómo utilizar las claves parciales.

4.-Gestionar **herencia** o **especialización** de entidades, representando subtipos y supertipos de forma correcta en la notación BigER

5.-Aplicar la notación para solucionar problemas de diseño E/R

---
## Contenidos

### Instalación de BigER

### Notación BigER

- 1.- Creación de Entidades y Atributos
- 2.- Relaciones entre Entidades
- 3.- Entidades Dependientes y Relaciones Débiles
- 4.- Roles
- 5.- Herencia y Especialización

---
## Instalación y uso de BigER en VSCode

Si no lo has hecho ya, instala BigER en VSCode siguiendo la guía que tenemos preparada en el repositorio de la asignatura: [Instalación y uso de BigER en VSCode](Instalaci%C3%B3n%20y%20uso%20de%20BigER%20en%20VSCode.md)

---

##  Introducción a la notación textual de BigER (1) Entidades y atributos

La notación textual **BigER** es una representación textual que permite describir **diagramas Entidad-Relación** (E/R) de forma estructurada y comprensible.

Esta notación es especialmente útil en entornos donde es más fácil trabajar con texto en lugar de crear diagramas visuales manualmente, como es el caso de la extensión **BigER** para Visual Studio Code. A través de esta sesión, aprenderemos gradualmente a utilizar esta notación con ejemplos que van desde lo más básico hasta lo más avanzado.

Antes de comenzar, vamos a comenzar creando 4 archivos **.erd** en **Vs Code**:

1. EjemplosNotacionBigER.erd
2. ModeloTheExpanseV1Simplificado.erd
3. EjemplosEERBigER.erd
4. ModeloEERUniversidad.erd

Para crear estos archivos, en VsCode, elige Archivo->Nuevo Archivo y elige **New Empty ER Model**:

![](../resources/BD%20-%20BigER%20Menu%20Nuevo%20Archivo.png)

Una vez creados los 4 archivos, ve al archivo EjemplosNotacionBigER.erd que será sobre el que escribamos los ejemplo iniciales con la notación.

Ahora ya sí, vamos a comenzar a ver la notación, empezando por cómo describir Entidades y Atributos.

Una **entidad** representa un objeto del mundo real o conceptual que tiene una existencia distinguible. Cada entidad tiene un conjunto de **atributos** que describen sus características.

Ve al archivo EjemplosNotacionBigER y escribe lo siguiente:

entity NombreEntidad {  
    atributo1 key  
    atributo2  
    atributo3  
    atributo4  
    atributo5  
}

Deberías ver algo similar a lo que se muestra en la siguiente pantalla:

![](../resources/BD%20-%20BigER%20Entidad%20y%20Atributos.png)

Tenemos varios elementos en los que fijarnos:

- palabra reservada `entity` (ojo, todas las palabras reservadas se escriben en minúsculas): especifica que vamos a definir una nueva entidad.
- **NombreEntidad**: es el nombre de la entidad.
- { : llave de inicio para indicar que pasamos a describir el contenido de la entidad.
- Lista de atributos, uno en cada línea:
    - Aquel atributo (o conjunto de atributos) que sean clave primaria habrá que indicarlo usando la palabra reservada `key` y aparecerá subrayado en el diagrama
- } : llave de fin de descripción del contenido de la entidad.

---
### Ejercicio 01 - Entidades y atributos (1)

Modifica el contenido de la entidad NombreEntidad anterior para que pase a ser la entidad **Estudiante**, con los atributos **numExpediente** (**Clave Primaria**), **nombre**, **apellido1** y **apellido2** para que se visualice dicha entidad de esta forma:

![](../resources/BD%20-%20BigER%20Entidad%20Estudiante.png)

¿Cuál de los siguientes textos modela correctamente la entidad **Estudiante**?

- Opción 1
`entity` Estudiante {  
numExpediente `Key`
nombre  
apellido1  
apellido2  
}

- Opción 2
`Entity` Estudiante {  
numExpediente `key`
nombre
apellido1  
apellido2  
}

- Opción 3
`entity` Estudiante {  
numExpediente `key`
nombre
apellido1  
apellido2  
}

- Opción 4
Todas las respuestas son correctas

---
### Ejercicio 02 - Entidades y atributos (2)

Abre el archivo ModeloTheExpanseV1Simplificado.erd que será donde vamos a ir modelando el enunciado Enunciado 2 ER The Expanse v1 simplificado que se ha resuelto en Grupo Grande.

Escribe el texto BigER necesario para modelar las entidades fuertes Planeta, Centro, Mineral y NaveEspacial que se muestran en la siguiente imagen:

![](../resources/BD%20-%20BigER%20Mineral%20Centro%20Planeta%20Centro%20Espacial.png)

¿En qué posición hay que situar la palabra reservada key para que nos aparezcan las claves primarias correctamente subrayadas en el diagrama E/R como en la imagen?:

- Opción 1
En la línea anterior a los atributos codMineral (en Mineral), codCentro (en Centro), codPlaneta (en Planeta) y matricula (en Nave Espacial)

- Opción 2
En la misma línea, justo delante de los atributos codMineral (en Mineral), codCentro (en Centro), codPlaneta (en Planeta) y matricula (en Nave Espacial)

- Opción 3
En la misma línea, justo a continuación de los atributos codMineral (en Mineral), codCentro (en Centro), codPlaneta (en Planeta) y matricula (en Nave Espacial)

- Opción 4
Todas las respuestas son correctas

---
##  Introducción a la notación textual de BigER (2) Relaciones

Vamos a continuar modelando **relaciones** entre entidades.

Una **relación** describe una interacción entre dos o más entidades. Además, es **imprescindible especificar la cardinalidad de la relación**, es decir, cuántas instancias, como mínimo y como máximo, de una entidad están asociadas con cuántas instancias, como mínimo y como máximo, de otra entidad.

La notación utilizada en BigER es la siguiente:

**relationship** NombreRelacion {

Entidad1[`cardMin..cardMax`] `->` Entidad2[`cardMin..cardMax`]
atributo1DeLaRelacion
atributo2DeLaRelacion
...
atributoZDeLaRelacion
}

- palabra reservada **relationship**
- NombreRelacion: es el nombre que le damos a la relación.
- Entidad1 y Entidad2: son las entidades involucradas en la relación.
- **cardMin**..**cardMax**: indica el número de instancias que pueden participar en la relación. Puede ser:
    - 0..1: mínimo cero, máximo uno.
    - 1..1: una y solo una instancia.
    - 0..N: desde cero a muchas instancias.
    - 1..N: mínimo una instancia, máximo muchas instancias.
- Lista de atributos específicos de la relación, cada uno en una línea.

Vuelve de nuevo al archivo EjemplosNotacionBigER y añade lo siguiente:

`entity` Titulacion {
codTitulacion `key`
nombre
}

`relationship` matriculadoEn {
Estudiante[`1..N`] `->` Titulacion[`1..1`]
fechaMatriculacion
}

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente pantalla:

![](../resources/BD%20-%20BigER%20Relacion%20Estudiante%20Titulacion%20Chen.png)

Como puedes observar, BigER por defecto usa una notación gráfica distinta a la notación Crow's foot que queremos en nuestros diagramas.

Hay dos maneras de conseguir que BigER nos muestre el diagrama E/R en notación Crow's foot:

- Incluyendo `notation`=crowsfoot justo en al línea a continuación del inicio del fichero donde aparece `erdiagram` NombreModelo
- Seleccionando dicha notación al hacer clic en el botón de cambio de notación ![](../resources/BD%20-%20BigER%20Botón%20Notación.png) y eligiendo Crow's foot

Tanto si lo haces de una forma como de otra ya deberías poder ver así el diagrama:

![](../resources/BD%20-%20BigER%20Estudiante%20Titulacion%20Matriculado%20En.png)

Como acabamos de ver, aunque BigER admite atributos al describir una relación, no los muestra.

Si pasas el ratón por encima del rombo que modela la relación, verás que aunque no lo muestre, sí lo está incluyendo como atributo en la relación tal y como se ve en la siguiente imagen:

![](../resources/BD%20-%20BigER%20Atributo%20en%20Matriculado%20En.png)

Para poder mostrar este, y cualquier otro atributo de relación, en el diagrama final tendremos que recurrir a otras herramientas que nos permitan incluir líneas y texto sobre la imagen del diagrama cuando tengamos nuestro diagrama terminado.

Cualquier editor de texto, de presentaciones o de imagen suele soportar ese tipo de inclusión y se puede recurrir a la que cada estudiante conozca con más profundidad.

En nuestro caso, en los distintos diagramas E/R que ves en la asignatura, estamos recurriendo a PowerPoint (también es igual de sencillo en cualquier otro editor de presentaciones) dado lo fácil que es incluir sobre una imagen tanto distintas formas como texto.

Así, puedes ver a continuación el diagrama tan incluir su imagen (rápidamente mediante captura de pantalla) en una diapositiva de Powerpoint, incluir el nombre del atributo como cuadro de texto y unir el rombo y el texto insertando una línea recta:

![](../resources/BD%20-%20BigER%20Atributo%20en%20Matriculado%20En%20Retocado.png)

Antes de pasar a realizar ejercicios con relaciones, indicar que es posible modelar relaciones ternarias entre tres entidades. La única diferencia es que en este caso la sintaxis sería:

`relationship` NombreRelacion {
Entidad1[`cardMin..cardMax`] `->` 
Entidad2[`cardMin..cardMax`] `->` 
Entidad3[`cardMin..cardMax`]
atributo1DeLaRelacion
atributo2DeLaRelacion
...
atributoZDeLaRelacion
}

---
### Ejercicio 03 - Relaciones (1)

Abre de nuevo el archivo ModeloTheExpanseV1Simplificado.erd.

Vamos a modelar alguna de las relaciones del diagrama con las entidades que tenemos hasta ahora.

En primer lugar vamos a modelar la relación ubicadoEn entre Centros y Planetas. Como hemos visto en clase, una instancia de Centro estará siempre relacionada con una y solo una instancia de Planeta mientras que una instancia de Planeta estará relacionada, como mínimo, con una instancia de Centro y, como máximo, con muchas instancias de Centro.

¿Cuál es la forma correcta de describir esta relación en BigER?

- Opción 1
`relationship` ubicadoEn {
Centro[`1..1`] `->` Planeta[`1..N`]
fechaMatriculacion
}

- Opción 2
`relationship` ubicadoEn {
Centro[`O..N`] `->` Planeta[`1..1`]
fechaMatriculacion
}

- Opción 3
`relationship` ubicadoEn {
Centro[`1..N`] `->` Planeta[`1..1`]
fechaMatriculacion
}

- Opción 4
`relationship` ubicadoEn {
Centro[`1..1`] `->` Planeta[`0..N`]
fechaMatriculacion
}

---
### Ejercicio 04 - Relaciones (2)

Vamos a seguir en el archivo ModeloTheExpanseV1Simplificado.erd.

Vamos a modelar el resto de relaciones del diagrama con las entidades que tenemos hasta ahora.

Así, vamos a modelar:

1. La relación esPrincipalEn entre Centro y Planeta. Como hemos visto en clase, una instancia de Centro estará, como mínimo, con cero instancias de Planeta y, como máximo, con una instancia de Planeta mientras que una instancia de Planeta siempre estará relacionada con una y solo una instancia de Centro.
2. La relación encontradoPor entre Centro y Mineral. Como hemos visto en clase, una instancia de Centro estará, como mínimo, con cero instancias de Mineral y, como máximo, con muchas instancias de Mineral mientras que, una instancia de Mineral estará, como mínimo, con cero instancias de Centro y, como máximo, con muchas instancias de Centro. Además, esta relación tiene un atributo llamado fechaHallazgo
3. La relación asignadaA entre NaveEspacial y Planeta. Como hemos visto en clase, una instancia de NaveEspacial estará, como mínimo, con una instancia de Planeta y, como máximo, con una instancia de Planeta mientras que una instancia de Planeta estará relacionada, como mínimo, con una instancia de NaveEspacial y, como máximo, con muchas instancias de NaveEspacial.

¿Cuál es la forma correcta de describir estas relaciones en BigER?

- Opción 1
`relationship` esPrincipalEn {
Centro[`1..1`] `->` Planeta[`0..1`]
}
`relationship` encontradoPor {
Mineral[`0..N`] `->` Centro[`0..N`]
fechaHallazgo
}
`relationship` asignadaA {
NaveEspacial[`1..N`] `->` Planeta[`1..1`]
}

- Opción 2
`relationship` esPrincipalEn {
Centro[`1..1`] `->` Planeta[`0..1`]
}
`relationship` encontradoPor {
Mineral[`0..N`] `->` Centro[`0..N`]
}
`relationship` asignadaA {
NaveEspacial[`1..N`] `->` Planeta[`1..1`]
}

- Opción 3
`relationship` esPrincipalEn {
Centro[`1..1`] `->` Planeta[`0..1`]
}
`relationship` encontradoPor {
Mineral[`0..N`] `->` Centro[`0..N`]
fechaHallazgo
}
`relationship` asignadaA {
NaveEspacial[`1..1`] `->` Planeta[`1..N`]
}

- Opción 4
`relationship` esPrincipalEn {
Centro[`1..1`] `->` Planeta[`1..1`]
}
`relationship` encontradoPor {
Mineral[`0..N`] `->` Centro[`0..N`]
fechaHallazgo
}
`relationship` asignadaA {
NaveEspacial[`1..N`] `->` Planeta[`1..1`]
}

---
##  Introducción a la notación textual de BigER (3) Entidades dependientes o débiles

Vamos a continuar modelando **entidades dependientes o débiles** entre entidades.

Una **entidad** **dependiente o débil** es aquella que **no puede existir** sin estar relacionada con una entidad fuerte y que, **además**, tiene una **dependencia de identificación** por la que necesita formar su clave primaria utilizando la clave o claves primarias de la/s entidad/es fuerte/s de la/s que depende en combinación o no, dependiendo del caso, con algún atributo de la entidad débil.

La notación utilizada en BigER es la siguiente:

1.- En primer lugar, la notación para una entidad dependiente o débil es la siguiente:

`weak entity` NombreEntidadDebil {
clavePrimariaParcialEnEntidadDebil `partial-key`
atributo1DeLaEntidadDebil
...
atributoNDeLaEntidadDebil
}

Donde:

- palabra reservada `weak entity`
- clavePrimariaParcialEnEntidadDebil es el nombre del atributo de la entidad débil que, en la mayoría de los casos, se combinará con la clave o claves primarias de la/s entidad/es fuerte/s de la/s que depende seguido de la palabra reservada `partial-key`
- resto de atributos de la entidad débil

2.- En segundo lugar, toda entidad dependiente o débil se relaciona con, al menos, una entidad fuerte mediante una relación débil cuya notación es la siguiente:

`weak relationship` NombreRelacionDebil {
EntidadDebil[`0..N`] (o [`1..N`]) **->** EntidadFuerte[`1..1`]
}

o dependiendo **si por el nombre de la relación se entiende mejor cambiando el orden:**

`weak relationship` NombreRelacionDebil {
EntidadFuerte[`1..1`] -> EntidadDebil[`0..N`] (o [`1..N`])
}

Donde:

- palabra reservada `weak relationship`
- NombreRelacionDebil: es el nombre que le damos a la relación débil.
- EntidadDebil: aparece siempre con cardinalidad [`0..N`] o [`1..N`] dependiendo del caso.
- EntidadFuerte: aparece siempre con cardinalidad [`1..1`].
- Lista de atributos específicos de la relación, cada uno en una línea.

Hay que tener en cuenta que en caso de que la entidad dependiente o débil dependa de más de una entidad tendremos que tener una relación débil con cada una de esas entidades fuertes.

Vuelve de nuevo al archivo EjemplosNotacionBigER y añade lo siguiente:

`weak entity` Asignatura {
codAsignatura `partial-key` //atributo aportado a la clave primaria por la entidad débil Asignatura
nombreAsignatura
}

`weak relationship` compuestaDe {
Titulacion[`1..1`] -> Asignatura[`1..N`]
}

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente imagen:

![](../resources/BD%20-%20BigER%20Debil%20Titulacion%20Asignatura%20Crows%20Foot.png)

Puedes ver como tanto la entidad dependiente Asignatura como la relación débil compuestaDe aparecen con doble borde para indicar que son débiles.

---
### Ejercicio 05 - Entidades Dependientes (1)

Abre de nuevo el archivo ModeloTheExpanseV1Simplificado.erd.

Vamos a modelar la entidad dependiente Vuelo que depende de NaveEspacial.

1.- En primer lugar tenemos que añadir la **Entidad débil** Vuelo que está formada por los atributos:

- fechaIni: clave parcial aportada por Vuelo que tenemos que combinar como clave parcial para formar la clave primaria.
- horaIni: clave parcial aportada por Vuelo que tenemos que combinar como clave parcial para formar la clave primaria.
- fechaFin
- horaFin

2.- En segundo lugar tenemos que añadir la **relación débil** realizadoPor entre Vuelo y NaveEspacial. Como hemos visto en clase, una instancia de Vuelo estará siempre relacionada con una y solo una instancia de NaveEspacial mientras que una instancia de NaveEspacial estará relacionada, como mínimo, con cero instancias de Vuelo y, como máximo, con muchas instancias de Vuelo.

¿Cuál es la forma correcta de describir esta entidad y esta relación en BigER?

- Opción 1
`weak entity` Vuelo {
fechaIni `partial-key`
horaIni `partial-key`
fechaFin
horaFin
}

`relationship` realizadoPor {
Vuelo[`0..N`] -> NaveEspacial[`1..1`]
}

- Opción 2
`weak entity` Vuelo {
fechaIni `partial-key`
horaIni `partial-key`
fechaFin
horaFin
}

`weak relationship` realizadoPor {
Vuelo[`0..N`] -> NaveEspacial[`1..N`]
}

- Opción 3
`weak entity` Vuelo {
fechaIni `key`
horaIni `key`
fechaFin
horaFin
}

`weak relationship` realizadoPor {
Vuelo[`0..N`] -> NaveEspacial[`1..1`]
}

- Opción 4
`weak entity` Vuelo {
fechaIni `partial-key`
horaIni `partial-key`
fechaFin
horaFin
}

`weak relationship` realizadoPor {
Vuelo[`0..N`] -> NaveEspacial[`1..1`]
}

---
### Ejercicio 06 - Entidades Dependientes (2)

Abre de nuevo el archivo ModeloTheExpanseV1Simplificado.erd.

Vamos a modelar la entidad dependiente HistoricoExtraccion que guarda todas las extracciones diarias que hará cada Centro de cada Mineral. Una entidad débil con repetición o histórica siempre es el resultado de una relación muchos a muchos entre dos entidades cuya relación se puede repetir a lo largo del tiempo y que tendrá un atributo que permita distinguir cada repetición que formará parte de la clave primaria como clave parcial.

1.- En primer lugar tenemos que añadir la Entidad débil HistoricoExtraccion que está formada por los atributos:

- fecha: clave parcial aportada por HistoricoExtraccion que permite distinguir la extracción de un día de Mineral por un Centro de la extracción de ese mismo mineral por ese mismo Centro cualquier otro día.
- cantidad

2.- En segundo lugar tenemos que añadir la relación débil extraidoPor entre HistoricoExtraccion y Centro. Como hemos visto en clase, una instancia de HistoricoExtraccion estará siempre relacionada con una y solo una instancia de Centro mientras que una instancia de Centro estará relacionada, como mínimo, con cero instancias de HistoricoExtraccion y, como máximo, con muchas instancias de HistoricoExtraccion.

3.- En tercer lugar tenemos que añadir la relación débil incluidoEn entre HistoricoExtraccion y Mineral. Como hemos visto en clase, una instancia de HistoricoExtraccion estará siempre relacionada con una y solo una instancia de Mineral mientras que una instancia de Mineral estará relacionada, como mínimo, con cero instancias de HistoricoExtraccion y, como máximo, con muchas instancias de HistoricoExtraccion.

¿Cuál es la forma correcta de describir esta entidad y estas relaciones en BigER?

- Opción 1
`weak entity` HistoricoExtraccion {
fecha `partial-key`
cantidad
}
`weak relationship` incluidoEn {
HistoricoExtraccion[`0..N`] -> Mineral[`0..1`]
}
`weak relationship` extraidoPor {
HistoricoExtraccion[`0..N`] -> Centro[`1..1`]
}

- Opción 2
`weak entity` HistoricoExtraccion {
fecha 
cantidad
}
`weak relationship` incluidoEn {
HistoricoExtraccion[`0..N`] -> Mineral[`1..1`]
}
`weak relationship` extraidoPor {
HistoricoExtraccion[`0..N`] -> Centro[`1..1`]
}

- Opción 3
`weak entity` HistoricoExtraccion {
fecha `partial-key`
cantidad
}
`weak relationship` incluidoEn {
HistoricoExtraccion[`0..N`] -> Mineral[`1..1`]
}
`weak relationship` extraidoPor {
HistoricoExtraccion[`0..N`] -> Centro[`1..1`]
}


- Opción 4
`weak entity` HistoricoExtraccion {
fecha `partial-key`
cantidad
}
`weak relationship` incluidoEn {
HistoricoExtraccion[`0..1`] -> Mineral[`1..1`]
}
`weak relationship` extraidoPor {
HistoricoExtraccion[`0..N`] -> Centro[`1..1`]
}

---
### Ejercicio 07 - Entidades Dependientes (3)

Vamos a terminar con el archivo ModeloTheExpanseV1Simplificado.erd.

Vamos a modelar el resto de relaciones del diagrama que nos faltan para completar lo requerido en el enunciado The Expanse V1 Simplificado.

Así, vamos a modelar:

1. La relación formaParteDe entre Cargamento y Mineral. Como hemos visto en clase, una instancia de Mineral estará, como mínimo, con cero instancias de Cargamento y, como máximo, con muchas instancias de Cargamento mientras que una instancia de Cargamento siempre estará relacionada con una y solo una instancia de Mineral. Además, esta relación tiene un atributo llamado cantidad.
2. La relación transportadoEn entre Cargamento y Vuelo. Como hemos visto en clase, una instancia de Cargamento estará, como mínimo, con cero instancias de Vuelo y, como máximo, con una instancia de Vuelo mientras que una instancia de Vuelo estará relacionada, como mínimo, con una instancia de Cargamento y, como máximo, con muchas instancias de Cargamento.
3. La relación origenDe entre Vuelo y Centro. Como hemos visto en clase, una instancia de Centro estará, como mínimo, con cero instancias de Vuelo y, como máximo, con muchas instancias de Vuelo mientras que una instancia de Vuelo siempre estará relacionada con una y solo una instancia de Centro.
4. La relación destinoDe entre Vuelo y Centro. Como hemos visto en clase, una instancia de Centro estará, como mínimo, con cero instancias de Vuelo y, como máximo, con muchas instancias de Vuelo mientras que una instancia de Vuelo siempre estará relacionada con una y solo una instancia de Centro.

¿Cuál es la forma correcta de describir estas relaciones en BigER?

- Opción 1
`relationship` formaParteDe {
Cargamento[`0..N`] -> Mineral[`1..N`]
cantidad
}
`relationship` transportadoEn {
Cargamento[`1..N`] -> Vuelo[`0..1`]
}
`relationship` origenDe {
Vuelo[`0..N`] -> Centro[`1..1`]
}
`relationship` destinoDe {
Vuelo[`0..N`] -> Centro[`1..1`]
}

- Opción 2
`relationship` formaParteDe {
Cargamento[`0..N`] -> Mineral[`1..1`]
cantidad
}
`relationship` transportadoEn {
Cargamento[`1..N`] -> Vuelo[`0..1`]
}
`relationship` origenDe {
Vuelo[`0..N`] -> Centro[`1..1`]
}
`relationship` destinoDe {
Vuelo[`0..N`] -> Centro[`1..1`]
}

- Opción 3
`relationship` formaParteDe {
Cargamento[`0..N`] -> Mineral[`1..1`]
}
`relationship` transportadoEn {
Cargamento[`1..N`] -> Vuelo[`0..1`]
}
`relationship` origenDe {
Vuelo[`0..N`] -> Centro[`1..N`]
}
`relationship` destinoDe {
Vuelo[`0..N`] -> Centro[`1..1`]
}

- Opción 4
`relationship` formaParteDe {
Cargamento[`0..N`] -> Mineral[`1..1`]
cantidad
}
`relationship` transportadoEn {
Cargamento[`1..N`] -> Vuelo[`0..1`]
}
`relationship` origenDe {
Vuelo[`0..N`] -> Centro[`1..1`]
}
`relationship` destinoDe {
Vuelo[`0..N`] -> Centro[`1..N`]
}

---
## Introducción a la notación textual de BigER (4) Relaciones con roles

Vamos a continuar con el uso de **roles** en relaciones.

A veces, las entidades en una relación pueden desempeñar distintos **roles**. Estos roles permiten aclarar la función específica que desempeña cada entidad en la relación. Se suelen usar en relaciones recursivas de una entidad consigo misma.

La notación utilizada en BigER es la siguiente:

`relationship` NombreRelacionConRoles {

Entidad1[`cardMin..cardMax` | `"rol1"`] -> Entidad2[`cardMin..cardMax` | `"rol2"`]
AtributoDeLaRelacion1
AtributoDeLaRelacion2
...
AtributoDeLaRelacionZ
}

Donde:

- palabra reservada `relationship`
- NombreRelacionConRoles: es el nombre que le damos a la relación débil.
- Entidad1 y Entidad2: son las entidades involucradas en la relación. Normalmente serán la misma relación dado que solo se suelen usar roles en relaciones recursivas  
    
- Entidad: aparece siempre en primer lugar con cardinalidad [`0..N`] o [`1..N`] dependiendo del caso.
- `cardMin..cardMax`: indica el número de instancias que pueden participar en la relación. Puede ser:
    - 0..1: mínimo cero, máximo uno. Habitual en uno de los dos lados de la relación recursiva
    - 1..1: una y solo una instancia. No suele ser habitual en relaciones recursivas.
    - 0..N: desde cero a muchas instancias. Habitual en uno de los dos lados de la relación recursiva
    - 1..N: mínimo una instancia, máximo muchas instancias.No suele ser habitual en relaciones recursivas.
- rol1: rol tomado por la Entidad1 en la relación
- rol2: rol tomado por la Entidad1 en la relación
- Lista de atributos específicos de la relación, cada uno en una línea.

Vuelve de nuevo al archivo EjemplosNotacionBigER y añade lo siguiente:

**relationship** esMentorDe {
Estudiante[`0..1`| `"Mentor"`] -> Estudiante[`0..N`|`"Mentorizado"`]
}

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente pantalla:

![](../resources/BD%20-%20BigER%20Relacion%20Estudiante%20con%20Roles.png)
Puedes ver como ahora en cada extremo de la relación esMentorDe aparece el rol tomado en cada extremo de la relación por el Estudiante.

---

## Introducción a la notación textual de BigER (5) Jerarquías de herencia

Vamos a finalizar modelando **jerarquías de especialización/generalización** de entidades.

En el modelado Entidad/Relación podemos tener **jerarquías de especialización/generalización** de entidades básicamente cuando:

- Tenemos subtipos de una entidad con atributos específicos aparte de los atributos comunes heredados del supertipo.
    - Por ejemplo, el atributo tipoIngeniero en una entidad subtipo INGENIERO que es especialización de la entidad supertipo EMPLEADO
- Tenemos subtipos de una entidad que pueden participar en tipos de relaciones específicas en las que no participa la entidad supertipo.
    - Por ejemplo, la relación afiliadoA de una entidad subtipo TIEMPOPARCIAL que es especialización de la entidad supertipo EMPLEADO  
        
Esta es una característica que en el momento de redactar este contenido está todavía en fase beta. Esto supone que vamos a tener que hacer más trabajo extra para que se muestre correctamente en nuestros diagramas.

La notación utilizada en BigER es la siguiente:

1.- En primer lugar, la notación para una entidad dependiente o débil es la siguiente:

//`total` o `parcial`, `solapada`o `disjunta`
`entity` NombreEntidadSubtipo `extends` NombreEntidadSupertipo{
clavePrimariaEnEntidadSupertipo `key`
atributo1DeLaEntidadSubtipo
...
atributoNDeLaEntidadSubtipo
}

Donde:

- //primero se describe sus restricciones de completitud y disyunción:
    - `total` o `parcial`: indicar qué restricción de completitud se aplica
    - `solapada` o `disjunta`: indicar qué restricción de disyunción se aplica
- palabra reservada `entity`
- NombreEntidadSubtipo es el nombre que le damos a la entidad subtipo.
- palabra reservada `extends`para indicar que la entidad que se va a describir hereda de la entidad a continuación de `extends`
- NombreEntidadSupertipo: nombre de la entidad supertipo de la que hereda
- clavePrimariaEnEntidadSupertipo es el nombre de la clave primaria en la entidad supertipo de la que hereda y que será también clave primaria en el subtipo y por tanto irá seguido de la palabra reservada `key`
- resto de atributos específicos de la entidad subtipo que no tiene el supertipo

Abre el archivo EjemplosEERBigER.erd:

1.- En primer lugar vamos a crear la entidad Empleado que va a ser el supertipo del resto de entidades que vamos a crear en este ejemplo:

`entity` Empleado {  
dni `key`  
nombre  
fecha_nacimiento  
direccion  
telefono  
}

2.- En segundo lugar, vamos a crear una primera especialización de Empleado en tres subtipos de acuerdo al tipo de puesto de cada Empleado donde cada subtipo tiene un atributo propio de su subtipo que no tiene el supertipo Empleado:

// `parcial`, `disjunta`  
`entity` Administrativo `extends` Empleado{  
dni `key`  
categoria  
}

//`parcial`, `disjunta`  
`entity` Tecnico `extends` Empleado{  
dni `key`
nivel  
}

//`parcial`, `disjunta`  
`entity` Ingeniero `extends` Empleado{  
dni `key`  
tipoIng  
}

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente pantalla:

![](../resources/BD%20-%20BigER%20Jerarquia%20Tipo%20Empleado.png)


3.- En tercer lugar, vamos a crear una segunda especialización de Empleado en un subtipo de acuerdo a la relación dirige con una entidad denominada Proyecto que solo tienen un subtipo del supertipo Empleado al que denominaremos Gerente.

Hay que tener en cuenta que al tener solo un subtipo siempre será parcial y sin restricción de disyunción:

//`parcial`  
`entity` Gerente `extends` Empleado{  
dni `key`  
}

`entity` Proyecto{  
cod_proyecto `key`
}

`relationship` dirige{  
Gerente[`1..1`] -> Proyecto[`1..N`]
}

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente pantalla:

![](../resources/BD%20-%20BigER%20Jerarquia%20Gerente.png)

4.- En tercer lugar, vamos a crear una tercera especialización de Empleado en dos subtipos de acuerdo al tipo de jornada de cada Empleado donde cada subtipo tiene un atributo propio de su subtipo que no tiene el supertipo Empleado. Además, uno de los dos subtipos posibles también mantiene una relación que no tiene el resto de empleados:

//`total`, `disjunta`  
`entity` EmpleadoTiempoCompleto `extends` Empleado{  
    dni `key`  
salario  
}

//`total`, `disjunta`
`entity` EmpleadoTiempoParcial `extends` Empleado{  
 dni `key`  
horas_semanales
}

`entity` Sindicato{  
 cod_sindicato `key`
}

`relationship` afiliadoA{  
EmpleadoTiempoParcial[`1..1`] -> Sindicato[`1..N`]
}

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente pantalla:

![](../resources/BD%20-%20BigER%20Jerarquia%20Afiliado%20A.png)

Puedes ver como, al estar en fase beta, BigER no es capaz de mostrar las restricciones de completitud y disyunción en el diagrama.

Para poder mostrar estas restricciones, al igual que ocurría con los atributos de relación, en el diagrama final tendremos que recurrir a otras herramientas que nos permitan incluir líneas y texto sobre la imagen del diagrama cuando tengamos nuestro diagrama terminado.

Como ya hemos comentado, en nuestro caso, en los distintos diagramas E/R que ves en la asignatura, estamos recurriendo a PowerPoint (también es igual de sencillo en cualquier otro editor de presentaciones) dado lo fácil que es incluir sobre una imagen tanto distintas formas como texto.

Así, puedes ver a continuación este diagrama tras retocar la imagen para incluir un cuadro de texto con las iniciales de las restricciones que apliquen en la especialización/generalización ( t,d o t,s o p,d o p,s o p) cerca de las flechas de la especialización/generalización y añadir también una línea recta uniendo las flechas a las que aplica dicha especialización/generalización:

![](../resources/BD%20-%20BigER%20Jerarquia%20Empleado%20retocada.png)

---
### Ejercicio 08 - Jerarquías (1)

Abre el archivo ModeloEERUniversidad.erd que será donde vamos a ir modelando el ejemplo de jerarquía de especialización/generalización de la Universidad que se ve en la en la sección 7.7.A del tema 5 de la asignatura.

En primer lugar, escribe la descripción de la entidad PersonaUEx que va a ser la raíz de toda la jerarquía:

`entity` PersonaUEX {  
nif `key`  
}

A continuación vamos a hacer la primera especialización de PersonaUEx entre Empleado y Estudiante.

¿Cuál es la forma correcta de describir estos dos subtipos de entidad en BigER?

- Opción 1
//`total`, `disjunta`
`entity` Empleado `extends` PersonaUEX {
nif `key`
}
//`total`, `disjunta`
`entity` Estudiante `extends` PersonaUEX {
nif `key`
}

- Opción 2
///`total`, `solapada`
`entity` Empleado `extends` PersonaUEX {
nif `key`
}
//`total`, `solapada`
`entity` Estudiante `extends` PersonaUEX {
nif `key`
}

- Opción 3
//`parcial`, `solapada`
`entity` Empleado `extends` PersonaUEX {
nif `key`
}
//`parcial`, `solapada`
`entity` Estudiante `extends` PersonaUEX {
nif `key`
}

- Opción 4
//`parcial`, `disjunta`
`entity` Empleado `extends` PersonaUEX {
nif `key`
}
//`parcial`, `disjunta`
`entity` Estudiante `extends` PersonaUEX {
nif `key`
}

---

### Ejercicio 09 - Jerarquías (2)

Seguimos con el archivo ModeloEERUniversidad.erd donde estamos modelando el ejemplo de jerarquía de especialización/generalización de la Universidad que se ve en  la sección 7.7.A del tema 5 de la asignatura.

Vamos a añadir lo siguiente a lo que ya llevamos:

//`total`, `disjunta`
`entity` Docente `extends` Empleado {
nif `key`
}
//`total`, `disjunta`
`entity` PTGAS `extends` Empleado {
nif `key`
}

A continuación vamos a añadir la especialización de Docente en Catedratico y Titular (ten en cuenta que existen más subtipos de docente que no van a aparecer en nuestra jerarquía (Sustituto, Ayudante,...) aparte de estos dos antes de contestar.

¿Cuál es la forma correcta de describir estos dos subtipos de la entidad Docente en BigER?

- Opción 1
//`total`, `disjunta`
`entity` Catedratico `extends` Docente {
nif `key`
}
//`total`, `disjunta`
`entity` Titular `extends` Docente {
nif `key`
}

- Opción 2
///`total`, `solapada`
`entity` Catedratico `extends` Docente {
nif `key`
}
//`total`, `solapada`
`entity` Titular `extends` Docente {
nif `key`
}

- Opción 3
//`parcial`, `solapada`
`entity` Catedratico `extends` Docente {
nif `key`
}
//`parcial`, `solapada`
`entity` Titular `extends` Docente {
nif `key`
}

- Opción 4
//`parcial`, `disjunta`
`entity` Catedratico `extends` Docente {
nif `key`
}
//`parcial`, `disjunta`
`entity` Titular `extends` Docente {
nif `key`
}

---

### Ejercicio 10 - Jerarquías (3)

Seguimos con el archivo ModeloEERUniversidad.erd donde estamos modelando el ejemplo de jerarquía de especialización/generalización de la Universidad que se ve en la sección 7.7.A del tema 5 de la asignatura.

Vamos a añadir lo siguiente a lo que ya llevamos:

//`parcial`
`entity` EstudiantePolitecnica `extends` Estudiante {
nif `key`
}
//`total`, `disjunta`
`entity` MayorDeEdad `extends` EstudiantePolitecnica {
nif `key`
}
//`total`, `disjunta`
`entity` MenorDeEdad `extends` EstudiantePolitecnica {
nif `key`
}

A continuación vamos a añadir la especialización de EstudiantePolitecnica en EstudianteInformatica, EstudianteCivil y EstudianteEdificacion y ten en cuenta que puede haber estudiantes de varias titulaciones a la vez y que hay más titulaciones en Politécnica.

¿Cuál es la forma correcta de describir estos tres subtipos de la entidad EstudiantePolitecnica en BigER?

- Opción 1
//`total`, `disjunta`
`entity` EstudianteInformatica `extends` EstudiantePolitecnica {
nif `key`
}
//`total`, `disjunta`
`entity` EstudianteCivil `extends` EstudiantePolitecnica {
nif `key`
}
//`total`, `disjunta`
`entity` EstudianteEdificacion `extends` EstudiantePolitecnica {
nif `key`
}

- Opción 2
//`total`, `solapada`
`entity` EstudianteInformatica `extends` EstudiantePolitecnica {
nif `key`
}
//`total`, `solapada`
`entity` EstudianteCivil `extends` EstudiantePolitecnica {
nif `key`
}
//`total`, `solapada`
`entity` EstudianteEdificacion `extends` EstudiantePolitecnica {
nif `key`
}

- Opción 3
//`parcial`, `solapada`
`entity` EstudianteInformatica `extends` EstudiantePolitecnica {
nif `key`
}
//`parcial`, `solapada`
`entity` EstudianteCivil `extends` EstudiantePolitecnica {
nif `key`
}
//`parcial`, `solapada`
`entity` EstudianteEdificacion `extends` EstudiantePolitecnica {
nif `key`
}

- Opción 4
//`parcial`, `disjunta`
`entity` EstudianteInformatica `extends` EstudiantePolitecnica {
nif `key`
}
//`parcial`, `disjunta`
`entity` EstudianteCivil `extends` EstudiantePolitecnica {
nif `key`
}
//`parcial`, `disjunta`
`entity` EstudianteEdificacion `extends` EstudiantePolitecnica {
nif `key`
}

---



