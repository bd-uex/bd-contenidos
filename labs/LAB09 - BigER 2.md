# Uso de Entidades Débiles

## Objetivos

1.- Modelar **entidades débiles**, comprendiendo cuándo una entidad no puede identificarse por sí misma y cómo se usan las **claves parciales** y las **relaciones débiles**.

2.- Revisar la primera versión del **Enunciado ER 00A Estudio Videojuegos** del laboratorio anterior, aplicando las entidades débiles a los dos problemas que dejamos abiertos.

3.- Aplicar la notación para resolver el **Enunciado ER 02 The Expanse Simplificado**.

---
## Contenidos

### Antes de empezar

### Notación BigER

- 4.- Entidades débiles y relaciones débiles

### Revisión del Enunciado ER 00A Estudio Videojuegos (segunda versión)

### Resolución del Enunciado ER 02 The Expanse Simplificado

---
## Antes de empezar

En este laboratorio vamos a usar los archivos que creaste en el laboratorio anterior:

1. **EjemplosNotacionBigER.erd**, donde seguiremos añadiendo los ejemplos de la notación.
2. **ModeloEstudioVideojuegos.erd**, con la primera versión del Enunciado ER 00A Estudio Videojuegos. Haz una copia con el nombre **ModeloEstudioVideojuegosV2.erd** y trabaja sobre ella: así conservarás la primera versión para compararlas.

Además, crea un archivo nuevo, **ModeloTheExpanseV1Simplificado.erd**, igual que en el laboratorio 1: Archivo -> Nuevo Archivo -> **New Empty ER Model**, y añade la línea `notation=crowsfoot` justo debajo de la línea `erdiagram`.

Recuerda que la vista del diagrama se abre con **Ctrl+Alt+E** (**Cmd+Alt+E** en macOS).

---
## Introducción a la notación textual de BigER (4) Entidades débiles

Hasta ahora todas nuestras entidades se identificaban por sí mismas, con su propia clave primaria. Pero hay entidades que **no pueden identificarse solo con sus atributos**: son las **entidades débiles**.

Una entidad es débil cuando cumple **dos condiciones a la vez**:

1. **Dependencia de existencia**: no tiene sentido si desaparece la entidad de la que depende. Esta condición **sola no basta**: hay entidades fuertes que también dependen de otra para existir.
2. **Dependencia de identificación** (la decisiva): sus propios atributos no bastan para distinguir una instancia de todas las demás, y necesita "tomar prestada" la clave de la entidad de la que depende.

La entidad de la que depende se llama **entidad propietaria**, y es siempre una entidad fuerte. El atributo de la entidad débil que distingue a una instancia de sus "hermanas", las que dependen del mismo propietario, se llama **clave parcial**. La clave primaria completa de la entidad débil es la **clave del propietario más la clave parcial**.

La notación utilizada en BigER tiene dos partes.

**1.- La entidad débil**, que se declara con `weak entity` y marca su clave parcial con `partial-key`:

```erdiagram
weak entity Nombre_Entidad_Debil {
    clave_parcial partial-key
    atributo_1
    ...
    atributo_n
}
```

**2.- La relación débil**, que une la entidad débil con su propietaria y se declara con `weak relationship`:

```erdiagram
weak relationship relacion_debil {
    Entidad[1..1] -> Entidad_Debil[0..N]
}
```

Sus cardinalidades siguen siempre las mismas reglas:

- En el lado de la **entidad fuerte**, siempre `[1..1]`: una entidad débil tiene exactamente un propietario.
- En el lado de la **entidad débil**, `[0..N]` o `[1..N]`, según si el propietario puede tener ninguna o debe tener al menos una.

Como en cualquier relación, el orden de las dos entidades no cambia el significado. Y si una entidad débil depende de **más de una** entidad fuerte, necesita una relación débil con cada una de ellas.

![](../imgs/BD%20-%20BigER%20Relación%20debil%20Horizontal.png)

Vuelve al archivo EjemplosNotacionBigER.erd y añade lo siguiente:

