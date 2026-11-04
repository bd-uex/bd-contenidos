# Uso de Jerarquías

## Objetivos

1.- Familiarizarse con la **sintaxis** de las jerarquías en la notación BigER.

2.- Modelar **jerarquías de especialización y generalización**, decidiendo qué subclases están justificadas y qué restricciones de completitud y disyunción tiene cada jerarquía.

3.- Aplicar la notación para resolver el **Enunciado ER 04 The Expanse Completo**.

---
## Contenidos

### Antes de empezar

### Notación BigER

- 5.- Jerarquías de especialización y generalización

### Ejercicios de jerarquías: la Universidad

### Resolución del Enunciado ER 04 The Expanse Completo

---
## Antes de empezar

En este laboratorio vamos a crear tres archivos nuevos, todos con **New Empty ER Model** y con la línea `notation=crowsfoot` justo debajo de la línea `erdiagram`:

1. **EjemplosEERBigER.erd**, para los ejemplos de la notación.
2. **ModeloEERUniversidad.erd**, para los ejercicios de jerarquías.
3. **ModeloTheExpanseCompleto.erd**, para el enunciado de The Expanse completo.

También necesitarás el archivo **ModeloTheExpanseV1Simplificado.erd** del laboratorio anterior.

Recuerda que la vista del diagrama se abre con **Ctrl+Alt+E** (**Cmd+Alt+E** en macOS), y que **Ctrl+Alt+F** (**Cmd+Alt+F**) la ajusta a la pantalla. Los diagramas con jerarquías suelen ser grandes, así que este segundo atajo te será útil.


---
## Introducción a la notación textual de BigER (5) Jerarquías de especialización y generalización

Dentro de una entidad general, la **superclase**, puede haber subgrupos con características propias: las **subclases**. Entre ellas existe una relación "ES UN": un ingeniero **es un** empleado.

Una subclase hereda **todos los atributos** de su superclase, incluida la clave, y **todas sus relaciones**. Por eso, solo está justificado crear una subclase cuando aporta **algo propio**:

- **Atributos específicos** que no tiene el resto de la superclase. Por ejemplo, el atributo `tipo_ingeniero` de la subclase `Ingeniero`, que es una especialización de la superclase `Empleado`.
- **Relaciones específicas** en las que no participa el resto de la superclase. Por ejemplo, la relación `afiliado_a` con `Sindicato` de la subclase `Empleado_Tiempo_Parcial`, que también es una especialización de `Empleado`.

Una subclase que no aporta ni atributos ni relaciones propias está **vacía**, y crearla es el fallo **L17**: esa distinción cabe en un simple atributo de la superclase.

Cada **jerarquía** agrupa las subclases que se obtienen con un mismo **criterio** de clasificación (por puesto, por jornada...) y tiene dos restricciones:

| Restricción | Pregunta | En BigER | Significado |
|---|---|---|---|
| **Completitud** | ¿Toda instancia de la superclase debe estar en alguna subclase? | `total` | Toda instancia está al menos en una subclase |
| | | `partial` | Puede haber instancias que no estén en ninguna |
| **Disyunción** | ¿Puede una instancia estar en varias subclases? | `disjoint` | Como máximo en una |
| | | `overlapping` | Puede estar en varias a la vez |

La notación utilizada en BigER tiene dos partes:

```erdiagram
hierarchy nombre_jerarquia Superclase completitud disyuncion

entity Subclase extends nombre_jerarquia {
    clave_de_la_superclase key
    atributo_1_de_la_subclase
    ...
    atributo_n_de_la_subclase
}
```

Donde:

- Palabra reservada `hierarchy`, que declara la jerarquía **una sola vez**, con su nombre, su superclase y sus restricciones.
- **nombre_jerarquia**: se nombra por el **criterio** de clasificación, en minúsculas: `por_puesto`, `por_jornada`, `por_tipo_vehiculo`.
- **completitud**: `total` o `partial`. **disyuncion**: `disjoint` u `overlapping`.
- Palabra reservada `extends`, que indica que la subclase pertenece a la jerarquía. Ojo: `extends` va seguido del **nombre de la jerarquía**, no del de la superclase.
- **clave_de_la_superclase**: cada subclase repite la clave de la superclase con `key`, y debe ser **la misma** (fallo **M8** si no lo es). Es el **único** atributo de la superclase que se repite: repetir cualquier otro es el fallo **M9**.
- Resto de atributos **propios** de la subclase.

