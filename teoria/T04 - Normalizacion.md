# Diseño lógico de bases de datos relacionales

## Contenidos

-  Proceso de diseño de bases de datos
- Diseño lógico
	- Esquema lógico
	- Directrices de diseño para bases de datos relacionales
		- Semántica de los atributos de una relación
		- Información redundante en tuplas y anomalías de actualización
		- Valores nulos en tuplas
		- Tuplas espurias

- Normalización
	- Dependencias funcionales (DF)
	- Formas normales 
		- Primera forma normal (1FN)
		- Segunda forma normal (2FN)
		- Tercera forma normal (3FN)
		- Forma normal de Boyce-Codd (FNBC)

- Desnormalización 

---

## Descripción general del proceso de diseño de bases de datos

![[BD - diseno modelo relacional.png]]

---

## 1. Diseño lógico

El diseño lógico es el proceso de **construir un esquema de la información** que utiliza la empresa, **basándose en un modelo de base de datos específico e independiente del SGBD concreto** que se vaya a utilizar, así como de cualquier otra consideración física. El objetivo del diseño lógico es obtener una **representación que use, del modo más eficiente posible, los recursos que el modelo** de SGBD posee para estructurar los datos y para modelar las restricciones

### 1.1. Esquema lógico

En un esquema lógico se utiliza las **estructuras de datos del modelo de base de datos en el que se basa el SGBD** que se vaya a utilizar. El modelos de bases de datos más extendido es el modelo relacional.

El esquema lógico es una **fuente de información para el diseño físico**. Además, juega un papel importante durante la etapa de mantenimiento del sistema, ya que permite que los futuros cambios que se realicen sobre los programas de aplicación o sobre los datos, se representen correctamente en la base de datos.

Tanto el diseño conceptual, como el diseño lógico, son **procesos iterativos**, tienen un punto de inicio y se van refinando continuamente. Ambos se deben ver como un proceso de aprendizaje en el que el diseñador va comprendiendo el funcionamiento de la empresa y el significado de los datos que maneja. El diseño conceptual y el diseño lógico son etapas **clave para conseguir un sistema que funcione correctamente**. Si la base de datos no es una representación fiel de la empresa, será difícil, si no imposible, **definir todas las vistas de los usuarios** (los esquemas externos), o **mantener la integridad** de la misma. También puede ser difícil definir la **implementación física** o mantener unas **prestaciones aceptables** del sistema. Además, hay que tener en cuenta que la capacidad de ajustarse a futuros cambios es un sello que identifica a los buenos diseños de bases de datos. Por todo esto, es fundamental dedicar el tiempo y las energías necesarias para producir el mejor esquema posible.

El objetivo de esta etapa es obtener el esquema lógico, que estará formado por las relaciones de la base de datos en tercera forma normal. Una vez obtenidas las relaciones, se considerará la posibilidad de modificar el esquema de la base de datos para conseguir una mayor eficiencia.

Para cada relación del esquema lógico se debe especificar:
- Nombre y descripción de la información que almacena. Es conveniente indicar si corresponde a una entidad, una relación o un atributo.
- Para cada columna, indicar: nombre, tipo de datos (puede ser un tipo de SQL), si admite nulos, el valor por defecto (si lo tiene) y el rango de valores (mediante un predicado en SQL).
- Indicar la clave primaria y si se ha de generar automáticamente.
- Indicar las claves alternativas.
- Indicar las claves externas y sus reglas de comportamiento ante el borrado y la modificación de la clave primaria a la que referencian.
- Si alguna columna es un dato derivado (su valor se calcula a partir de otros datos de la base de datos) indicar cómo se obtiene su valor.
- Especificar las restricciones a nivel de fila de cada tabla, si las hay. Estas restricciones son aquellas que involucran a una o varias columnas dentro de una misma fila.
- Especificar otras restricciones no expresadas antes (serán aquellas que involucran a varias filas de una misma tabla o a filas de varias tablas a la vez).
- Especificar las reglas de negocio, que serán aquellas acciones que se deba llevar a cabo de forma automática como consecuencia de actualizaciones que se realicen sobre la base de datos.
- Introducir relaciones de referencia para establecer listas de valores para las columnas que las necesiten.

