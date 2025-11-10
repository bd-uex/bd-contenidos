# Uso de BigER

## Objetivos

1.-Familiarizarse con la **sintaxis** de jerarquías de la notación BigER.

2.-Gestionar **herencia** o **especialización** de entidades, representando subtipos y supertipos de forma correcta en la notación BigER

3.-Aplicar la notación para solucionar problemas de diseño E/R

---
## Contenidos

### Notación BigER

- 1.- Herencia y Especialización
### Resolución del Enunciado ER 4 The Expanse Completo

---
## Introducción a la notación textual de BigER (5) Jerarquías de herencia

Vamos a comenzar viendo cómo se modelan **jerarquías de especialización/generalización** de entidades.

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

Crea el archivo EjemplosEERBigER.erd:

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
### Ejercicio 01 - Jerarquías (1)

Crea el archivo ModeloEERUniversidad.erd que será donde vamos a ir modelando el ejemplo de jerarquía de especialización/generalización de la Universidad que se ve en la en la sección 7.7.A del tema 5 de la asignatura.

![](resources/BD%20-%20BigER%20Jerarquia%20Universidad.png)

En primer lugar, escribe la descripción de la entidad PersonaUEx que va a ser la raíz de toda la jerarquía:

`entity` PersonaUEX {  
nif `key`  
}

A continuación vamos a hacer la primera especialización de PersonaUEx entre Empleado y Estudiante.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 02 - Jerarquías (2)

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

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 03 - Jerarquías (3)

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

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

## Resolución del Enunciado ER 04 The Expanse Completo

