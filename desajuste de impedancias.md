# Desajuste de impedancias

El término "impedance mismatch" (desajuste de impedancias) está tomado de la **ingeniería eléctrica**, y la analogía es bastante acertada.

## El origen eléctrico

En electrónica, la _impedancia_ mide cómo un circuito se opone al flujo de corriente alterna. Cuando conectas dos sistemas eléctricos con impedancias diferentes (por ejemplo, un amplificador de audio a un altavoz), se produce un **desajuste de impedancias**—lo que resulta en reflexión de señal, pérdida de potencia y distorsión. Los sistemas no transfieren energía eficientemente porque sus características fundamentales no están alineadas.

## La analogía en software

La metáfora se adoptó en ingeniería de software porque el modelo relacional y el paradigma orientado a objetos tienen "resistencias" fundamentalmente diferentes para representar y manipular datos:

|Modelo Relacional|Modelo Orientado a Objetos|
|---|---|
|Tablas, filas, columnas|Objetos con estado y comportamiento|
|Operaciones basadas en conjuntos|Navegación mediante referencias|
|Sin herencia|Jerarquías de clases|
|Identidad mediante claves|Identidad independiente del estado|
|Estructura plana|Grafos anidados y complejos|

Cuando intentas "conectar" estos dos mundos, surge fricción—igual que con impedancias desajustadas. Los datos no fluyen limpiamente entre ellos. Necesitas transformaciones, mapeos, y a menudo pierdes algo en la traducción (como la elegante semántica de conjuntos de SQL o la encapsulación de la POO).

## Por qué el término se consolidó

Es evocador: sugiere que ambos paradigmas son perfectamente válidos por sí solos, pero cuando intentas acoplarlos, hay una resistencia inherente. Los ORMs (Object-Relational Mappers) como Hibernate o SQLAlchemy son esencialmente "circuitos de adaptación de impedancia"—adaptadores que intentan minimizar la pérdida de señal entre ambos mundos, aunque nunca de forma perfecta.

El término se atribuye ampliamente a discusiones de los años 90, cuando la POO se volvió dominante y los desarrolladores luchaban por persistir objetos en bases de datos relacionales. Captura tanto la realidad técnica como la frustración de manera elegante.