Una vez obtenido el esquema de la base de datos en tercera forma normal, y teniendo en cuenta los requisitos en cuanto a transacciones, volumen de datos y prestaciones deseadas, se puede realizar ciertos cambios que ayuden a conseguir una mayor eficiencia en el acceso a la base de datos:
- Introducir redundancias desnormalizando algunas tablas o añadiendo datos derivados.
- Partir tablas horizontalmente (por casos) o verticalmente (por columnas).

---
### 1.2. Directrices de diseño para bases de datos relacionales

Las medidas informales para obtener un buen diseño relacional se pueden resumir en:
- La semántica de los atributos.
- La reducción de información redundante en las tuplas. 
- La reducción de los valores NULL en las tuplas.
- La prohibición de la posibilidad de generar tuplas falsas. 
#### Semántica de los atributos

- **DIRECTRIZ 1**- De manera informal, **un esquema de relación se corresponde con un solo tipo de entidad**. 
	- Los **atributos de diferentes entidades** (EMPLEADOS, DEPARTAMENTOS, PROYECTOS) **no deben mezclarse en la misma relación**
	- **Solo las claves externas** hacen referencia a otras entidades
- En resumen: Diseñe un **esquema que pueda explicarse fácilmente relación por relación**. La semántica de los atributos debe ser fácil de interpretar.

Si una relación está compuesta por una mezcla de múltiples entidades, se producirá una ambigüedad semántica y la relación no podrá explicarse con claridad.

![[BD - semantica attrs.png]]

#### Información redundante en tuplas

Uno de los principales **objetivo** del diseño lógico en el modelo relacional es **reducir el espacio de almacenamiento** que ocupa nuestra base de datos. Por lo tanto, un buen diseño lógico debe **evitar almacenar información redundante**.

Además, almacenar información redundante provoca otros problemas dentro del modelo relacional, como son las:
- Anomalías de actualización
- Anomalías de inserción
- Anomalías de eliminación

![[BD - redundancia info.png]]

Consideremos la relación:
$EMP\_PROY$(<u>Dni, NumProyecto</u>, NombreE, NombreP, Horas)
##### Ejemplo de anomalía de actualización
Un ejemplo de la anomalía de actualización sería:
- Cambiar el nombre del proyecto número 1 de “ProyectoX” a “Computación” provoca que esta modificación se tenga que realizar para los 100 empleados que trabajan en el proyecto 1.
##### Ejemplo de anomalía de inserción
Un ejemplo de la anomalía de inserción sería:
- No se puede insertar un proyecto a menos que haya un empleado asignado a él.
Y del mismo modo:
- No se puede insertar un empleado a menos que esté asignado a un proyecto.
- Valores NULL en `Dni` o `NumProyecto` no son válidos
##### Ejemplo de anomalía de eliminación
Anomalía de eliminación:
- Cuando se elimina un proyecto, se eliminarán todos los empleados que trabajan en ese proyecto.
- Alternativamente, si un empleado es el único empleado en un proyecto, eliminar ese empleado resultaría en la eliminación del proyecto correspondiente.

**DIRECTRIZ 2**. Diseñe un esquema que no sufra anomalías de inserción, eliminación ni actualización en las relaciones. Si existen anomalías presentes, anótelas para que las aplicaciones que operan con la base de datos puedan tenerlas en cuenta. Una de las **maneras de evitar errores es representar un hecho solo una vez** en la base de datos.
#### Valores nulos en tuplas

Los valores nulos en las tuplas suponen un tratamiento específico de los mismos. Su uso desmedido puede causar alguno de los siguientes problemas:
- Desperdicio de espacio de almacenamiento
- Errores en su contabilización en funciones de agregación
- Comparaciones impredecibles en SELECT o JOIN

Los valores nulos pueden tener distintas interpretaciones en cada caso de uso:
- Atributo no aplicable o inválido
- Valor del atributo desconocido (puede existir)
- Se sabe que existe un valor, pero no está disponible

**DIRECTRIZ 3**. Las relaciones deben diseñarse para que sus **tuplas tengan el mínimo número de valores NULL**. Los **atributos que frecuentemente son NULL se pueden colocar en relaciones separadas**.

Si sólo el 10 por ciento de los empleados tienen oficinas individuales, 
	- no incluir atributo NúmeroOficina en relación EMPLEADO; 
	- crear una relación $OFICINAS\_EMPS$(DniEmpleado, NúmeroOficina) que incluya las tuplas de los empleados con oficinas individuales. 

#### Generación de tuplas espurias