> [!warning] Tres detalles importantes
> - Si la jerarquía tiene **una sola subclase**, solo cabe `partial`, sin restricción de disyunción: no hay nada con lo que solaparse.
> - Una misma superclase puede tener **varias jerarquías**, una por cada criterio. Por eso es importante nombrarlas por su criterio.
> - Una entidad solo puede tener **un** `extends`: BigER no admite herencia múltiple. Sí admite jerarquías **multinivel**: una subclase puede ser a su vez superclase de otra jerarquía.

Vamos a verlo con el ejemplo del `Empleado` del Tema 5. Abre el archivo EjemplosEERBigER.erd.

**1.-** En primer lugar, la superclase `Empleado`, de la que van a heredar todas las demás:

```erdiagram
entity Empleado {
    dni key
    nombre
    fecha_nacimiento
    direccion
    telefono
}
```

**2.-** En segundo lugar, una primera jerarquía de `Empleado` **por el tipo de puesto**, con tres subclases que tienen cada una un atributo propio. Un empleado tiene como mucho un puesto de estos tres (`disjoint`), y puede tener otro puesto que no nos interesa modelar (`partial`):

```erdiagram
hierarchy por_puesto Empleado partial disjoint

entity Administrativo extends por_puesto {
    dni key
    categoria
}

entity Tecnico extends por_puesto {
    dni key
    nivel
}

entity Ingeniero extends por_puesto {
    dni key
    tipo_ingeniero
}
```

Tras escribir ese texto, deberías ver algo similar a lo que se muestra en la siguiente pantalla:

![](../imgs/BD%20-%20BigER%20Jerarquia%20Empleado%20por%20puesto.png)

Fíjate en cómo se dibuja: cada subclase apunta con una flecha a una caja con las restricciones de la jerarquía, y esa caja apunta a la superclase.

**3.-** En tercer lugar, una segunda jerarquía **por responsabilidad**, con una sola subclase, `Gerente`. No tiene atributos propios, pero está justificada porque participa en una relación propia: solo los gerentes dirigen proyectos. Al tener una sola subclase, la jerarquía solo puede ser `partial`:

```erdiagram
hierarchy por_responsabilidad Empleado partial

entity Gerente extends por_responsabilidad {
    dni key
}

entity Proyecto {
    codigo_proyecto key
}

relationship dirige {
    Gerente[1..1] -> Proyecto[1..N]
}
```

Tras escribir ese texto, deberías ver algo similar a lo que se muestra en la siguiente pantalla:

![](../imgs/BD%20-%20BigER%20Jerarquia%20Empleado%20por%20puesto%20y%20responsabilidad.png)

**4.-** En cuarto lugar, una tercera jerarquía **por tipo de jornada**, con dos subclases que tienen cada una un atributo propio. Todo empleado tiene una jornada (`total`), y solo una (`disjoint`). Además, una de las dos subclases participa en una relación que no tiene el resto de empleados:

```erdiagram
hierarchy por_jornada Empleado total disjoint

entity Tiempo_Completo extends por_jornada {
    dni key
    salario
}

entity Tiempo_Parcial extends por_jornada {
    dni key
    horas_semanales
}

entity Sindicato {
    codigo_sindicato key
}

relationship afiliado_a {
    Tiempo_Parcial[1..N] -> Sindicato[0..1]
}
```

Tras escribir ese texto, deberías ver algo similar a lo que se muestra en la siguiente pantalla:

![](../imgs/BD%20-%20BigER%20Jerarquia%20Empleado.png)

Fíjate en que `afiliado_a` se conecta a `Tiempo_Parcial` y no a `Empleado`: conectar una relación específica de una subclase a la superclase es el fallo **L19**.

---
## Ejercicios de jerarquías: la Universidad

