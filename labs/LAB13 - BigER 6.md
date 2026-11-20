# Uso de BigER

## Objetivos

1.- Aplicar la notación para solucionar dos exámenes reales de diseño E/R: el **Enunciado ER 14 Todos Tus Libros** y el **Enunciado ER 16 TravelFun**.

---
## Contenidos

### Resolución del Enunciado ER 14 Todos Tus Libros

### Resolución del Enunciado ER 16 TravelFun

---
## Resolución del Enunciado ER 14 Todos Tus Libros

Los ejercicios a continuación están orientados a resolver el Enunciado ER 14 Todos Tus Libros, disponible [aquí](../ejercicios/entidad-relacion/Enunciado%20ER%2014%20Todos%20Tus%20Libros.md).

Crea el archivo **ModeloTodosTusLibros.erd** con **New Empty ER Model** y añade la línea `notation=crowsfoot` justo debajo de la línea `erdiagram`. Será donde vamos a ir modelando el Enunciado ER 14 Todos Tus Libros.

Como en el laboratorio anterior, este enunciado es un **examen real**, así que las pistas serán mínimas. Cada ejercicio incluye los **nombres** que espera el comprobador para que reconozca tu solución. Solo los nombres: decidir qué es cada cosa, sus claves y sus cardinalidades es cosa tuya.

---
### Ejercicio 01 - Enunciado ER 14 Todos Tus Libros  (1)

Vamos a comenzar centrándonos en resolver estos párrafos iniciales:

---
La plataforma TodosTusLibros busca facilitar la reserva de libros a los lectores en las librerías independientes que todavía existen. Nos han pedido diseñar su base de datos.

Así, el sistema debe almacenar la información de los libros, registrando un id único, título, sinopsis, fecha de publicación y los géneros en los que se clasifica. De cada libro hay que saber en qué idioma está teniendo en cuenta que muchos libros son traducciones de otros. Por ejemplo, la base de datos podría contener el libro 'Cien años de soledad' en español y cuando se registrara la versión inglesa 'One Hundred Years of Solitude', la base de datos permitiría indicar que su idioma es inglés y que es una traducción de 'Cien años de soledad'. Hay que tener en cuenta que un libro puede tener muchas traducciones distintas e incluso ser una traducción de otra traducción. Por ejemplo, un libro en chino puede no haber sido traducido al español directamente sino desde una traducción al inglés.

---

De esta parte, pon especial atención a las traducciones. Fíjate en que un libro puede ser traducción de otra traducción: ¿qué tipo de relación necesitas? Y antes de crear una subclase para los libros traducidos, piensa qué datos o relaciones tendría que no tengan los demás libros.

> **Nombres para el comprobador**: las entidades `Libro` (`id_libro`, `titulo`, `sinopsis`, `fecha_publicacion`), `Genero` (`id_genero`, `nombre`) e `Idioma` (`id_idioma`, `nombre_idioma`), y las relaciones `se_clasifica_en`, `esta_en_idioma` y `es_traduccion_de`, con los roles `Traduccion` y `Traducido`.

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 02 - Enunciado ER 14 Todos Tus Libros  (2)

Vamos a continuar con el siguiente párrafo:

---
Tenemos que mantener de los profesionales literarios que escriben y/o traducen libros un identificador, nombre, apellidos y biografía. Entre los profesionales literarios están los autores que escriben los libros que almacenamos, teniendo en cuenta que de un libro puede no conocerse el autor (son anónimos) o que puede tener varios. Además, nos interesa conocer si un autor es especialista en uno o varios géneros o en ninguno, independientemente de que luego escriba algún libro de otro género. Por otro lado, tendremos a los posibles traductores de los libros. Un libro puede tener uno o varios traductores o no tenerlo (si es el libro original). De los traductores es imprescindible registrar en qué idioma o idiomas son expertos. Hay que tener en cuenta que hay autores, no muchos, pero los hay, que escriben sus propios libros y traducen otros.

---

De esta parte, pon especial atención a las restricciones de la jerarquía: ¿puede un profesional literario no ser ni autor ni traductor? ¿Y ser las dos cosas a la vez? Ten cuidado también con las cardinalidades mínimas: el enunciado dice expresamente qué puede no existir.

> **Nombres para el comprobador**: las entidades `Profesional_Literario` (`id_profesional`, `nombre`, `apellido_1`, `apellido_2`, `biografia`), `Autor` y `Traductor`, y las relaciones `escribe`, `es_especialista_en`, `traduce` y `experto_en`.

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 03 - Enunciado ER 14 Todos Tus Libros  (3)