Los ejercicios a continuación están orientados a resolver el Enunciado ER 04 The Expanse Completo disponible [aquí](https://github.com/bd-uex/bd-contenidos/blob/main/Enunciados%20ER/Enunciado%20ER%2004%20The%20Expanse%20Completo.md)

---
### Ejercicio 04 - Enunciado ER 04 The Expanse Completo  (1)

Crea el archivo EnunciadoER04TheExpanseCompleto.erd que será donde vamos a ir modelando el enunciado Enunciado 4 ER The Expanse Completo.

Para empezar, copia en este archivo en contenido del fichero ModeloTheExpanseV1Simplificado.erd que hiciste en el anterior Laboratorio ya que partiremos de ese para realizar este enunciado.

Antes de seguir, puedes comprobar que partimos del modelo ER correcto copiando todo el contenido a continuación:

---
### Ejercicio 05 - Enunciado ER 04 The Expanse Completo  (2)

Vamos, a comenzar centrándonos en el primer párrafo que contiene cambios con respecto al enunciado original:

---
Por supuesto, en este futuro brillante (o polvoriento, dependiendo del punto de vista), la extracción de **minerales** es lo más importante. Cada mineral tiene un código, un nombre y una descripción física. _Por otra parte ,existen **distintas clases de minerales** (silicatos, sulfatos, nitratos, etc.) según su composición química. Esta clasificación debe estar estandarizada. Un mineral sólo pertenece a una de estas clasificaciones, aunque, como es obvio, varios minerales pueden ser de la misma clase. Además, hay minerales que están relacionados con otros, en el sentido de que si se encuentra alguno de ellos, es bastante probable encontrar los otros en algún lugar cercano. Es importante mantener la información de qué minerales están relacionados con otros._

---

En primer lugar, tenemos una nueva entidad fuerte:
 - Clase_Mineral, con un código de clase de mineral (candidato idóneo a ser Clave Primaria) y un nombre de clase de mineral.

Tenemos que modelar también las relaciones contenidas en la información dada:
	- En primer lugar, vamos a modelar la relación **agrupaA** entre Mineral y Clase_Mineral. Por lo que nos dice el enunciado, podemos entender que un Mineral solo puede pertenecer a una de las clases mientras que una Clase_MIneral que guardemos en nuestra Base de Datos siempre tendrá, como mínimo un Mineral en él.
	- En segundo lugar, vamos a modelar la relación recursiva **relacionadoCon** de Mineral consigo misma. Por lo que dice el enunciado, un Mineral puede estar o no relacionado con minerales. Como es una relación recursiva debemos poner un rol a cada lado de la relación pero dado que ambos lados de la relación tienen la misma cardinalidad los roles son conmutativos por lo que vamos simplemente a darle a un rol el nombre Mineral_A y al otro Mineral_B.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 06 - Enunciado ER 04 The Expanse Completo  (3)

Vamos, a continuar con el siguiente párrafo que contiene cambios con respecto al enunciado original:

---
_La OPA no solo cuenta con naves espaciales, también dispone en cada planeta de vehículos de distinto tipo: vehículos para transporte de personal por la superficie de un planeta, vehículos específicos para la extracción de minerales, etc. Al igual que de las naves espaciales, cada vehículo está identificado por su matrícula, y se almacenan detalles sobre su modelo y también es clave saber en qué planeta o estación está asignado. Todo lo relativo a la gestión de las operaciones y los transportes que se realizan con el resto de los tipos de vehículos no se incluirá de momento en la base de datos._

---

En primer lugar, nos hablan de los Vehículos y podemos ver que hay muchas similitudes entre Vehículo y Nave Espacial:
	- Atributos matrícula y modelo.
	- Ambos están asignados a un Planeta.

También podemos ver que hay algunas diferencias:
	- Nave Espacial tienen atributo capacidad que no tiene un Vehículo genérico.
	- Nave Espacial son los únicos vehículos que siguen realizando Vuelos.

En realidad, todo esto son pistas para inferir que un Vehículo es una generalización de una Nave Espacial, ya que tiene un subconjunto de los atributos y relaciones de una Nave Espacial.

Por tanto, debemos modificar el diagrama para:
	- Añadir la nueva entidad fuerte Vehículo con sus atributos.
	- Modificar la entidad Nave_Espacial:
		- Indicar que pasa a extender a Vehículo (dado que es la única subclase de vehículo, es fácil suponer qué comentario hay que añadir antes de declararla).
		- Como atributos pasa a tener solo la Clave Primaria que comparte con Vehículo y el atributo capacidad que es exclusivo suyo. El atributo Modelo solo estará en Vehículo. (Naves Espaciales hereda este atributo de manera implícita al extender a Vehículo).
		- Modificar la relación asignadaA de manera que ahora son los Vehículos los que están relacionados con los Planetas (Naves Espaciales hereda esta relación de manera implícita al extender a Vehículo).

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 07 - Enunciado ER 04 The Expanse Completo  (4)

Vamos, a continuar con el siguiente párrafo que contiene cambios con respecto al enunciado original:

---
_Como otra gran novedad, la OPA, siempre buscando mantener el orden en sus operaciones por todo el sistema solar, también necesita llevar un control detallado del **personal** que trabaja en sus centros mineros. Así, cada persona de la OPA está identificado por un número único de personal y se registran datos como su nombre completo, pasaporte (ya sea de la Tierra, Marte o alguna estación espacial independiente), la fecha en la que se unió a la OPA, el centro minero al que está asignado, y su puesto (que puede ser desde directivo, astronauta, técnico de extracción, o hasta incluso uno de esos conductores que maniobran cargueros por los túneles de las estaciones mineras). Además, cada uno de los **centros** debe tener un coordinador. Este coordinador, por supuesto, solo pude ser alguien con un puesto de directivo de la OPA, y solo puede estar al mando de un centro a la vez. La OPA necesita saber, y con precisión, desde cuándo este directivo está a cargo del centro y nos dicen que no es necesario saber qué Directivos pudo tener anteriormente un Centro. También es necesario saber qué astronautas fueron los afortunados en pilotar los vuelos teniendo en cuenta que varios astronautas pueden pilotar un vuelo._

---

En primer lugar, parece claro que vamos a tener una nueva entidad fuerte para representar al personal:
 - Personal, con un numero de personal (candidato idóneo a ser Clave Primaria), pasaporte (también candidado a ser Clave Primaria pero lo dejaremos como Clave Alternativa), nombreCompleto (como no nos dan más detalles lo dejaremos como un único atributo), su fecha de alta y su puesto. Las cardinalidades de la relación deberían ser ya fáciles de suponer.

Además, asociado a esta nueva entidad tendremos la relación **trabajaEn** para almacenar la información de qué personal trabaja en cada Centro. Por lo que nos dice el enunciado, podemos entender que el Personal solo trabaja en un Centro mientras que un Centro que guardemos en nuestra Base de Datos siempre tendrá, como mínimo un Personal  en él.

Posteriormente, nos indican que todos los centros deben tener un coordinador que solo puede ser alguien con un puesto de Directivo. Es decir, nos están diciendo que hay una parte del Personal que va a tener una relación que no tiene el resto del Personal, por lo que tendremos que crear una subclase de Personal llamada Directivo que solo se distingue del resto del Personal en que tiene una relación, que llamaremos **coordina** con los Centros. Además, necesitamos almacenar en la relación la fecha de inicio de esta coordinación. Las cardinalidades de la relación deberían ser ya fáciles de suponer.

Por último, nos indican que es necesario saber el Personal que es Astronauta ya que nos están diciendo que solo son ellos los que pilotan vuelos, por lo que tendremos que crear una subclase de Personal llamada Astronauta que solo se distingue del resto del Personal en que tiene una relación, que llamaremos **pilota** con los Vuelos. Por lo que nos dice el enunciado, podemos entender que un Astronauta habrá pilotado al menos un vuelo para ser Astronauta y claramente nos dice que un Vuelo puede tener muchos Astronautas pilotándolos.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el enunciado Enunciado 04 ER The Expanse Completo.

![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---