>[!error] Evitar a toda costa!

 Un mal esquema de base de datos relacional **puede generar filas falsas (tuplas espurias)** al hacer un JOIN, y la propiedad que debe cumplir un esquema de base de datos para evitar esto se llama precisamente **lossless join** o **descomposición sin pérdida**.
##### El problema de las tuplas espurias

Cuando descompones una relación en varias tablas y luego intentas reconstruir la información original mediante JOINs, puedes obtener **más filas de las que había originalmente** (tuplas espurias/falsas), debido a la aparición de **combinaciones que nunca existieron** en los datos originales.

**Ejemplo ilustrativo**
Imagina la tabla original EMPLEADO_PROYECTO:

| Empleado | Proyecto | Gerente |
|----------|----------|---------|
| Ana      | X        | Luis    |
| Ana      | Y        | María   |
| Carlos   | X        | Luis    |
Y ahora realizamos una **descomposición incorrecta:**

- Tabla1: (Empleado, Proyecto)
- Tabla2: (Empleado, Gerente)

Al hacer el JOIN, obtendrías:

| Empleado | Proyecto | Gerente |            |
| -------- | -------- | ------- | ---------- |
| Ana      | X        | Luis    | ✓ original |
| Ana      | X        | María   | ✗ FALSA    |
| Ana      | Y        | Luis    | ✗ FALSA    |
| Ana      | Y        | María   | ✓ original |
| Carlos   | X        | Luis    | ✓ original |

**DIRECTRIZ 4**. Para garantizar una descomposición sin pérdida, se debe cumplir que el **atributo común** entre las tablas sea una **clave** (o superclave) en al menos una de ellas. Esto se verifica mediante las **dependencias funcionales** del esquema y es fundamental en el proceso de **normalización** (especialmente en 3FN y BCNF). Lo veremos más adelante.

---
## 2. Normalización

La normalización es una **técnica para diseñar la estructura lógica de los datos de un sistema de información en el modelo relacional**, desarrollada por E. F. Codd en 1972. Es una estrategia de diseño de abajo a arriba: se parte de los atributos y éstos se van agrupando en tablas según su afinidad. 

En la mayoría de las ocasiones, una base de datos completamente normalizada no proporciona la máxima eficiencia; sin embargo, el objetivo en esta etapa es conseguir una base de datos normalizada por las siguientes **razones**:
- Un esquema normalizado **organiza los datos de acuerdo a sus dependencias funcionales**, es decir, de acuerdo a sus relaciones lógicas.
- El esquema lógico n**o tiene por qué ser el esquema final**. Debe representar lo que el diseñador entiende sobre la naturaleza y el significado de los datos de la empresa. Si se establecen unos objetivos en cuanto a prestaciones, el diseño físico cambiará el esquema lógico de modo adecuado. Sin embargo, la **normalización obliga a entender completamente cada uno de los atributos** que se han de representar en la base de datos.
- Un esquema normalizado es **robusto y carece de redundancias**, por lo que está libre de ciertas anomalías que las redundancias pueden provocar cuando se actualiza la base de datos.
- Los equipos informáticos son cada vez más potentes, por lo que puede ser más razonable implementar **bases de datos fáciles de manejar** (las normalizadas), a costa de un tiempo adicional de proceso.
- La normalización produce bases de datos con **esquemas flexibles que pueden extenderse con facilidad**.

De lo que se trata es de obtener un conjunto de tablas que se encuentren en la
forma normal de Boyce-Codd. Para ello, hay que pasar por la primera, segunda
y tercera formas normales.
### 2.1. Dependencias funcionales

#### Definición

Uno de los **conceptos fundamentales** en la teoría de diseño de un esquema relacional, y por tanto en la normalización, es el de dependencia funcional. 

Una dependencia funcional significa que **si conozco el valor de un atributo, siempre puedo determinar el valor de otro**. La notación utilizada en la teoría relacional es una flecha entre los dos atributos, por ejemplo, $A \to B$, que se puede leer como "A determina B". Si conozco su número de empleado, puedo determinar su nombre; si conozco el número de una pieza, puedo determinar el peso y el color de la pieza; y así sucesivamente.

La dependencia funcional es una **noción semántica**. Si hay o no dependencias funcionales entre atributos, no lo determina una serie abstracta de reglas, sino, más bien, los modelos mentales del usuario y las reglas de negocio de la organización o empresa para la que se desarrolla el sistema de información. Las dependencias funcionales son **restricciones** que **formalizan la semántica de una relación** mediante una serie de propiedades que deben cumplir los atributos de su esquema.