En los siguientes ejercicios vamos a modelar la jerarquía de la Universidad que se ve en la sección 7.7.A del Tema 5. Abre el archivo **ModeloEERUniversidad.erd**.

![](../imgs/BD%20-%20BigER%20Jerarquia%20Universidad.png)

> [!note] Sobre los atributos de este ejemplo
> En estos ejercicios nos vamos a centrar en la **estructura** de las jerarquías: superclases, subclases, restricciones y niveles. Por eso las subclases solo tendrán su clave. En un diseño real, cada una de ellas tendría que aportar atributos o relaciones propias para estar justificada, como hemos visto. Por ejemplo, la división de los estudiantes por edad solo tendría sentido si los menores tuvieran algún dato propio, como la relación con su tutor legal.

---
### Ejercicio 01 - Jerarquías (1)

En primer lugar, escribe la entidad **Persona_UEX**, que va a ser la raíz de toda la jerarquía:

```erdiagram
entity Persona_UEX {
    nif key
}
```

A continuación, escribe la primera jerarquía de Persona_UEX, cuyas subclases son **Empleado** y **Estudiante**. La llamaremos `por_tipo_relacion` y decide sus restricciones a partir de la imagen.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 02 - Jerarquías (2)

Seguimos con el archivo ModeloEERUniversidad.erd. Añade lo siguiente a lo que ya llevas:

```erdiagram
hierarchy por_tipo_empleado Empleado total overlapping

entity Docente extends por_tipo_empleado {
    nif key
}

entity PTGAS extends por_tipo_empleado {
    nif key
}
```

A continuación, escribe la jerarquía `por_tipo_docente` para la especialización de **Docente** en **Catedratico** y **Titular**. Antes de decidir las restricciones, ten en cuenta que existen más tipos de docente que no van a aparecer en nuestra jerarquía (Sustituto, Ayudante...).

Fíjate en que es una jerarquía **multinivel**: `Docente` es subclase de `Empleado`, que a su vez es subclase de `Persona_UEX`.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---

### Ejercicio 03 - Jerarquías (3)

Seguimos con el archivo ModeloEERUniversidad.erd.

En primer lugar, añade la especialización de **Estudiante** en **Estudiante_Politecnica**. Como no vamos a modelar a los estudiantes del resto de centros, la jerarquía tiene una sola subclase:

```erdiagram
hierarchy por_centro Estudiante partial

entity Estudiante_Politecnica extends por_centro {
    nif key
}
```

A continuación, añade una primera jerarquía de Estudiante_Politecnica por edad:

```erdiagram
hierarchy por_edad Estudiante_Politecnica total disjoint

entity Mayor_De_Edad extends por_edad {
    nif key
}

entity Menor_De_Edad extends por_edad {
    nif key
}
```

Por último, escribe una segunda jerarquía de **Estudiante_Politecnica**, por titulación, con las subclases **Estudiante_Informatica**, **Estudiante_Civil** y **Estudiante_Edificacion**. Antes de decidir las restricciones, ten en cuenta que puede haber estudiantes de varias titulaciones a la vez, y que en la Politécnica hay más titulaciones que estas tres.

Fíjate en que `Estudiante_Politecnica` tiene **dos jerarquías** distintas, cada una con su criterio y sus restricciones.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---

## Resolución del Enunciado ER 04 The Expanse Completo

Los ejercicios a continuación están orientados a resolver el Enunciado ER 04 The Expanse Completo disponible [aquí](../ejercicios/entidad-relacion/Enunciado%20ER%2004%20The%20Expanse%20Completo.md)

---
### Ejercicio 04 - Enunciado ER 04 The Expanse Completo  (1)

Abre el archivo **ModeloTheExpanseCompleto.erd**, que será donde vamos a ir modelando el Enunciado ER 04 The Expanse Completo.

Para empezar, copia en este archivo el contenido del archivo **ModeloTheExpanseV1Simplificado.erd** que hiciste en el laboratorio anterior, ya que partiremos de él.

Antes de seguir, puedes comprobar que partimos del modelo ER correcto copiando todo el contenido a continuación:

---
### Ejercicio 05 - Enunciado ER 04 The Expanse Completo  (2)

