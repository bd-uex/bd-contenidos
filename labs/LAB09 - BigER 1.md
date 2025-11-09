# Uso de BigER

## Objetivos

1.-Instalar y aprender a usar la extensión BigER de VsCode para modelado Entidad Relación

2.-Familiarizarse con la **sintaxis** de la notación BigER, comprendiendo cómo se describen las entidades, atributos, relaciones y cardinalidades.

3.-Modelar **entidades débiles** que dependen de otras entidades para existir, comprendiendo cómo utilizar las claves parciales.

4.-Aplicar la notación para solucionar problemas de diseño E/R

---
## Contenidos

### Instalación de BigER

### Notación BigER

- 1.- Creación de Entidades y Atributos
- 2.- Relaciones entre Entidades
- 3.- Entidades Dependientes y Relaciones Débiles
- 4.- Roles
### Resolución del Enunciado ER 2 The Expanse Simplificado

---
## Instalación y uso de BigER en VSCode

Si no lo has hecho ya, instala BigER en VSCode siguiendo la guía que tenemos preparada en el repositorio de la asignatura: [Instalación y uso de BigER en VSCode](Instalaci%C3%B3n%20y%20uso%20de%20BigER%20en%20VSCode.md)

---

##  Introducción a la notación textual de BigER (1) Entidades y atributos

La notación textual **BigER** (resumen completo de la notación disponible [aquí](https://github.com/bd-uex/bd-contenidos/blob/main/Resumen%20BigER.md)) es una representación textual que permite describir **diagramas Entidad-Relación** (E/R) de forma estructurada y comprensible.

Esta notación es especialmente útil en entornos donde es más fácil trabajar con texto en lugar de crear diagramas visuales manualmente, como es el caso de la extensión **BigER** para Visual Studio Code. A través de esta sesión, aprenderemos gradualmente a utilizar esta notación con ejemplos que van desde lo más básico hasta lo más avanzado.

Antes de comenzar, vamos a comenzar creando 2 archivos **.erd** en **Vs Code**:

1. EjemplosNotacionBigER.erd
2. ModeloTheExpanseV1Simplificado.erd

Para crear estos archivos, en VsCode, elige Archivo->Nuevo Archivo y elige **New Empty ER Model**:

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Menu%20Nuevo%20Archivo.png)

Una vez creados los 2 archivos, ve al archivo EjemplosNotacionBigER.erd que será sobre el que escribamos los ejemplo iniciales con la notación.

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

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Entidad%20y%20Atributos.png)

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
![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20BigER%20Entidad%20Estudiante.png)

Escribe en la notación BigER cómo modelar la entidad **Estudiante**

---

## Introducción a la notación textual de BigER (2) Relaciones

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

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Relacion%20Estudiante%20Titulacion%20Chen.png)

Como puedes observar, BigER por defecto usa una notación gráfica distinta a la notación Crow's foot que queremos en nuestros diagramas.

Hay dos maneras de conseguir que BigER nos muestre el diagrama E/R en notación Crow's foot:

- Incluyendo `notation`=crowsfoot justo en al línea a continuación del inicio del fichero donde aparece `erdiagram` NombreModelo
- Seleccionando dicha notación al hacer clic en el botón de cambio de notación ![](../resources/BD%20-%20BigER%20Botón%20Notación.png) y eligiendo Crow's foot

Tanto si lo haces de una forma como de otra ya deberías poder ver así el diagrama:

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Estudiante%20Titulacion%20Matriculado%20En.png)

Como acabamos de ver, aunque BigER admite atributos al describir una relación, no los muestra.

Si pasas el ratón por encima del rombo que modela la relación, verás que aunque no lo muestre, sí lo está incluyendo como atributo en la relación tal y como se ve en la siguiente imagen:

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Atributo%20en%20Matriculado%20En.png)

Para poder mostrar este, y cualquier otro atributo de relación, en el diagrama final tendremos que recurrir a otras herramientas que nos permitan incluir líneas y texto sobre la imagen del diagrama cuando tengamos nuestro diagrama terminado.

Cualquier editor de texto, de presentaciones o de imagen suele soportar ese tipo de inclusión y se puede recurrir a la que cada estudiante conozca con más profundidad.

En nuestro caso, en los distintos diagramas E/R que ves en la asignatura, estamos recurriendo a PowerPoint (también es igual de sencillo en cualquier otro editor de presentaciones) dado lo fácil que es incluir sobre una imagen tanto distintas formas como texto.