De manera formal, dados dos conjuntos de atributos $X$ e $Y$, subconjuntos de un esquema $R$, diremos que $Y$ depende funcionalmente de $X$ o que $X$ implica o determina a $Y$, y lo denotamos como $X \to Y$, si para cualquier extensión $r(R)$ se cumple que para cada valor de $X$ existe un solo valor de $Y$. A $X$ se le denomina *determinante*.

 $X \to Y$  si y sólo si  $\forall t_1, t_2 \in r(R) / t_1[X] = t_2[X], t_1[Y] = t_2[Y]$

 $X \to Y$ en $R$ especifica una restricción en todas las instancias de relación $r(R)$

Escrito como $X \to Y$; se puede mostrar como un conjunto de dependencias.

**Ejemplo**. Por ejemplo, si consideramos la relación:
$EMP\_PROY$(<u>Dni, NumProyecto</u>, NombreE, NombreP, UbicacionProyecto, Horas)

Sus dependencias funcionales son:
- DF1: $DNI \to NombreE$
- DF2:  $NumProyecto \to {NombreProyecto, UbicacionProyecto}$
- DF3:  ${DNI, NumProyecto} \to HORAS$

Por lo tanto, para definir una relación en el modelo relacional es necesario indicar, además de su nombre y esquema, el conjunto de dependencias funcionales que satisface. De esta forma, un ejemplo de definición sería:

$EMP\_PROY$(<u>Dni, NumProyecto</u>, NombreE, NombreP, UbicacionProyecto, Horas)
DF = {DNI $\to$ NombreE, NumProyecto $\to$ {NombreProyecto, UbicacionProyecto}, 
{DNI, NumProyecto} $\to$ HORAS}

Finalmente, es necesario apuntar dos hechos que se desprenden de la definición de dependencias funcionales realizada:
- Si $K$ es una **clave candidata** de $R$, entonces **$K$ determina funcionalmente todos los atributos en $R$** nunca tenemos dos tuplas distintas con $t_1[K]=t_2[K]$
- $X \to Y$ en $R$ no supone que $Y \to X$ en $R$ 
#### Dependencia funcional total o parcial

Una dependencia funcional $X \to Y$ es una **dependencia funcional completa si la eliminación de cualquier atributo $A$ de $X$ implica que la dependencia deja de existir**; es decir, para cualquier atributo $A \in X, (X − \{A\})$ no determina funcionalmente a $Y$. 

Una dependencia funcional $X \to Y$ es una **dependencia parcial si se puede eliminar algún atributo**  $A \in X, (X − \{A\})$ y la dependencia **se mantiene**; es decir, para algún  $A \in X, (X − \{A\}) \to Y$.
#### Definición de dependencias funcionales a partir de instancias

Para poder definir las dependencias funcionales de una relación a partir de su estado, necesitamos comprender el significado de los atributos involucrados y las relaciones entre ellos.

Dado un estado de una relación, no se puede concluir con una certeza absoluta que hay una DF entre ciertos atributos; ya que, nuevos estados podrían hacer que no se cumpliese esa dependencia. Por lo tanto, todo lo que se puede concluir es que **puede existir una DF** entre ciertos atributos. Por otra parte, sí se puede concluir con certeza es que **ciertas DF no existen** porque hay tuplas que muestran una violación de esas dependencias

**Ejemplo: descarte de dependencias funcionales**

Consideremos el estado de la relación $IMPARTIR$, 

| Profesor | Curso                   | Texto    |
| -------- | ----------------------- | -------- |
| Smith    | Estructuras de datos    | Bartram  |
| Smith    | Administración de datos | Martin   |
| Hall     | Compiladores            | Hoffman  |
| Brown    | Estructuras de datos    | Horowitz |

podemos decir que:

- la $DF: Texto \to Curso$ puede existir. 

Sin embargo, las DF:
- $Profesor \to Curso$, 
- $Profesor \to Texto$ y
- $Curso \to Texto$ 
 
quedan descartadas.

>[!info]+ **Ejemplo: ¿Qué DF puede existir?**
>Una relación R (A, B, C, D) con su extensión
>
>| A   | B   | C   | D   |
>| --- | --- | --- | --- |
>| a1  | b1  | c1  | d1  |
>| a1  | b2  | c2  | d2  |
>| a2  | b2  | c2  | d3  |
>| a3  | b3  | c4  | d3  |
>
>¿Qué DF pueden existir en esta relación?
>
>Posibles:
>B -> C, C->B, 
>{A,B} ->C 
>{A.B} -> D
>{C,D} -> B
>
>No posibles:
>A -> B, B -> A
>D -> C