```erdiagram
weak entity Contacto_Emergencia {
    nombre partial-key
    telefono
    parentesco
}

weak relationship tiene_contacto {
    Contacto_Emergencia[1..N] -> Estudiante[1..1]
}
```

Tras escribir ese texto, deberías poder ver en la ventana del diagrama E/R algo similar a lo que se muestra en la siguiente imagen:

![](../imgs/BD%20-%20BigER%20Relacion%20Debil%20Estudiante%20Contacto%20Emergencia.png)

Fíjate en cómo se representa todo en el diagrama:

- Tanto la entidad débil como la relación débil aparecen con **borde grueso**. Cuando dibujemos un diagrama a mano, podemos usar **doble borde** para que se distingan mejor.
- La clave parcial aparece subrayada de forma distinta, más grisácea, a una clave primaria, para recordar que por sí sola no identifica a la entidad.

Antes de continuar, piensa:

- ¿Puede existir un contacto de emergencia si se borra al estudiante?
- ¿Basta el nombre para distinguir un contacto de todos los demás de la base de datos? ¿Y para distinguirlo de los demás contactos **del mismo estudiante**? Piensa que dos estudiantes distintos pueden tener un contacto llamado "María García", pero un mismo estudiante nunca tiene dos contactos con el mismo nombre. 

---
## Revisión del Enunciado ER 00A Estudio Videojuegos (segunda versión)

Al final del laboratorio anterior dejamos dos preguntas abiertas sobre la primera versión del Enunciado ER 00A Estudio Videojuegos. Ahora que conocemos las entidades débiles, vamos a resolverlas. Abre el archivo **ModeloEstudioVideojuegosV2.erd**.

---

### Ejercicio 01 - Enunciado ER 00A Estudio Videojuegos (6)

La primera pregunta era si `numero` es una clave primaria correcta para **Nivel**. El enunciado decía:

---
La numeración de los niveles empieza en 1 en cada videojuego, así que el nivel 1 de un juego y el nivel 1 de otro son niveles distintos.

---

La respuesta es **no**: casi todos los videojuegos tienen un nivel 1, así que el número no distingue a un nivel de todos los demás de la base de datos. Solo lo distingue **entre los niveles de su mismo videojuego**. Comprobemos las dos condiciones:

- **Dependencia de existencia**: un nivel no tiene sentido sin el videojuego al que pertenece.
- **Dependencia de identificación**: para saber de qué nivel hablamos, necesitamos preguntar "¿el nivel 1 **de qué juego**?". Necesita la clave del videojuego.

Por tanto, **Nivel es una entidad débil** que depende de Videojuego, con `numero` como clave parcial. Su clave completa es el código del videojuego más el número del nivel.

Este es el **Caso 1** de entidades débiles del Tema 5: los niveles son, en el fondo, un atributo multivalor compuesto de Videojuego (un juego tiene varios niveles, y cada uno tiene número, nombre, dificultad y duración), igual que las ediciones de un curso.

Con todo esto, en nuestro diagrama ER tendremos que:

1.- Convertir **Nivel** en **entidad débil**, con `numero` como **clave parcial**.

2.- Convertir **forma_parte_de** en **relación débil**. Al ser una relación débil, el lado de Videojuego ya era `[1..1]`, como debe ser; la única duda es la cardinalidad del lado de Nivel, y sigue igual que antes: un videojuego puede no tener niveles todavía.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code.

---
### Ejercicio 02 - Enunciado ER 00A Estudio Videojuegos (7)

La segunda pregunta era si es correcto el atributo `sedes` en **Equipo**. El enunciado decía:

---
Un equipo trabaja desde una o varias **sedes**, de cada una de las cuales se guarda la ciudad y la dirección; eso sí, un equipo nunca tiene dos sedes en la misma ciudad.

---

Tampoco es correcto: `sedes` es un **atributo multivalor** (un equipo tiene varias) y además **compuesto** (de cada sede, la ciudad y la dirección). Como hemos visto en el Tema 5, un atributo multivalor **siempre** acaba convertido en una **entidad débil** (Caso 1).