Vamos a continuar con el siguiente párrafo:

---
Lo que se vende al público es la edición física de un libro. Un libro puede no tener ediciones disponibles en ese momento o tener varias ediciones, de manera que podemos tener libros en nuestra base de datos independientemente de si en ese momento hay ediciones disponibles o no. Cada edición tiene su ISBN único, precio oficial, fecha de lanzamiento, páginas y dimensiones y la editorial que la publica. De las editoriales guardaremos su CIF, nombre (que será también único), email y teléfono y debemos tener en cuenta que podemos tener editoriales que en ese momento no tienen editados ejemplares de ningún libro de los mantenidos en nuestra base de datos.

---

Esta parte es sencilla, pero no te olvides de las claves alternativas.

> **Nombres para el comprobador**: las entidades `Edicion_Fisica` (`isbn`, `precio`, `fecha_lanzamiento`, `numero_paginas`, `dimensiones`) y `Editorial` (`cif`, `nombre`, `email`, `telefono`), y las relaciones `tiene_edicion` y `publicada_por`.

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 04 - Enunciado ER 14 Todos Tus Libros  (4)

Vamos a continuar con el siguiente párrafo:

---
El objetivo final de TodosTusLibros es facilitar que los usuarios reserven ediciones físicas en las librerías. De los usuarios guardaremos su NIF, nombre completo, email, teléfono y contraseña. De las librerías mantendremos su CIF, nombre, teléfono, dirección y email. Además, de las librerías necesitamos saber la cantidad en stock disponible de cada edición, teniendo en cuenta que una librería puede estar en un momento dado sin stock de ningún libro de los mantenidos en nuestra base de datos.

---

De esta parte, ten en cuenta especialmente de quién es la cantidad en stock: ¿de la librería, de la edición o de la pareja?

> **Nombres para el comprobador**: las entidades `Usuario` (`nif`, `nombre_completo`, `contrasena`, `email`, `telefono`) y `Libreria` (`cif`, `nombre`, `telefono`, `direccion`, `email`), y la relación `stock_disponible` (`cantidad`).

Escribe el texto BigER necesario para modelar lo indicado en ese párrafo y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 05 - Enunciado ER 14 Todos Tus Libros  (5)

Vamos a finalizar con los últimos párrafos:

---
Hay que tener en cuenta que un usuario puede realizar múltiples reservas (o ninguna). De cada reserva mantendremos un identificador único, la fecha de la reserva y la librería donde se hace. Cada reserva estará compuesta por una o varias líneas de reserva, donde cada línea estará asociada con la edición física deseada y la cantidad. Por ejemplo, si un usuario reserva dos ejemplares de una edición concreta de 'Dune' y otro de una edición concreta de 'El Hobbit', su reserva tendrá dos líneas de reserva: una Línea 1 asociada a la edición elegida de 'Dune' con la cantidad de 2, y una Línea 2 para la edición elegida de 'El Hobbit' indicando la cantidad de 1. El sistema debe ser capaz de almacenar y distinguir estas líneas dentro de la reserva a la que pertenecen.

Por último, los usuarios pueden valorar los libros (no sus ediciones), con una puntuación, comentario y la fecha teniendo en cuenta que solo se permite al usuario que valore una vez cada libro.

---

Esta parte, tras haber llegado hasta aquí, seguro que puedes hacerla sin ninguna pista.

> **Nombres para el comprobador**: las entidades `Reserva` (`id_reserva`, `fecha_reserva`) y `Linea_Reserva` (`numero_linea`, `cantidad`), y las relaciones `realiza`, `gestionada_en`, `contiene_lineas`, `reserva_de_edicion` y `valora` (`puntuacion`, `comentario`, `fecha`).

Escribe el texto BigER necesario para modelar lo indicado en estos párrafos y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
## Resolución del Enunciado ER 16 TravelFun

Los ejercicios a continuación están orientados a resolver el Enunciado ER 16TravelFun, disponible [aquí](../ejercicios/entidad-relacion/Enunciado%20ER%2016%20TravelFun.md).

Crea el archivo **ModeloTravelFun.erd** con **New Empty ER Model** y añade la línea `notation=crowsfoot` justo debajo de la línea `erdiagram`. Será donde vamos a ir modelando el Enunciado ER TravelFun.