---

### 2.2.  Formas normales

En el proceso de normalización debe irse **comprobando que cada tabla cumple una serie de reglas que se basan en la clave primaria y las dependencias funcionales**. Cada regla que se cumple aumenta el grado de normalización. Si una regla **no se cumple**, la **tabla se debe descomponer** en varias tablas que sí la cumplan.

La normalización se lleva a cabo en una serie de pasos. Cada paso corresponde a una forma normal que tiene unas propiedades. Conforme se va avanzando en la normalización, las tablas tienen un formato más estricto (más fuerte) y, por lo tanto, son menos vulnerables a las anomalías de actualización. El modelo relacional sólo requiere un conjunto de tablas en primera forma normal (en caso contrario no se pueden implementar). Las restantes formas normales son opcionales. Sin embargo, para evitar las anomalías de actualización, es recomendable llegar al menos a la tercera forma normal. **La forma normal de una relación hace referencia a la forma normal más alta que cumple**, e indica por tanto el **grado al que ha sido normalizada**. 

Las formas normales son un i**ntento de garantizar que no se destruyan datos verdaderos ni se creen datos falsos en la base de datos**. Una de las maneras de evitar errores es representar un hecho solo una vez en la base de datos, ya que si un hecho aparece más de una vez, es probable que una de sus instancias sea errónea: una persona con dos relojes de pulsera nunca puede estar segura de la hora. Por eso sincronizamos los relojes con un reloj atómico, convirtiéndolos en vistas de una misma fuente de datos.

Este proceso de diseño de tablas se llama normalización. No es misterioso, pero puede ser complejo. Se pueden adquirir herramientas CASE (Ingeniería de Software Asistida por Computadora) para facilitar su uso, pero conviene conocer un poco la teoría antes de usar dicha herramienta.

>[!reminder]+ Definiciones de claves y atributos principales o primos
>- Una superclave de un esquema de relación $R = {A_1, A_2, \dots, A_n}$ es un conjunto de atributos $S$ subconjunto de $R$ con la propiedad de que ninguna de dos tuplas $t_1$ y $t_2$ en ningún estado de relación legal $r$ de $R$ tendrá $t_1[S] = t_2[S]$.
>- Una clave $K$ es una superclave con la propiedad adicional de que la eliminación de cualquier atributo de $K$ hará que $K$ ya no sea una superclave.
>- Si un esquema de relación tiene más de una clave, cada una se denomina **clave candidata**.
>- Una de las claves candidatas se designa arbitrariamente como **clave primaria** y las demás se denominan **claves alternativas**.
>- Un **atributo primo** (o principal) debe ser miembro de alguna clave candidata.
>- Un **atributo no primo** (o no principal) no es miembro de ninguna clave candidata.
#### 2.2.1. Primera forma normal: 1FN

Primera Forma Normal (1NF) significa que la tabla no tiene grupos repetitivos. Un grupo repetitivo es un atributo que puede tener múltiples valores para cada fila de la relación.Es decir, **cada columna es un valor atómico**, no una matriz, una lista ni nada con su propia estructura. También significa que tiene **un único significado**: puede ser la talla de un sombrero o de un zapato, pero nunca ambas ni ninguna de las dos.

Por lo tanto, la 1FN no permite atributos compuestos, multivaluados, relaciones anidadas o, fundamentalmente, atributos cuyos valores para una tupla individual no son atómicos.

La primera forma normal (1FN) ahora s**e considera parte de la definición formal de una relación en el modo relacional básico**. De hecho, la mayoría de los SGBDR permiten solo definir aquellas relaciones que están en 1FN.

Considera el esquema de relación DEPARTAMENTO, cuya clave principal es `NumeroDpto`, y posee el atributo `UbicacionesDpto`, de manera que cada departamento puede tener varias ubicaciones. El esquema DEPARTAMENTO no está en 1FN porque `UbicacionesDpto` no es un atributo atómico, como lo ilustra la primera tupla en (b). 

![[BD - 1FN.png]]