- Cada sede es de un equipo: sin su equipo, no tiene sentido.
- La ciudad solo distingue a una sede **entre las de su equipo**: dos equipos distintos pueden tener sede en Madrid, pero un mismo equipo nunca tiene dos sedes en la misma ciudad. Por eso la ciudad es la **clave parcial**.

Con todo esto, en nuestro diagrama ER tendremos que:

1.- **Eliminar** el atributo `sedes` de Equipo.

2.- Añadir la **entidad débil** **Sede**, formada por los atributos:
- ciudad: clave parcial aportada por Sede para formar la clave primaria.
- direccion

3.- Añadir la **relación débil** **tiene_sede** entre Equipo y Sede. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Equipo sin ninguna Sede, y el enunciado nos dice que no: todo equipo trabaja desde **al menos una** sede.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Compáralo con la primera versión, que conservas en ModeloEstudioVideojuegos.erd: es exactamente la evolución que vimos en clase

---
## Resolución del Enunciado ER 02 The Expanse Simplificado

Los ejercicios a continuación están orientados a resolver el Enunciado ER 02 The Expanse Simplificado disponible [aquí](../ejercicios/entidad-relacion/Enunciado%20ER%2002%20The%20Expanse%20Simplificado.md)

---
### Ejercicio 03 - Enunciado ER 02 The Expanse Simplificado  (1)

Abre el archivo ModeloTheExpanseV1Simplificado.erd que será donde vamos a ir modelando el enunciado Enunciado ER 02 The Expanse Simplificado.

Vamos a comenzar centrándonos en los 3 primeros párrafos:

---
En un futuro no tan lejano, donde los humanos ya no solo sueñan con Marte, sino que habitan y explotan casi todos los rincones de nuestro sistema solar, la **OPA (Outer Planets Alliance)** ha decidido organizar el caos de sus explotaciones espaciales. Sabemos que, si hay algo que su responsable, Camina Drummer, odia más que la corrupción terrícola, es un mal control de recursos. Así que, en un intento por poner orden, la OPA te ha pedido que diseñes una base de datos para organizar los centros mineros repartidos por el sistema.

Camina Drummer nos ha pedido una versión simplificada de lo que necesitan y si le convence nuestro diseño inicial nos encargarán realizar la versión completa.

Los **centros mineros** de los **planetas** son la joya de la corona de la OPA. Cada uno tiene su propio código único, algo parecido a los tatuajes de identificación que usan los cinturonianos. Además, tienen nombre, fecha de creación y unas coordenadas exactas porque, bueno, no querrás perderte en una mina en algún asteroide perdido. Cada planeta puede tener más de un centro (sí, Marte está lleno de ellos), pero siempre hay uno que es el principal en cada planeta. De los planetas almacenamos un código del planeta, su nombre, nº de lunas, periodo de rotación (en horas) y órbita (en días).

---

Básicamente todos estos párrafos se pueden resumir en que tenemos dos entidades fuertes:
 - Planeta con un código único (candidato idóneo a ser Clave Primaria), un nombre, un número de lunas, un periodo de rotación en horas y órbita en días.
 - Centro: con un código único (candidato idóneo a ser Clave Primaria), nombre, fecha de creación y unas coordenadas exactas.

Con esta información ya podemos modelar las dos primeras entidades mencionadas, para ello, escribe el texto BigER necesario para modelar los Planetas y los Centros mineros de manera que nuestro diagrama ER tenga estas dos entidades tal y como se muestran en la siguiente imagen:

![](../imgs/BD%20-%20BigER%20Enunciado%202%20ER%20The%20Expanse%20v1%20simplificado%20Lab09%20parte%201.png)


---

### Ejercicio 04 - Enunciado ER 02 The Expanse Simplificado  (2)