Es otro **examen real**, así que, igual que con Todos Tus Libros, las pistas serán mínimas y cada ejercicio incluye solo los **nombres** que espera el comprobador.

---
### Ejercicio 06 - Enunciado ER 16 TravelFun  (1)

Vamos a comenzar centrándonos en resolver estos párrafos iniciales, que describen el catálogo de viajes:

---
TravelFun es una nueva plataforma de viajes organizados que nace con una misión clara: que cualquier persona pueda reservar desde el sofá de su casa una experiencia de viaje completa y sin sorpresas. Desde un viaje por la Normandía de la Segunda Guerra Mundial hasta otro por los Fiordos Noruegos, pasando por un viaje por la Toscana, TravelFun se encarga de todo y a nosotros nos piden que diseñemos su base de datos.

La plataforma ofrece un catálogo de viajes organizados. De cada viaje guardaremos un código único, su nombre comercial y una descripción. Cada viaje está estructurado en etapas que se identifican por un código único. De cada etapa guardaremos su nombre, descripción y duración en días. Una misma etapa puede formar parte de varios viajes distintos, por ejemplo la etapa "Roma: Coliseo y Vaticano" puede aparecer tanto en el Viaje por la Toscana como en el Viaje por Roma y Nápoles. Dentro de cada viaje las etapas siguen un orden concreto que hay que registrar. Cada etapa garantiza el alojamiento en una localidad concreta, del que solo es necesario saber el número de estrellas, teniendo en cuenta que las visitas de una etapa pueden realizarse en localidades distintas a la del alojamiento.

De las atracciones turísticas que se visitan en cada etapa guardaremos un código único, nombre y descripción. Una etapa puede visitar varias atracciones y una misma atracción puede aparecer en etapas de distintos viajes. Para facilitar el filtrado, las atracciones están clasificadas en tipos estandarizados (museo, monumento, parque temático, lugar histórico, catedral, etc.), con su código, nombre y descripción del tipo, teniendo en cuenta que una atracción puede pertenecer a varios tipos simultáneamente. Dentro de cada etapa las atracciones se visitan en un orden concreto que hay que registrar.

Para facilitar las búsquedas y filtrados, la plataforma mantiene información geográfica detallada. Las localidades pertenecen a países, y las atracciones turísticas están ubicadas en localidades concretas. Esto permite que un usuario pueda buscar, por ejemplo, todos los viajes que incluyen una visita al Coliseo de Roma o todos los que pasan por Italia.

---

De esta parte, pon especial atención a los atributos que no son de ninguna entidad: ¿de quién es el orden de una etapa dentro de un viaje? ¿Y el de una atracción dentro de una etapa? ¿Y el número de estrellas del alojamiento?

> **Nombres para el comprobador**: las entidades `Pais` (`codigo_pais`, `nombre`), `Localidad` (`codigo_localidad`, `nombre`), `Viaje_Organizado` (`codigo_viaje`, `nombre`, `descripcion`), `Etapa` (`codigo_etapa`, `nombre`, `descripcion`, `duracion_dias`), `Atraccion` (`codigo_atraccion`, `nombre`, `descripcion`) y `Tipo_Atraccion` (`codigo_tipo`, `nombre`, `descripcion`), y las relaciones `situada_en`, `incluye_etapa` (`orden_etapa`), `se_aloja_en` (`numero_estrellas`), `es_de_tipo`, `ubicada_en` y `visita` (`orden_visita`).

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 07 - Enunciado ER 16 TravelFun  (2)

Vamos a continuar con el siguiente párrafo:

---
Los viajes organizados se ofertan en distintas ediciones concretas a lo largo del año. Cada edición se identifica por su número dentro del viaje al que pertenece (la edición 1 del viaje A es distinta a la edición 1 del viaje B), almacenando también su fecha de inicio, fecha de fin, precio, estado y el porcentaje del plan amigo que explicaremos más adelante. Antes de que salga cada edición, se cierra definitivamente el equipo de guías que la acompañará durante todo el recorrido, sin posibilidad de cambios una vez iniciado el viaje. Cada edición es guiada por uno o varios guías profesionales. De cada guía guardaremos un identificador único, nombre, dos apellidos, teléfono y email único.

---

De esta parte, pon especial atención a cómo se identifica cada edición. Y lee con atención la frase sobre el equipo de guías: ¿hace falta guardar cómo ha ido cambiando a lo largo del tiempo?