Existen **tres técnicas principales para lograr la primera forma normal** para dicha relación:
1. Eliminar el atributo `UbicacionesDpto` que infringe la 1FN y colocarlo en una r**elación independiente, LOCALIZACIONES_DPTO**, junto con la clave primaria `NumeroDpto` de DEPARTAMENTO. La clave primaria de esta nueva relación es la combinación {`NumeroDpto`, `UbicacionesDpto`}, como en la [[BD Empresa - Ejemplo completo|Base de datos Empresa]]. Existe una tupla distinta en LOCALIZACIONES_DPTO para cada ubicación de un departamento. Esto descompone la relación no-1FN en dos relaciones 1FN.
2. **Expandir la clave** para que haya una tupla separada en la relación DEPARTAMENTO original para cada ubicación de un DEPARTAMENTO, como se muestra en (c). En este caso, la clave principal se convierte en la **combinación {`NumeroDpto`, `UbicacionesDpto`}**. Esta solución tiene la **desventaja de introducir redundancia** en la relación y, por lo tanto, rara vez se adopta. De elegirse, se descompondrá en la primera solución durante los pasos de normalización posteriores. 
3. Si se conoce un número máximo de valores para el atributo (por ejemplo, si se sabe que pueden existir como máximo tres ubicaciones para un departamento), se puede reemplazar el atributo `UbicacionesDpto` por tres atributos atómicos:  `Ubicacion1Dpto`,  `Ubicacion2Dpto` y  `Ubicacion3Dpto`. Esta solución tiene la **desventaja de introducir valores nulos** si la mayoría de los departamentos tienen menos de tres ubicaciones. Además, introduce una **semántica espuria sobre el orden entre los valores de ubicación**; ese orden no fue el previsto originalmente. Consultar este atributo se vuelve más difícil. Por todas estas razones, es **mejor evitar esta alternativa**.

De las tres soluciones anteriores, **la primera se considera generalmente la mejor porque no presenta redundancia y es completamente general**; no impone un límite máximo al número de valores. De hecho, si elegimos la segunda solución, se descompondrá aún más durante los pasos de normalización posteriores en la primera solución.
#### 2.2.2. Segunda forma normal: 2FN

Una tabla está en Segunda Forma Normal (2FN) si **está en 1FN y no tiene dependencias de clave parciales**. Informalmente, la tabla está en 1FN y tiene una clave que determina todos los atributos no clave de la tabla.

>[!info] Claves primarias con más de un atributo
> La 2FN se aplica a las **tablas que tienen claves primarias compuestas por dos o más atributos**. Si una tabla está en 1FN y su clave primaria es simple (tiene un solo atributo), entonces también está en 2FN.

Un esquema de relación $R$ está en 2FN si está en 1FN y si cada **atributo no primo $A$ en $R$ presenta una dependencia funcional completa de la clave primaria** de $R$.

En la relación $EMP\_PROY$ siguiente, se pueden encontrar ejemplos de dependencias totales y parciales. En concreto:
- {Dni, NumProyecto} $\to$ Horas es una dependencia funcional total; ya que ni Dni $\to$ Horas ni NumProyecto $\to$ Horas son dependencias funcionales válidas
- {Dni, NumProyecto} $\to$ NombreE es una dependencia funcional parcial, ya que Dni $\to$ NombreE es una dependencia funcional válida (NombreE se refiere al nombre de un empleado)

![[BD - 2FN.png]]
Una relación $R$ en 1FN pero no en 2FN se puede descomponer en relaciones 2FN en las que los atributos no primos aparecen con la parte de la clave primaria de la que son completa y funcionalmente dependientes sin pérdida de información y de dependencias. Por lo tanto, las dependencias funcionales FD1, FD2 y FD3 en la figura anterior conducen a la descomposición de EMP_PROJ en los tres esquemas de relación EP1, EP2 y EP3, cada uno de los cuales está en 2FN.

Dicho de otra forma, para pasar una tabla en 1FN a 2FN hay que eliminar las dependencias parciales de la clave primaria. Para ello, se **eliminan los atributos que presentan dependencia funcional parcial y se ponen en una nueva tabla con una copia de su determinante**. Su determinante estará formado por los atributos de la clave primaria de los que depende.
#### 2.2.3. Tercera forma normal: 3FN

Una tabla está en tercera forma normal (3FN) si, y sólo si, está en 2FN y, además, ningún atributo no primo depende transitivamente de la clave primaria. La dependencia $X \to Z$ es transitiva si existen las dependencias $X \to Y$ , $Y \to Z$ , siendo $X, Y, Z$ atributos o conjuntos de atributos de una misma tabla.