Vamos a continuar centrándonos en los 3 siguientes párrafos:

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
	- Nave_Espacial: con una matrícula (candidato idóneo a ser Clave Primaria), modelo y capacidad.
	- Vuelo: con fecha de inicio del vuelo, hora de inicio del vuelo, fecha de fin del vuelo y hora del fin del vuelo.
		- Ten cuidado de no confundirte con esta entidad, los centros entre los que vuela y los cargamentos que transportan son relaciones de la entidad.

Pero, ¿qué ocurre con los Cargamentos? Tal y como nos dice el enunciado no tienen un atributo que permita actuar como Clave Primaria ya que ese código único es solo único para cada centro. Además, ¿qué pasaría con la información de los Cargamentos si desapareciera el Centro donde se almacena?

Del mismo modo, ¿qué ocurre con los Vuelos? Tal y como nos dice el enunciado no tienen un atributo que permita distinguir vuelos que despeguen y aterricen a la misma hora. Además, ¿qué pasaría con la información de los Vuelos si desapareciera la Nave Espacial que los realiza?

En resumen, Cargamento y Vuelo no son entidades fuertes y las modelaremos después, por ahora vamos a modelar las entidades fuertes de estos tres párrafos, es decir, Mineral y Nave_Espacial.

Escribe el texto BigER necesario para modelarlas de manera que nuestro diagrama ER tenga estas dos nuevas entidades tal y como se muestran en la siguiente imagen:

![](../imgs/BD%20-%20BigER%20Enunciado%202%20ER%20The%20Expanse%20v1%20simplificado%20Lab09%20parte%202.png)

---
### Ejercicio 05 - Enunciado ER 02 The Expanse Simplificado  (3)

Vamos a modelar entre las entidades que tenemos hasta ahora alguna de las relaciones que han ido apareciendo en los párrafos anteriores:
	- En primer lugar, vamos a modelar la relación **ubicado_en** entre Centros y Planetas. Por lo que nos dice el enunciado, podemos entender que un Centro solo puede estar en un Planeta mientras que un Planeta que guardemos en nuestra Base de Datos siempre tendrá, como mínimo un Centro en él.
	- En segundo lugar, vamos a modelar la relación **es_principal_en** entre Centro y Planeta. Por lo que dice el enunciado, un Planeta tendrá siempre un Centro como principal y solo uno, mientras que un Centro no tiene por qué ser principal.
	- En tercer lugar, la relación **encontrado_por** entre Centro y Mineral.  Por lo que nos dice el enunciado, puede que haya centros que nunca hallen un mineral y otros que hallen muchos, mientras que puede haber minerales que nunca sean hallados por ningún centro y otros que sean hallados por muchos. Además, de esta relación tenemos que guardar un atributo llamado fecha_hallazgo ya que no tiene sentido que ese atributo sea del Centro o del Mineral.
	- Por último, vamos a modelar la relación **asignada_a** entre Nave_Espacial y Planeta. Por lo que nos dice el enunciado, una instancia de Nave_Espacial solo puede estar asociada a un planeta mientras que una instancia de Planeta puede no tener ninguna Nave_Espacial o tener muchas.

Escribe el texto BigER necesario para modelarlas y visualiza el resultado en el diagrama en VS Code. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente cada relación.

---
### Ejercicio 06 - Enunciado ER 02 The Expanse Simplificado  (4)

Vamos a tratar de modelar la entidad Vuelo que dejamos sin modelar al modelar las entidades fuertes en los ejercicios previos.

Ya vimos que, tal y como nos dice el enunciado no tienen un atributo que permita distinguir vuelos que despeguen y aterricen a la misma hora. Además, ¿qué pasaría con la información de los Vuelos si desapareciera la Nave_Espacial que los realiza?

Todo esto nos tiene que llevar a modelar Vuelo como **Entidad débil** que va a depender de Nave_Espacial. ¿Cuál será la clave parcial que aportan los vuelos para, junto con la clave de la entidad fuerte Nave_Espacial, poder distinguir un vuelo de cualquier otro en la Base de Datos? Podríamos pensar que con la fecha de despegue del vuelo es suficiente pero, ¿qué pasa si una nave realiza más de un vuelo el mismo día? Con la fecha de despegue ya vemos que no es suficiente pero si además de la fecha de despegue, incluimos la hora del despegue ya sí tenemos los suficientes atributos para distinguir cualquier vuelo de otro.