Vamos a comenzar centrándonos en el primer párrafo que contiene cambios con respecto al enunciado simplificado:

---
Por supuesto, en este futuro brillante (o polvoriento, dependiendo del punto de vista), la extracción de **minerales** es lo más importante. Cada mineral tiene un código, un nombre y una descripción física. _Por otra parte, existen **distintas clases de minerales** (silicatos, sulfatos, nitratos, etc.) según su composición química. Esta clasificación debe estar estandarizada. Un mineral sólo pertenece a una de estas clasificaciones, aunque, como es obvio, varios minerales pueden ser de la misma clase. Además, hay minerales que están relacionados con otros, en el sentido de que si se encuentra alguno de ellos, es bastante probable encontrar los otros en algún lugar cercano. Es importante mantener la información de qué minerales están relacionados con otros._

---

En primer lugar, tenemos una nueva entidad fuerte:

- **Clase_Mineral**, con un código de clase de mineral (candidato idóneo a ser clave primaria) y un nombre. Fíjate en que el enunciado pide que la clasificación esté **estandarizada**: por eso es una entidad, y no un simple atributo de Mineral.

Tenemos que modelar también las relaciones contenidas en el párrafo:

- En primer lugar, la relación **agrupa_a** entre Mineral y Clase_Mineral. Por lo que nos dice el enunciado, un Mineral pertenece a una sola clase, mientras que una Clase_Mineral que guardemos en nuestra base de datos siempre tendrá como mínimo un Mineral.
- En segundo lugar, la relación recursiva **relacionado_con** de Mineral consigo mismo. Por lo que dice el enunciado, un Mineral puede estar relacionado con ninguno, uno o varios minerales. Como es una relación recursiva, debemos poner un rol en cada extremo; pero como los dos extremos tienen la misma cardinalidad y significan lo mismo, los roles son intercambiables, así que vamos a llamarlos simplemente "Mineral_A" y "Mineral_B".

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---
### Ejercicio 06 - Enunciado ER 04 The Expanse Completo  (3)

Vamos a continuar con el siguiente párrafo que contiene cambios con respecto al enunciado simplificado:

---
_La OPA no solo cuenta con naves espaciales, también dispone en cada planeta de vehículos de distinto tipo: vehículos para transporte de personal por la superficie de un planeta, vehículos específicos para la extracción de minerales, etc. Al igual que de las naves espaciales, cada vehículo está identificado por su matrícula, y se almacenan detalles sobre su modelo y también es clave saber en qué planeta está asignado. Todo lo relativo a la gestión de las operaciones y los transportes que se realizan con el resto de los tipos de vehículos no se incluirá de momento en la base de datos._

---

En primer lugar, nos hablan de los vehículos, y podemos ver que hay muchas similitudes entre Vehiculo y Nave_Espacial:

- Los dos tienen matrícula y modelo.
- Los dos están asignados a un Planeta.

También podemos ver algunas diferencias:

- Nave_Espacial tiene el atributo capacidad, que no tiene un vehículo cualquiera.
- Nave_Espacial es el único tipo de vehículo que realiza Vuelos.

Todo esto son pistas de que **Vehiculo es una generalización de Nave_Espacial**: tiene un subconjunto de sus atributos y de sus relaciones. Y como los demás tipos de vehículos (de transporte de personal, de extracción...) no tienen ningún dato ni relación propios, no crearemos subclases para ellos: serían subclases vacías.

Por tanto, debemos modificar el diagrama para:

- Añadir la nueva entidad fuerte **Vehiculo**, con `matricula` como clave primaria y `modelo`.
- Declarar la jerarquía **por_tipo_vehiculo** de Vehiculo. Como Nave_Espacial es su **única** subclase, ya sabes qué restricción de completitud tiene, y que no lleva restricción de disyunción.
- Modificar la entidad **Nave_Espacial**:
    - Pasa a extender la jerarquía `por_tipo_vehiculo`.
    - Como atributos, solo mantiene la clave primaria (`matricula`), que comparte con Vehiculo, y `capacidad`, que es exclusiva suya. El atributo `modelo` pasa a estar solo en Vehiculo: Nave_Espacial lo hereda.
