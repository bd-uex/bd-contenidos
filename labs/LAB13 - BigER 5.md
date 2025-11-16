# Uso de BigER

## Objetivos

1.-Aplicar la notación para solucionar un examen real de diseño E/R

---
## Contenidos

### Resolución del Enunciado ER 13 Todas Tus Series
---
## Resolución del Enunciado ER 13 Todas Tus Series.

Los ejercicios a continuación están orientados a resolver el Enunciado 13 Todas Tus Series disponible [aquí](https://github.com/bd-uex/bd-contenidos/blob/main/Enunciados%20ER/Enunciado%20ER%2013%20Todas%20Tus%20Series.md)

Crea el archivo EnunciadoER13TodasTusSeries.erd que será donde vamos a ir modelando el Enunciado 13 Todas Tus Series.

---
### Ejercicio 01 - Enunciado ER 13 Todas Tus Series  (1)

Vamos, a comenzar centrándonos en resolver estos párrafos iniciales:

---
Los emprendedores de TodosTusSeries.com han decidido crear la plataforma definitiva para que los adictos a las series en España puedan encontrar su próxima serie y nos han encargado diseñar su base de datos considerando todos los aspectos que se detallan a continuación.

Para empezar, necesitamos almacenar información completa sobre las series que incluiremos en nuestro catálogo. Cada serie tendrá un identificador único, su título, una sinopsis, la fecha de lanzamiento, y el estado actual (en emisión, finalizada, cancelada sin avisar, etc.). Es fundamental recordar que en el mundo del streaming moderno, las series pueden ser producidas por múltiples productoras a la vez; de hecho, es cada vez más común ver colaboraciones entre estudios internacionales, por lo que una serie puede tener varias productoras involucradas. De cada productora guardaremos su identificador, nombre y URL.

Las series necesitan estar clasificadas por géneros para que los usuarios puedan encontrar fácilmente lo que buscan. Mantendremos un catálogo estandarizado de géneros con su identificador, nombre y descripción. Una serie puede pertenecer a múltiples géneros (podemos tener un drama sin más o una mezcolanza “drama-ciencia-ficción-comedia), y por supuesto puede haber géneros que aún no tienen ninguna serie asignada en nuestro catálogo inicial. Además, para evitar que los padres acaben poniendo "The Walking Dead" a sus hijos de 5 años pensando que es sobre senderismo, mantendremos un sistema de clasificación por edades. Cada clasificación tendrá su identificador y edad mínima recomendada. Todas las series deben tener asignada una clasificación por edad obligatoriamente.

---

Esta parte es muy sencilla y no debería suponer ningún problema el llevarla a cabo

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 02 - Enunciado ER 13 Todas Tus Series (2)

Vamos, a continuar con los siguientes párrafos:

---
Hay que tener en cuenta que podemos tener series de una sola temporada o series con muchas temporadas (por ejemplo, Stranger Things ya tiene 4 temporadas emitidas, pero ya se está rodando la temporada 5). Cada temporada se identifica por su número dentro de la serie y además almacenaremos un resumen de sus novedades principales respecto a la temporada anterior (por ejemplo, para la temporada 3 de Stranger Things ese campo contendría algo del estilo de “Se presenta una nueva amenaza para Hawkins y sus habitantes: el Azotamentes”). Cada temporada contendrá un número variable de episodios: de cada uno de ellos guardaremos su número para esa temporada, su título propio, fecha de primera emisión, duración y sinopsis. 

El aspecto multiidioma es crucial en nuestra plataforma. Mantendremos un catálogo estandarizado de idiomas con identificador y nombre. Cada episodio puede tener audio disponible en varios idiomas (al menos uno) y subtítulos en otros tantos(no es obligatorio tenerlos), pero no necesariamente coinciden; por ejemplo, podríamos tener una serie con audio en inglés y coreano, pero con subtítulos disponibles en español, francés e italiano (o incluso sin subtítulos).

---

De esta parte pon especial atención a las posibles entidades débiles y ten cuidado con las cardinalidades en el modelado de los idiomas para audio y subtítulos

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 03 - Enunciado ER 13 Todas Tus Series (3)

Vamos, a continuar con el siguiente párrafo:

---
Detrás del telón aparecen las personas que participan en las series. De estas personas guardaremos su identificador, nombre, apellido, fecha de nacimiento, nacionalidad y una pequeña bio. Las personas que nos interesan se pueden dividir en creadores, directores e intérpretes. Para empezar, nada impide que una persona no pueda ser creador, director e interprete. De los creadores, hay que saber qué series han creado teniendo en cuenta que puede haber series con varios creadores.  Los directores pueden dirigir varios episodios en distintas series y un episodio puede ser dirigido por varios. El tema de los intérpretes y sus actuaciones en episodios es más complejo. De los intérpretes hay que saber su nombre artístico (rara vez usan su nombre de normal). Un intérprete puede actuar representando uno o varios personajes en uno o varios episodios de una serie. De los personajes hay que saber su nombre y descripción. Hay que tener en cuenta que un mismo personaje puede ser interpretado por distintos intérpretes en distintos episodios de la misma serie. Por ejemplo, el personaje de Geralt de Rivia en la serie The Witcher ha sido interpretado primero por Henry Cavill y luego por Liam Hemsworth. Incluso en un mismo episodio, un mismo personaje puede ser interpretado por varios intérpretes, representando distintas versiones (por ejemplo, "Geralt niño" interpretado por un actor infantil y "Geralt anciano" interpretado por otro actor en un flashback). Es fundamental registrar los minutos exactos que dura cada una de estas actuaciones (es decir, cuántos minutos aparece el "Geralt niño" de ese actor, y cuántos el "Geralt anciano" del otro actor, en ese episodio). También debe almacenarse una breve descripción de esa versión específica, como "niño", "adulto" o "anciano", para darle contexto.  

---

De esta parte pon especial atención a las actuaciones de los interpretes haciendo distintos personajes en los episodios. Es un caso muy especial que solo habíamos visto de forma téorica en un laboratorio anterior pero que no aparecía en ninguno de los enunciados anteriores. La pista definitiva para este caso es que tienes que revisar la sección 6 del Tema 5.

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VsCode. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 04 - Enunciado 13 Todas Tus Series (4)

Vamos, a continuar con el siguiente párrafo:

---
 También mantendremos las plataformas de streaming disponibles en España con su identificador, nombre comercial, y URL. El sistema de derechos de emisión de las series es complejo: cada plataforma puede tener licencias para emitir diferentes series durante períodos específicos, con fechas de inicio y fin. Una serie puede estar disponible en múltiples plataformas simultáneamente o puede cambiar de plataforma según expiren los contratos.

---

De esta parte ten en cuenta especialmente la parte "Una serie puede estar disponible en múltiples plataformas simultáneamente o puede cambiar de plataforma según expiren los contratos."

Escribe el texto BigER necesario para modelar lo indicado en ese párrafo. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 05 - Enunciado 13 Todas Tus Series (5)

Vamos, a finalizar con el siguiente párrafo:

---
Para la comunidad de usuarios, registraremos usuarios con identificador, email y nombre de usuario. Los usuarios pueden tener plataformas favoritas, valorar series con una puntuación y un comentario, y también valorar episodios individuales, pero en este caso solo con puntuación. Además, un usuario puede crearse distintas listas personalizadas de series, a las que se les dará un número consecutivo para cada usuario, y tendrán un nombre y descripción. Estas listas pueden ser compartidas con otros usuarios y hay que saber con quienes.

---

Esta parte, tras haber llegado hasta aquí, seguro que puedes hacerla sin ninguna pista.

Escribe el texto BigER necesario para modelar lo indicado en ese párrafo. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el enunciado Enunciado 13 Todas Tus Series.

![](https://github.com/bd-uex/bd-contenidos/raw/main/resources/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---