Con todo esto, en nuestro diagrama ER tendremos que:

1.- Añadir la **Entidad débil** **Vuelo** que está formada por los atributos:
- fecha_inicio: clave parcial aportada por Vuelo que tenemos que combinar como clave parcial para formar la clave primaria.
- hora_inicio: clave parcial aportada por Vuelo que tenemos que combinar como clave parcial para formar la clave primaria.
- fecha_fin
- hora_fin

2.- Añadir la **relación débil** **realizado_por** entre Vuelo y Nave_Espacial. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD  Naves que todavía no hayan realizado vuelos y tiene sentido que sí tengamos dado que un Planeta cada vez que reciba una Nave nueva esta todavía no habrá volado.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---

### Ejercicio 07 - Enunciado ER 02 The Expanse Simplificado  (5)

Ahora que tenemos la entidad débil Vuelo, podemos acabar de modelar el resto de sus relaciones:
	- En primer lugar, vamos a modelar la relación **origen_de** entre Vuelo y Centro. Por lo que nos dice el enunciado, podemos entender que puede haber todavía algún Centro que no haya sido el origen de ningún Vuelo, pero un Vuelo siempre debe tener un Centro de Origen.
	- En segundo lugar, vamos a modelar la relación **destino_de** entre Vuelo y Centro. Por lo que nos dice el enunciado, podemos entender que puede haber todavía algún Centro que no haya sido destino de ningún Vuelo, pero un Vuelo siempre debe tener un Centro de Destino (vamos a suponer que un Vuelo no tiene ningún incidente que le haya impedido alcanzar su destino).

Escribe el texto BigER necesario para modelarlas y visualiza el resultado en el diagrama en VS Code. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente cada relación.

---

### Ejercicio 08 - Enunciado ER 02 The Expanse Simplificado  (6)

Vamos ahora a tratar de modelar la entidad Cargamento que dejamos sin modelar al modelar las entidades fuertes en los ejercicios previos.

Ya vimos que, tal y como nos dice el enunciado, un Cargamento no tiene un atributo que permita distinguir entre los distintos cargamentos de los distintos centros ya que ese código interno único es solo único para cada centro. Además, ¿qué pasaría con la información de los Cargamentos si desapareciera el Centro donde se almacena?

Todo esto nos tiene que llevar a modelar Cargamento como **Entidad débil** que va a depender de Centro. ¿Cuál será la clave parcial que aportan los cargamentos para, junto con la clave de la entidad fuerte Centro, poder distinguir un cargamento de cualquier otro en la Base de Datos? En este caso con el código único interno para cada centro ya sí sería suficiente.

Con todo esto, en nuestro diagrama ER tendremos que:

1.- Añadir la **Entidad débil** **Cargamento** que está formada por los atributos:
- codigo: clave parcial aportada por Cargamento para formar la clave primaria.
- fecha_empaquetado

2.- Añadir la **relación débil** **empaquetado_en** entre Cargamento y Centro. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Centro que todavía no haya tenido un Cargamento y tiene sentido que sí dado que un Centro puede estar recién construido sin haber encontrado ni extraído ningún mineral.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Ejercicio 09 - Enunciado ER 02 The Expanse Simplificado  (7)

Ahora que tenemos la entidad débil Cargamento, podemos acabar de modelar el resto de sus relaciones:
	- En primer lugar, vamos a modelar la relación **forma_parte_de** entre Cargamento y Mineral. Por lo que nos dice el enunciado, puede que un mineral todavía no haya formado parte de ningún cargamento y otros que estén en muchos. Además, esta relación tiene un atributo llamado cantidad.
	- En segundo lugar, vamos a modelar la relación **transportado_en** entre Cargamento y Vuelo. Por lo que nos dice el enunciado, puede que haya cargamentos que todavía no hayan sido transportados pero los vuelos, al menos, transportarán un cargamento cada vez que se producen.