- **Eliminar** la relación `asignada_a` entre Nave_Espacial y Planeta, y crear en su lugar la relación **asignado_a** entre Vehiculo y Planeta, con las mismas cardinalidades. Nave_Espacial hereda esta relación al extender a Vehiculo, y mantenerla también en la subclase sería redundante.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---

### Ejercicio 07 - Enunciado ER 04 The Expanse Completo  (4)

Vamos a continuar con el siguiente párrafo que contiene cambios con respecto al enunciado simplificado:

---
_Como otra gran novedad, la OPA, siempre buscando mantener el orden en sus operaciones por todo el sistema solar, también necesita llevar un control detallado del **personal** que trabaja en sus centros mineros. Así, cada persona de la OPA está identificada por un número único de personal y se registran datos como su nombre completo, pasaporte (ya sea de la Tierra, Marte o algún otro), la fecha en la que se unió a la OPA, el centro minero al que está asignado, y su puesto (que puede ser desde directivo, astronauta, técnico de extracción, o hasta incluso uno de esos conductores que maniobran cargueros por los túneles de las minas). Además, cada uno de los **centros** debe tener un coordinador. Este coordinador, por supuesto, solo puede ser alguien con un puesto de directivo de la OPA, y solo puede estar al mando de un centro a la vez. La OPA necesita saber, y con precisión, desde cuándo este directivo está a cargo del centro y nos dicen que no es necesario saber qué Directivos pudo tener anteriormente un Centro. También es necesario saber qué astronautas fueron los afortunados en pilotar los vuelos teniendo en cuenta que varios astronautas pueden pilotar un vuelo._

---

En primer lugar, parece claro que vamos a tener una nueva entidad fuerte para representar al personal:

- **Personal**, con un número de personal (candidato idóneo a ser clave primaria), pasaporte (también candidato a ser clave primaria, pero lo dejaremos como **clave alternativa**), nombre completo (como no nos dan más detalles, lo dejaremos como un único atributo), fecha de alta y puesto.

Asociada a esta nueva entidad tendremos la relación **trabaja_en**, para almacenar en qué Centro trabaja cada miembro del Personal. Por lo que nos dice el enunciado, una persona trabaja en un solo Centro, mientras que un Centro que guardemos en nuestra base de datos siempre tendrá como mínimo una persona trabajando en él.

Después, nos indican que todos los centros deben tener un coordinador que solo puede ser alguien con un puesto de directivo, y que es necesario saber qué astronautas pilotan los vuelos. Es decir, hay **dos partes** del Personal que tienen relaciones que no tiene el resto:

- Los **directivos**, que coordinan centros.
- Los **astronautas**, que pilotan vuelos.

Las dos subclases salen del **mismo criterio** de clasificación, el puesto, así que pertenecen a **una sola jerarquía**, que llamaremos `por_tipo_personal`. Para decidir sus restricciones, piensa si una persona puede tener dos puestos a la vez, y si hay puestos (técnico de extracción, conductor...) que no tienen subclase.

Las dos subclases solo se distinguen del resto del Personal por sus relaciones:

- **Directivo**, con la relación **coordina** con Centro. Además, necesitamos almacenar en la relación la fecha de inicio de la coordinación. Las cardinalidades se deducen del enunciado: todo centro tiene un coordinador, y un directivo solo puede estar al mando de un centro a la vez.
- **Astronauta**, con la relación **pilota** con Vuelo. Por lo que nos dice el enunciado, un Vuelo puede tener varios astronautas pilotándolo, y siempre tendrá al menos uno, mientras que un Astronauta puede no haber pilotado todavía ningún vuelo.

Escribe el texto BigER necesario para modelar todo esto y visualiza el resultado en el diagrama en VS Code. Antes de continuar, asegúrate de entender cómo se está representando gráficamente todo.

---

¡Enhorabuena! Has modelado correctamente el diagrama Entidad Relación para el enunciado Enunciado 04 ER The Expanse Completo.

![](../imgs/BD%20-%20ERD%20resuelto%20por%20Bender.png)

Imagen generada por IA (Gemini Pro 2.5)

---