> **Nombres para el comprobador**: las entidades `Edicion_Viaje` (`numero_edicion`, `fecha_inicio`, `fecha_fin`, `precio`, `estado`, `porcentaje_plan_amigo`) y `Guia` (`id_guia`, `email`, `nombre`, `apellido_1`, `apellido_2`, `telefono`), y las relaciones `es_edicion_de` y `guiada_por`.

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 08 - Enunciado ER 16 TravelFun  (3)

Vamos a continuar con los párrafos que describen a los clientes y el Plan Amigo:

---
Los clientes se registran en la plataforma con un identificador único, nombre, dos apellidos, DNI único, email único, teléfono y su saldo acumulado del plan amigo. Entre los clientes distinguimos dos papeles. Los viajeros son los clientes que participan físicamente en alguna edición de algún viaje, y de ellos, por exigencia de los alojamientos, debemos guardar también su fecha de nacimiento y su nacionalidad (uno de los países registrados en la plataforma). Los compradores son los clientes que han pagado alguna reserva, y de ellos guardamos además su dirección de facturación. Un mismo cliente puede desempeñar los dos papeles, como quien paga su propio viaje, o solo uno de ellos: un padre puede pagar el viaje de sus hijos sin viajar él mismo, y sus hijos serán viajeros sin haber pagado nada. También puede haber clientes recién registrados que todavía no desempeñen ninguno de los dos.

TravelFun dispone de un sistema de recomendación llamado Plan Amigo: un cliente puede haber recomendado la plataforma a uno o varios clientes nuevos, y cada cliente puede haber llegado recomendado, como mucho, por otro cliente. Queremos saber quién recomendó a quién. A partir de ese momento, cada vez que cualquiera de los dos realice una reserva, ambos acumulan un saldo equivalente a un porcentaje del importe de esa reserva, porcentaje que varía para cada edición concreta del viaje (la visita a las playas de Normandía de 2027 puede generar un 3% mientras que ese mismo viaje en 2028 solo un 1%). Ese saldo acumulado puede usarse como descuento en futuras reservas.

---

De esta parte, pon especial atención a las restricciones de la jerarquía y a dónde va cada cosa: ¿quién puede recomendar a quién? ¿Quién acumula saldo? ¿Es algo de un papel concreto o de cualquier cliente?

> **Nombres para el comprobador**: las entidades `Cliente` (`id_cliente`, `dni`, `email`, `nombre`, `apellido_1`, `apellido_2`, `telefono`, `saldo_acumulado`), `Viajero` (`fecha_nacimiento`) y `Comprador` (`direccion_facturacion`), y las relaciones `recomienda`, con los roles `Recomendado` y `Recomendador`, y `tiene_nacionalidad`.

Escribe el texto BigER necesario para modelar todas estas entidades y relaciones y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 09 - Enunciado ER 16 TravelFun  (4)

Vamos a finalizar con el último párrafo:

---
Cuando se realiza una reserva se genera un localizador alfanumérico único, almacenando también la fecha de reserva y el precio total pagado. Siempre hay un comprador responsable del pago de la reserva completa, independientemente de si él mismo viaja o no. Cuando se da de alta una edición, se crean en la plataforma todas sus plazas (si una edición tiene 60 plazas, se crean las 60), que se identifican por su número dentro de la edición a la que pertenecen. Cada reserva es siempre para una única edición concreta del viaje y agrupa una o varias plazas de esa edición que estaban libres. Al reservar, el comprador asigna cada plaza al viajero que la ocupará, y en la plaza se guardan las peticiones especiales de ese viajero (como menores que viajan sin acompañante o personas que requieren asistencia por movilidad reducida). Mientras una plaza está libre, no tiene ni reserva ni viajero. No es necesario representar en el diagrama la restricción de que todas las plazas de una reserva sean de la misma edición, ni la de que una plaza tenga viajero solo cuando está reservada.

---

Esta parte, tras haber llegado hasta aquí, seguro que puedes hacerla sin ninguna pista. Solo un consejo: lee con atención qué ocurre con una plaza mientras está libre.

> **Nombres para el comprobador**: las entidades `Reserva` (`localizador`, `fecha_reserva`, `precio_total`) y `Plaza` (`numero_plaza`, `peticiones_especiales`), y las relaciones `realiza`, `tiene_plaza`, `contiene` y `asignada_a`.

Escribe el texto BigER necesario para modelar lo indicado en ese párrafo y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.


---
### Fin

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el  Enunciado ER 14 Todos Tus Libros y el Enunciado ER 16 TravelFun.

![](../imgs/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---