Escribe el texto BigER necesario para modelarlas y visualiza el resultado en el diagrama en VS Code. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente cada relación.

---
### Ejercicio 10 - Enunciado ER 02 The Expanse Simplificado (8)

Para terminar, nos falta modelar esta parte del cuarto párrafo del enunciado:

---
Por otra parte, debe mantenerse información de la cantidad extraída por día de cada mineral en cada centro (puede haber minerales para los que no se extraiga nada durante algún día o en algunos períodos de tiempo).

---

Esto nos dice que para cada centro y mineral extraído por ese centro, debemos saber la cantidad concreta extraída de cada uno cada día. Es decir, un mineral concreto (oro, platino, etc) puede ser extraído el mismo día por distintos centros (Titán1, Marte2, Saturno3, etc..) y un centro  (Titán1, Marte2, Saturno3, etc..) puede extraer distintos minerales  (oro, platino, etc) el mismo día. Es decir es una relación con cardinalidad máxima N en ambos lados. 

Además, podemos ver que esta relación entre Centro y Mineral no es estática, cambia con el tiempo. Si probamos a escribir ejemplos de esta relación, por ejemplo, el centro Titán1 extrae 10 kilos de platino el 25/11/2235, el centro Titán1 extrae 20 kilos de platino el 26/11/2235, el centro Titán1 extrae 15 kilos de platino el 27/11/2235, etc, vemos que el mismo par (Centro, Mineral) se puede repetir por lo que los atributos de la relación, fecha y cantidad, realmente serían multivaluado.

Este caso se corresponde con lo visto para Entidades Débiles en el apartado 5.4.2 "Caso 2: Para gestionar atributos multivaluados en una relación M:N con repetición de relaciones entre instancias" del Tema 5.

Si recordamos ese apartado, esto significa convertir la interacción a lo largo del tiempo en una **entidad débil con repetición** (o histórica). Esta nueva entidad guardará cada "evento" de la relación, y su clave primaria casi siempre incluirá un atributo de tiempo (como curso o fecha).

En nuestro ejemplo de un Centro extrae Mineral, vamos a convertir la relación en una entidad débil llamada **Historico_Extraccion**. Dado que en una fecha concreta, una instancia de Centro puede relacionarse con **varias** instancias de Mineral, y, varias instancias de Mineral estar relacionadas con **varias** de Centro, esta entidad depende tanto de Centro como de Mineral. 

¿Cuál será la clave parcial que aporta la relación para, junto con la clave de la entidad fuerte Centro y la clave de la entidad fuerte Mineral, poder distinguir una extracción de cualquier otra en la Base de Datos? En este caso usaremos la fecha ya que con ella es suficiente.

Con todo esto, en nuestro diagrama ER tendremos que:

1.- Añadir la **Entidad débil** **Historico_Extraccion** que está formada por los atributos:
- fecha: clave parcial aportada por la relación que permite distinguir la extracción de un día de Mineral por un Centro de la extracción de ese mismo mineral por ese mismo Centro cualquier otro día
- cantidad

2.- Añadir la **relación débil** **extraido_por** entre Historico_Extraccion y Centro. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Centro que todavía no haya extraído ningún Mineral y tiene sentido que sí dado que un Centro puede estar recién construido sin haber encontrado ni extraído ningún mineral.

3.- Añadir la **relación débil** **incluido_en** entre Historico_Extraccion y Mineral. Dado que es una relación débil, la única duda a resolver para fijar la cardinalidad es si puede haber en nuestra BD algún Mineral que todavía no haya estado incluido en ninguna extracción de ningún Centro y tiene sentido que sí.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar asegúrate de estar entendiendo cómo se está representando gráficamente todo.

---
### Fin

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para los enunciados Enunciado ER 00A Estudio Videojuegos y Enunciado ER 02 The Expanse v1 simplificado.

![](../imgs/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---