En la relación $EMP\_DEPT$ siguiente, se pueden encontrar ejemplos de dependencias transitivas y no. En concreto:
- Dni $\to$ DniDirector es una dependencia funcional transitiva, dado que se cumplen Dni $\to$ NumeroDpto y NumeroDpto $\to$ DniDirector. Y NumeroDpto no es clave ni subconjunto de clave.
- Dni $\to$ NombreE no es transitivo, dado que no existe un conjunto de atributos $X$ donde Dni $\to X$ y $X \to$ NombreE

![[BD - 3FN.png]]

Para pasar una relación en 2FN a 3FN hay que eliminar las dependencias transitivas. Para
ello, se eliminan los atributos que dependen transitivamente y se ponen en una nueva relación con una copia de su determinante (el atributo o atributos no clave de los que depende). El esquema de relación `EMP_DEPT` de la figura anterior está en 2FN, ya que no existen dependencias parciales de una clave. Sin embargo, `EMP_DEPT` no está en 3FN debido a la dependencia transitiva de DniDirector (y también de NombreDtpo) de Dni a través de NumeroDpto. Se puede normalizar `EMP_DEPT` descomponiéndola en los dos esquemas de relación en 3FN ED1 y ED2. Intuitivamente,  se puede ver que ED1 y ED2 representan datos independientes sobre empleados y departamentos, ambos entidades por derecho propio. Una operación `NATURAL JOIN` en ED1 y ED2 recuperará la relación original `EMP_DEPT` sin generar tuplas espurias.