Así, puedes ver a continuación el diagrama tan incluir su imagen (rápidamente mediante captura de pantalla) en una diapositiva de Powerpoint, incluir el nombre del atributo como cuadro de texto y unir el rombo y el texto insertando una línea recta:

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Atributo%20en%20Matriculado%20En%20Retocado.png)

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

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Debil%20Titulacion%20Asignatura%20Crows%20Foot.png)

Puedes ver como tanto la entidad dependiente Asignatura como la relación débil compuestaDe aparecen con borde negro más grueso para indicar que son débiles. Cuando las dibujemos a mano normalmente usaremos doble borde en lugar de un borde grueso negro para que se distingan mejor.

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

![](https://github.com/bd-uex/bd-contenidos/raw/main//resources/BD%20-%20BigER%20Relacion%20Estudiante%20con%20Roles.png)
Puedes ver como ahora en cada extremo de la relación esMentorDe aparece el rol tomado en cada extremo de la relación por el Estudiante.

---
## Resolución del Enunciado ER 2 The Expanse Simplificado

Los ejercicios a continuación están orientados a resolver el Enunciado ER 2 The Expanse Simplificado disponible [aquí](https://github.com/bd-uex/bd-contenidos/blob/main/Enunciados%20ER/Enunciado%20ER%202%20The%20Expanse%20Simplificado.md)

---
### Ejercicio 02 - Enunciado ER 2 The Expanse Simplificado  (1)

Abre el archivo ModeloTheExpanseV1Simplificado.erd que será donde vamos a ir modelando el enunciado Enunciado 2 ER The Expanse v1 simplificado.

Vamos, a comenzar centrándonos en los 3 primero párrafos:

---
En un futuro no tan lejano, donde los humanos ya no solo sueñan con Marte, sino que habitan y explotan casi todos los rincones de nuestro sistema solar, la **OPA (Outer Planets Alliance)** ha decidido organizar el caos de sus explotaciones espaciales. Sabemos que, si hay algo que su responsable, Camina Drummer, odia más que la corrupción terrícola, es un mal control de recursos. Así que, en un intento por poner orden, la OPA te ha pedido que diseñes una base de datos para organizar los centros mineros repartidos por el sistema.

Camina Drummer nos ha pedido una versión simplificada de lo que necesitan y si le convence nuestro diseño inicial nos encargarán realizar la versión completa.

Los **centros mineros** de los **planetas** son la joya de la corona de la OPA. Cada uno tiene su propio código único, algo parecido a los tatuajes de identificación que usan los cinturonianos. Además, tienen nombre, fecha de creación y unas coordenadas exactas porque, bueno, no querrás perderte en una estación minera en algún asteroide perdido. Cada planeta puede tener más de un centro (sí, Marte está lleno de ellos), pero siempre hay uno que es el principal en cada planeta. De los planetas almacenamos un código del planeta, su nombre, nº de lunas, periodo de rotación (en horas) y órbita (en días).

---

Básicamente todo estos párrafos se pueden resumir en que tenemos dos entidades fuertes:
 - Planeta con un código único (candidato idóneo a ser Clave Primaria), un nombre, un número de lunas, un periodo de rotación en horas y órbita en días.
 - Centro: con un código único (candidato idóneo a ser Clave Primaria), nombre, fecha de creación y unas coordenadas exactas.

Con esta información ya podemos modelar las dos primeras entidades mencionadas, para ello, escribe el texto BigER necesario para modelar los Planetas y los Centros mineros de manera que nuestro diagrama ER tenga estas dos entidades tal y como se muestran en la siguiente imagen:

![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20BigER%20Enunciado%202%20ER%20The%20Expanse%20v1%20simplificado%20Lab09%20parte%201.png)

---

### Ejercicio 03 - Enunciado ER 2 The Expanse Simplificado  (2)

Vamos, a continuar centrándonos en los 3 siguientes párrafos:

---
Por supuesto, en este futuro brillante (o polvoriento, dependiendo del punto de vista), la extracción de **minerales** es lo más importante. Cada mineral tiene un código, un nombre y una descripción física. Esto último es clave porque algunos minerales, como te dirá cualquier minero cinturoniano, pueden ser más volátiles de lo que parecen. Los **centros** deben llevar un control detallado de qué minerales han encontrado y, más importante aún, en qué fecha fue el primer hallazgo. Puede haber minerales que ningún centro ha tenido la suerte de hallar todavía y no todos los centros han tenido la suerte de encontrar minerales. Por otra parte, debe mantenerse información de la cantidad extraída por día de cada mineral en cada centro (puede haber minerales para los que no se extraiga nada durante algún día o en algunos períodos de tiempo).

El mineral extraído no se queda flotando por ahí. Se organiza en **cargamentos** que luego son transportados a otros planetas o entre centros, dependiendo de las necesidades (y del equilibrio político entre Marte, la Tierra y el Cinturón, claro). Cada cargamento tiene un código único interno consecutivo de cargamento dentro del centro de origen asociado, (es decir, el primer cargamento empaquetado por el centro X7 de Marte tendrá el valor c1 para este campo, el segundo el C2, etc… mientras que el primer cargamento del centro T1 de Titán también tendrá el valor C1, el segundo el C2, etc..). Además, debemos saber el mineral que incluye (sólo uno) y su cantidad, así como la fecha de empaquetado, porque, como siempre, el tiempo es oro... o en este caso, platino.

Pero, ¿cómo mover estos preciados cargamentos entre planetas? Aquí es donde entran en juego las **naves espaciales** de la OPA. Cada nave tiene una matrícula, un modelo, y por supuesto, una capacidad de carga, que más de un cinturoniano intentará sobrepasar en algún momento. También deben registrar el planeta al que están asignadas, para que no acaben en el lugar equivocado (una nave en el lugar equivocado en el momento equivocado puede terminar mal, pregúntale a los terrícolas). Los **vuelos** de estas naves son otro asunto. La OPA necesita saber cuándo despegan y aterrizan, desde qué centro comenzaron el viaje y a cuál llegaron, y qué cargamentos transportaron en cada vuelo.

---

Aquí ya nos están dando mucha información, pero vamos a centrarnos por ahora en buscar más entidades fuertes como eran Centro y Planeta.

En una primera lectura podemos pensar que tenemos cuatro entidades fuertes más:
	- Mineral: con un código único (candidato idóneo a ser Clave Primaria), nombre y descripción de sus propiedades.
	- Cargamento: código único interno consecutivo y fecha del empaquetado. 
		- Ten cuidado de no confundirte con esta entidad, el mineral que transporta es una relación que tendrá con mineral y la cantidad un atributo de dicha relación.
	- NaveEspacial: con una matrícula, modelo y capacidad.
	- Vuelo: con fecha de inicio del vuelo, hora de inicio del vuelo, fecha de fin del vuelo y hora del fin del vuelo.
		- Ten cuidado de no confundirte con esta entidad, los centros entre los que vuela y los cargamentos que transportan son relaciones de la entidad.

Pero, ¿qué ocurre con los Cargamentos? Tal y como nos dice el enunciado no tienen un atributo que permita actuar como Clave Primaria ya ese código interno único es solo único para cada centro. Además, ¿qué pasaría con la información de los Cargamentos si desapareciera el Centro donde se almacena?

Del mismo modo, ¿qué ocurre con los Vuelos? Tal y como nos dice el enunciado no tienen un atributo que permita distinguir vuelos que despeguen y aterricen a la misma hora.. Además, ¿qué pasaría con la información de los Vuelos si desapareciera la Nave Espacial que los realiza?

En resumen, Cargamento y Vuelo no son entidades fuertes y las modelaremos después, por ahora vamos a modelar las entidades fuertes de estos tres párrafos, es decir, Mineral y Nave Espacial.

Escribe el texto BigER necesario para modelarlas de manera que nuestro diagrama ER tenga estas dos nuevas entidades tal y como se muestran en la siguiente imagen:

![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20BigER%20Enunciado%202%20ER%20The%20Expanse%20v1%20simplificado%20Lab09%20parte%202.png)

---
### Ejercicio 04 - Enunciado ER 2 The Expanse Simplificado  (3)

Vamos a modelar alguna de las relaciones del diagrama con las entidades que tenemos hasta ahora:
	- En primer lugar, vamos a modelar la relación **ubicadoEn** entre Centros y Planetas. Por lo que nos dice el enunciado, podemos entender que un Centro solo puede estar en un Planeta mientras que un Planeta que guardemos en nuestra Base de Datos siempre tendrá, como mínimo un Centro en él.
	- En segundo lugar, vamos a modelar la relación **esPrincipalEn** entre Centro y Planeta. Por lo que dice el enunciado, un Planeta tendrá siempre un Centro como principal y solo uno, mientras que un Centro no tiene por qué ser principal.
	- En tercer lugar, la relación **encontradoPor** entre Centro y Mineral.  Por lo que nos dice el enunciado, puede que haya centros que nunca hallen un mineral y otros que hallen muchos, mientras que puede haber minerales que nunca sean hallados por ningún centro y otros que sean hallados por muchos. Además, de esta relación tenemos que guardar un atributo llamado fechaHallazgo ya que no tiene sentido que ese atributo sea del Centro o del Mineral.
	- Por último, vamos a modelar la relación **asignadaA** entre NaveEspacial y Planeta. Por lo que nos dice el enunciado, una instancia de NaveEspacial solo puede estar asociado a un planeta mientras que una instancia de Planeta estará relacionada, como mínimo, con una instancia de NaveEspacial y, como máximo, con muchas instancias de NaveEspacial.

Escribe el texto BigER necesario para modelarlas y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente cada relación.

---
### Ejercicio 05 - Enunciado ER 2 The Expanse Simplificado  (4)

Vamos a tratar de modelar la entidad Vuelo que dejamos sin modelar al modelar las entidades fuertes en los ejercicios previos.

Ya vimos que, tal y como nos dice el enunciado no tienen un atributo que permita distinguir vuelos que despeguen y aterricen a la misma hora.. Además, ¿qué pasaría con la información de los Vuelos si desapareciera la Nave Espacial que los realiza?

Todo esto nos tiene que llevar a modelar Vuelo como **Entidad débil** que va a depender de NaveEspacial. ¿Cuál será la clave parcial que aportan los vuelos para, junto con la clave de la entidad fuerte NaveEspacial, poder distinguir un vuelo de cualquier otro en la Base de Datos? Podríamos pensar que con la fecha de despegue del vuelo es suficiente pero, ¿qué pasa si una nave realiza más de un vuelo el mismo día? Con la fecha de despegue ya vemos que no es suficiente pero si además de la fecha de despegue, incluimos la hora del despegue ya sí tenemos los suficientes atributos para distinguir cualquier vuelo de otro.

Con todo esto, en nuestro diagrama ER tendemos que:

1.- Añadir la **Entidad débil** **Vuelo** que está formada por los atributos:
- fechaIni: clave parcial aportada por Vuelo que tenemos que combinar como clave parcial para formar la clave primaria.
- horaIni: clave parcial aportada por Vuelo que tenemos que combinar como clave parcial para formar la clave primaria.
- fechaFin
- horaFin

2.- Añadir la **relación débil** **realizadoPor** entre Vuelo y NaveEspacial. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD  Naves que todavía no hayan realizado vuelos y tiene sentido que sí tengamos dado que un Planeta cada vez que reciba una Nave nueva esta todavía no habrá volado.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 06 - Enunciado ER 2 The Expanse Simplificado  (5)

Ahora que tenemos la entidad débil Vuelo, podemos acabar de modelar el resto de sus relaciones:
	- En primer lugar, vamos a modelar la relación **origenDe** entre Vuelo y Centro. Por lo que nos dice el enunciado, podemos entender que puede haber todavía algún Centro que no haya sido el origen de ningún Vuelo, pero un Vuelo siempre debe tener un Centro de Origen.
	- En segundo lugar, vamos a modelar la relación **destinoDe** entre Vuelo y Centro. Por lo que nos dice el enunciado, podemos entender que puede haber todavía algún Centro que no haya sido destino de ningún Vuelo, pero un Vuelo siempre debe tener un Centro de Destino (vamos a suponer que un Vuelo no tiene ningún incidente que le haya impedido alcanzar su destino).

Escribe el texto BigER necesario para modelarlas y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente cada relación.

---

### Ejercicio 07 - Enunciado ER 2 The Expanse Simplificado  (6)

Vamos ahora a tratar de modelar la entidad Cargamento que dejamos sin modelar al modelar las entidades fuertes en los ejercicios previos.

Ya vimos que, tal y como nos dice el enunciado, un Cargamento no tiene un atributo que permita distinguir entre los distintos cargamentos de los distintos centros ya que ese código interno único es solo único para cada centro. Además, ¿qué pasaría con la información de los Cargamentos si desapareciera el Centro donde se almacena?

Todo esto nos tiene que llevar a modelar Cargamento como **Entidad débil** que va a depender de Centro. ¿Cuál será la clave parcial que aportan los cargamentos para, junto con la clave de la entidad fuerte Centro, poder distinguir un vuelo de cualquier otro en la Base de Datos? En este caso con el código único interno para cada centro ya sí sería suficiente.

Con todo esto, en nuestro diagrama ER tendemos que:

1.- Añadir la **Entidad débil** **Cargamento** que está formada por los atributos:
- codCargam: clave parcial aportada por Cargamento para formar la clave primaria.
- fechaCargam

2.- Añadir la **relación débil** **almacenadoEn** entre Cargamento y Centro. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Centro que todavía no haya tenido un Cargamento y tiene sentido que sí dado que un Centro puede estar recién construido sin haber encontrado ni extraído ningún mineral.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 08 - Enunciado ER 2 The Expanse Simplificado  (7)

Ahora que tenemos la entidad débil Cargamento, podemos acabar de modelar el resto de sus relaciones:
	- En primer lugar, vamos a modelar la relación **formaParteDe** entre Cargamento y Mineral. Por lo que nos dice el enunciado, puede que un mineral todavía no haya formado parte de ningún cargamento y otros que estén en muchos. Además, esta relación tiene un atributo llamado cantidad.
	- En segundo lugar, vamos a modelar la relación **transportadoEn** entre Cargamento y Vuelo. Por lo que nos dice el enunciado, puede que haya cargamentos que todavía no hayan sido transportados pero los vuelos, al menos, transportarán un cargamento cada vez que se producen.

Escribe el texto BigER necesario para modelarlas y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente cada relación.

---
### Ejercicio 09 - Enunciado ER 2 The Expanse Simplificado (8)

Para terminar, nos falta modelar esta parte del cuarto párrafo del enunciado:

---
Por otra parte, debe mantenerse información de la cantidad extraída por día de cada mineral en cada centro (puede haber minerales para los que no se extraiga nada durante algún día o en algunos períodos de tiempo).

---

Esto nos dice que para cada centro y mineral extraído por ese centro, debemos saber la cantidad concreta extraída de cada uno cada día. Es decir, un mineral concreto (oro, platino, etc) puede ser extraído el mismo día por distintos centros (Titán1, Marte2, Saturno3, etc..) y un centro  (Titán1, Marte2, Saturno3, etc..) puede extraer distintos minerales  (oro, platino, etc) el mismo día. Es decir es una relación con cardinalidad máxima N en ambos lados. 

Además, podemos ver que esta relación entre Centro y Mineral no es estática, cambia con el tiempo. Si probamos a escribir ejemplos de esta relación, por ejemplo, el centro Titán1 extrae 10 kilos de platino el 25/11/2235, el centro Titán1 extrae 20 kilos de platino el 26/11/2235, el centro Titán1 extrae 15 kilos de platino el 27/11/2235, etc, vemos que el mismo par (Centro, Mineral) se puede repetir por lo que los atributos de la relación, fecha y cantidad, realmente sería multivaluado.

Este caso se corresponde con lo visto para Entidades Débiles en el apartado 5.4.2 "Caso 2: Para gestionar atributos multivaluados en una relación M:N con repetición de relaciones entre instancias" del Tema 5.

Si recordamos ese apartado, esto significa convertir la interacción a lo largo del tiempo en una **entidad débil con repetición** (o histórica). Esta nueva entidad guardará cada "evento" de la relación, y su clave primaria casi siempre incluirá un atributo de tiempo (como curso o fecha).

En nuestro ejemplo de un Centro extrae Mineral, vamos a convertir la relación en una entidad débil llamada **HistoricoExtraccion**. Dado que en una fecha concreta, una instancia de Centro puede relacionarse con **varias** instancias de Mineral, y, varias instancias de Mineral estar relacionadas con **varias** de Centro, esta entidad depende tanto de Centro como de Mineral. 

¿Cuál será la clave parcial que aporta la relación para, junto con la clave de la entidad fuerte Centro y la clave de la entidad fuerte Mineral, poder distinguir una extracción de cualquier otra en la Base de Datos? En este caso usaremos la fecha ya que con ella es suficiente.

Con todo esto, en nuestro diagrama ER tendemos que:

1.- Añadir la **Entidad débil** **HistoricoExtraccion** que está formada por los atributos:
- fecha: clave parcial aportada por la relación que permite distinguir la extracción de un día de Mineral por un Centro de la extracción de ese mismo mineral por ese mismo Centro cualquier otro día
- cantidad

2.- Añadir la **relación débil** **extraidoPor** entre HistoricoExtraccion y Centro. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Centro que todavía no haya extraído ningún Mineral y tiene sentido que sí dado que un Centro puede estar recién construido sin haber encontrado ni extraído ningún mineral.

3.- Añadir la **relación débil** **incluidoEn** entre HistoricoExtraccion y Mineral. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Mineral que todavía no haya estado incluido en ninguna extracción de ningún Centro y tiene sentido que sí.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Fin

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el enunciado Enunciado 2 ER The Expanse v1 simplificado.

![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---