>[!note]+ Las relaciones en 3FN pueden tener dependencias transitivas
>Un malentendido común sobre la teoría relacional es que las tablas 3FN no tienen dependencias transitivas. 
>
>En $X \to Y$ e $Y \to Z$, con $X$ como clave primaria, es un problema solo si $Y$ no es una clave candidata (o un atributo primo). Cuando $Y$ es una clave candidata, no hay problema con la dependencia transitiva.
>
>Por ejemplo, considera la relación $EMP$(<u>Dni</u>, =Emp#=, Sueldo). En esta relación en 3FN, se da la dependencia funcional transitiva Dni $\to$ Emp# $\to$ Sueldo porque Emp# es una clave candidata.

### 2.3. Resumen de formas normales definidas informalmente

| Forma normal | Definición informal                                         |
|:------------:| ----------------------------------------------------------- |
|     1FN      | Todos los atributos dependen de la clave                    |
|     2FN      | Todos los atributos dependen de la clave completa           |
|     3FN      | Todos los atributos no dependen de nada más que de la clave |
### 2.4. Definiciones generales de forma normal (para claves múltiples) 

En general,  se quiere diseñar esquemas de relación de forma que no presenten dependencias parciales ni transitivas, ya que este tipo de dependencias causa las anomalías de actualización anteriormente descritas. Los pasos de normalización a relaciones 3FN analizados hasta ahora impiden las dependencias parciales y transitivas de la clave primaria. El **procedimiento** de normalización descrito **hasta ahora** es útil para el análisis en situaciones prácticas de una **base de datos dada donde ya se han definido claves primarias**. Sin embargo, estas definiciones no consideran otras claves candidatas de una relación, si las hubiera. A continuación, se presentan las definiciones más generales de 2FN y 3FN que consideran todas las claves candidatas de una relación.

**Definición general de 2FN**. Un esquema de relación $R$ está en segunda forma normal (2FN) si cada atributo no primo $A$ en $R$ tiene dependencia funcional completa de cada clave de R.

**Definición general de 3FN**. Un esquema de relación $R$ está en tercera forma normal (3FN) si siempre que se cumple una dependencia funcional $X \to Y$ en $R$, entonces:
- $X$ es una superclave de $R$, o
- $Y$ es un atributo principal o primo de $R$

Considera la relación $PARCELAS$ de la siguiente figura, 
- en la que hay dos claves candidatas: idPropiedad y {NombreMunicipio, NombreParcela}
- las dependencias funcionales FD1 y FD2 se cumplen
- la  dependencia funcional FD3 NombreMunicipio $\to$ Impuesto viola la 2FN porque depende parcialmente de la clave candidata {NombreMunicipio, NombreParcela}

Para normalizar $PARCELAS$ a 2FN se descompone la relación en las dos relaciones siguientes:
$PARCELAS1$ (<u>IdPropiedad</u>, NombreMunicipio, NúmeroParcela, Área, Precio)
$PARCELAS2$ (<u>NombreMunicipio</u>, Impuestos)

Se construye $PARCELAS1$ eliminando el atributo Impuestos que infringe la 2FN de $PARCELAS$ y colocándolo con NombreMunicipio (el lado izquierdo de FD3 que causa la dependencia parcial) en otra relación $PARCELAS2$. Tanto $PARCELAS1$ como $PARCELAS2$ están en 2FN. Observa que FD4 no infringe la 2FN y se transfiere a $PARCELAS1$.

La FD4 en $PARCELAS1$ viola la 3FN porque Área no es una superclave y Precio no es un atributo primo en $PARCELAS1$. Para $PARCELAS1$ LOTS1 en 3FN, se descompone en los esquemas de relación $PARCELAS1A$ y $PARCELAS1B$. Se construye $PARCELAS1A$ eliminando el atributo Precio que viola la 3FN de $PARCELAS1$ y colocándolo con Área (el lado izquierdo de la FD4 que causa la dependencia transitiva) en otra relación $PARCELAS1B$. Tanto $PARCELAS1A$ como $PARCELAS1B$ están en 3FN.

![[BD - FNG.png]]

**Definición alternativa de 3FN**. Un esquema de relación $R$ está en 3FN si cada atributo no primo en $R$ cumple estas dos condiciones:
- Tiene dependencia funcional completa de cada clave de R
- No depende transitivamente de cada clave de R
Nótese que, expresado de esta manera, una relación en 3FN también cumple los requisitos para 2FN.

### 2.5. FNBC (Forma normal de Boyce-Codd)

**Definición general de FNBC**. Un esquema de relación R está en FNBC si siempre que se cumple una dependencia funcional $X \to Y$ en $R$, entonces $X$ es una superclave de $R$. FNBC se considera una forma más fuerte de 3FN.

### 2.6. Otras propiedades importantes

Las formas normales, consideradas de forma aislada de otros factores, no garantizan un buen diseño de base de datos. Generalmente no basta con comprobar por separado que cada esquema de relación de la base de datos esté, por ejemplo, en BCNF o 3NF. Además, el proceso de normalización mediante descomposición también debe confirmar la existencia de **propiedades adicionales que los esquemas relacionales**, en conjunto, deberían poseer.

Estas incluyen dos propiedades:
- La propiedad de **concatenación no aditiva o descomposición sin pérdidas** (lossless join decomposition), que garantiza que el problema de generación de tuplas espurias no ocurra con respecto a los esquemas de relación creados tras la descomposición.
- La propiedad de **preservación de dependencias**, que asegura que cada dependencia funcional esté representada en alguna relación individual resultante tras la descomposición.

La propiedad de **concatenación no aditiva es extremadamente crítica** y debe lograrse a cualquier precio, mientras que la propiedad de preservación de dependencias, aunque deseable, a veces se sacrifica.

## 3. Desnormalización

La **desnormalización** es el proceso **intencional** de introducir redundancia en un esquema de base de datos previamente normalizado, concatenando tablas o duplicando datos para mejorar el rendimiento de las consultas. Normalmente, consiste en almacenar la concatenación de relaciones de forma normal superior como una relación en una forma normal inferior (proceso contrario a la normalización).
### Apuntes prácticos para la denormalización

El tema de la desnormalización es una excelente manera de entrar en guerras religiosas. En un extremo, se encuentran los puristas relacionales que creen que la idea de no llevar el diseño de una base de datos al menos a 5FN es un crimen contra la naturaleza. En el otro extremo, se encuentran quienes simplemente añaden y mueven columnas por toda la base de datos con sentencias ALTER, sin mantener el esquema estable. La **razón** que se esgrime para la desnormalización es el **rendimiento**. **Una base de datos completamente normalizada requiere muchas concatenaciones para construir vistas comunes de los datos a partir de sus componentes**. Las concatenaciones solían ser muy costosas en términos de tiempo y recursos informáticos, por lo que "montar" la concatenación en una tabla desnormalizada podía ahorrar bastante. Hoy en día, disponemos de mejor hardware y software. Las vistas se pueden materializar e indexar si las sesiones las utilizan con frecuencia. **Hoy en día, solo se deberían desnormalizar los almacenes de datos, y nunca un sistema OLTP de producción**. El código procedimental adicional necesario para mantener la integridad de los datos de un esquema desnormalizado simplemente no vale la pena.

