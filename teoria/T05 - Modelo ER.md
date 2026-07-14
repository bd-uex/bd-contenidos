# El Modelo Entidad Relación Extendido

---

tags: #database, #lecture, #entity-relationship
author: aeprieto
origin: [“Fundamentos de Sistemas de Bases de Datos”](https://explora.unex.es/discovery/fulldisplay?docid=alma991004714442207611&context=L&vid=34UEX_INST:34UEX&lang=es&search_scope=MyInst_and_CI&adaptor=Local%20Search%20Engine&tab=Everything&query=any,contains,elmasri&sortby=date_d&facet=frbrgroupid,include,9013732198023457128&offset=0). R. Elmasri, R. y S. B. Navathe.  Addison-Wesley, 2008 (5ª edición) + contenido generado por Gemini 2.5 Pro refinado y editado por aeprieto
date: 2025-09-22

---
## Contenidos
1. Introducción
2. Los Pilares del Modelo - Entidades y Atributos
3. El Pegamento del Modelo: Relaciones.
4. El Arte del Buen Diseño - Criterios y Decisiones Clave.
5. Entidades Débiles: Cuando una Entidad Necesita Ayuda para Identificarse.
6. Relaciones de Grado Superior (N-arias).
7. Más Allá del E/R Básico - El Modelo Extendido (EER)
8. El Arte del Buen Diseño - Principios Fundamentales y Errores a Evitar
  Anexo I. Resumen BigER
## 1. Introducción
### 1.1. El Origen del Modelo Entidad-Relación: Las Cuestiones Abiertas por el Modelo Relacional

El [Modelo Relacional](T02%20-%20Modelo%20Relacional.md#Modelo%20Relacional), propuesto por Edgar F. Codd, fue una revolución. Ofreció una forma matemática, consistente y robusta de almacenar y gestionar datos, superando el [problema de la rigidez](anexos/Problema%20Modelo%20Jerárquico.md) del [Modelo Jerárquico](anexos/Historia%20Bases%20de%20Datos.md#Modelo%20Jerárquico)  y [el problema de la complejidad](anexos/Problema%20Modelo%20Red.md) del [Modelo en Red](anexos/Historia%20Bases%20de%20Datos.md#Modelo%20de%20Red%20(o%20grafo)). Por primera vez, los programas podían trabajar con los datos sin depender de punteros ni rutas de acceso físicas.

Sin embargo, el relacional es un modelo **lógico**: describe cómo estructurar y manipular los datos con consistencia matemática, pero no cómo capturar el significado del negocio ni cómo llevar a cabo el proceso de diseño previo. Esto dejó abiertos varios desafíos importantes:

1. **El Desafío Semántico: ¿Qué significan realmente los datos?** El **Modelo Relacional**, por decirlo de forma sencilla, trabaja con tablas, filas y columnas. Sus claves y restricciones expresan _algo_ de significado, pero no la semántica del dominio: una tabla `T01` con columnas `C1` y `C2` no dice nada sobre si representa a un "Cliente" comprando un "Producto" o a un "Paciente" asignado a un "Doctor". Somos nosotros quienes le damos la interpretación. Hay además un síntoma revelador: en el relacional _todo_ es una tabla — la que almacena clientes y la que almacena el hecho de que un cliente hace un pedido tienen exactamente la misma forma. El mundo real tiene al menos tres (entidades, atributos y relaciones). El modelo E/R le da a cada una su propia categoría. Para un negocio, en cambio, la realidad no son "tablas": son "Clientes", "Productos" y "Pedidos", y las relaciones entre ellos. La pregunta era: ¿cómo podemos modelar el significado de los datos del negocio antes de pensar en tablas?
      
2. **La Barrera de la Comunicación: ¿Cómo diseñar con personas no técnicas?**  Diseñar una **base de datos relacional** requiere una comunicación fluida con los expertos del negocio (gerentes, empleados, etc.). Es prácticamente imposible que una persona no técnica valide un diseño expresado como un conjunto de tablas normalizadas, claves foráneas y tipos de datos: es un lenguaje demasiado técnico y orientado a la máquina. Se necesitaba un lenguaje común, preferiblemente visual, que permitiera a diseñadores y usuarios del negocio hablar sobre la misma realidad sin barrera técnica.
            
3. **La Dependencia del Modelo de Datos: ¿Y si no quiero usar tablas?** El **Modelo relacional** es una forma específica, excelente y popular,  de implementar una base de datos, pero el diseño conceptual de un negocio no debería depender de la tecnología final. De hecho, la arquitectura de tres niveles ANSI/SPARC (1975) dio nombre a esta separación: una cosa es el nivel conceptual (el "plano" del universo de datos de la empresa) y otra el nivel lógico en el que ese plano se materializa. ¿Cómo crear ese plano de forma que valiera igual si al final se construía sobre una base de datos relacional, una orientada a objetos o cualquier otra tecnología?

Estas tres preguntas encontraron respuesta en 1976, cuando Peter Chen propuso el **Modelo Entidad-Relación**: un lenguaje **conceptual** (semántica del negocio), **visual** (comunicación sin barrera técnica) e **independiente de la tecnología** (un plano, no una implementación) para describir el mundo antes de describir las tablas.

---
### **1.2. La Solución de Chen: Un Modelo para el Diseño Conceptual**

Frente a estos desafíos, Peter Chen propuso en 1976 el **Modelo Entidad-Relación**[^1].

[^1]: Nos referiremos a menudo a él también como modelo ER a secas.

> [!INFO] Artículo 
> Peter P. S. Chen. 1976. _[The Entity-Relationship Model—Toward a Unified View of Data](https://doi.org/10.1145/320434.320440)_, ACM Trans. Database Syst. 1, 1 (March 1976), 9-36.

Su objetivo no era reemplazar al modelo relacional, sino cubrir el paso previo y fundamental que este no cubría: un modelo de alto nivel para el **diseño conceptual**. (El subtítulo del artículo —"hacia una visión unificada de los datos"— revela la ambición original: Chen lo presentó como un marco capaz de abarcar los modelos existentes, el relacional incluido; el tiempo consolidó su papel como el lenguaje estándar de la fase conceptual.)

El Modelo Entidad-Relación respondió punto por punto a las tres preguntas de la sección anterior:

1. **La respuesta al desafío semántico.** Chen creó un modelo que captura la semántica del negocio _antes_ de pensar en tablas: permite modelar directamente un `Cliente` (una **entidad**) que tiene un `Nombre` (un **atributo**) y que `realiza` (una **relación**) un `Pedido` (otra entidad). Lo que en el relacional era indistinguible —todo son tablas— aquí tiene categorías propias con significado. El modelo se centra en _qué_ significa la información, no en cómo se almacenará.
    
2. **La respuesta a la barrera de comunicación.** Chen acompañó el modelo de un lenguaje visual y común: los **diagramas ER**. Un gerente sin formación técnica puede mirar un diagrama de rectángulos y rombos y decir: _"no, un cliente puede tener muchos pedidos, no solo uno"_ — corrigiendo la lógica del negocio en la fase de diseño, cuando equivocarse es barato y arreglar es fácil. (Esa frase del gerente es, por cierto, una **cardinalidad**: una regla de negocio que el diagrama captura con un simple símbolo. Las estudiaremos en detalle más adelante.)

3. **La respuesta a la dependencia tecnológica.** Un diagrama ER es puramente conceptual: describe la realidad del negocio con independencia de la tecnología que la almacenará. Un mismo diagrama puede implementarse después sobre una base de datos relacional, una orientada a objetos o cualquier otra. Es, exactamente, el "plano" del nivel conceptual de ANSI/SPARC que buscábamos en la sección anterior.

La mejor forma de fijar la diferencia es la analogía de construir una casa:

- **El Modelo ER es el plano del arquitecto** — el diseño _conceptual_. Es el primer boceto, y se dibuja hablando con el cliente (los usuarios del negocio). Define las habitaciones (**entidades** como "Cliente" o "Producto"), sus características (**atributos** como "Nombre" o "Precio") y los pasillos que las conectan (**relaciones** como "Compra"). Su propósito es capturar las necesidades y servir de herramienta de comunicación: asegurarse de que la estructura tiene sentido antes de poner un solo ladrillo. Cualquiera puede leerlo.
    
- **El Modelo Relacional es el plano de ingeniería** — el diseño _lógico_. Traduce el plano del arquitecto en especificaciones precisas y sin ambigüedad: las vigas, los muros de carga y las conducciones (**tablas**, **columnas**, **claves primarias y foráneas**). Su lector ya no es el cliente: es el equipo técnico.

- Y completando los tres niveles de ANSI/SPARC: **la obra misma es el nivel físico** — los cimientos y materiales concretos con los que el constructor (el DBMS) materializa el plano: ficheros, índices, almacenamiento. Al cliente nunca se le enseña, y esa es precisamente la gracia: cada nivel protege al anterior de los detalles del siguiente.

En el resto del tema aprenderemos a dibujar el plano del arquitecto: sus piezas (entidades, atributos y relaciones) y sus reglas (cardinalidades y restricciones).

---
### **1.3. El Flujo de Trabajo: Cómo se Complementan Ambos Modelos**

El modelo entidad-relación y el modelo relacional **operan en niveles de abstracción diferentes y resuelven problemas distintos**. No son competidores: son eslabones consecutivos del mismo proceso de diseño — y encajan, uno a uno, con los planos de nuestra analogía de la casa:

1. **Fase 1. Conceptual** _(el plano del arquitecto)_: nos comunicamos con los usuarios para entender sus necesidades y creamos un **diagrama ER** que modela la realidad del negocio. Aquí es donde se piensa, se pregunta y se corrige barato.
    
2. **Fase 2. Lógica** _(el plano de ingeniería)_: traducimos el diagrama ER a un conjunto de relaciones. Y aquí una buena noticia que veremos en su momento: esa traducción sigue reglas tan sistemáticas que es casi mecánica (la veremos en el Tema 6). El trabajo intelectual duro ya se hizo en la fase 1; el resultado es un **esquema relacional**.
    
3. **Fase 3. Física** _(la obra)_: el esquema relacional se implementa en un sistema gestor concreto (MySQL, Oracle, PostgreSQL…), con sus ficheros, índices y detalles de almacenamiento.
    
En resumen: **Chen no creó una alternativa al modelo relacional, sino el puente que faltaba entre la comprensión humana de la realidad desordenada de un negocio y la estructura lógica, ordenada y matemática del modelo relacional.** Codd nos dio una forma soberbia de construir la base de datos; Chen, una forma soberbia de diseñarla primero.

Antes de empezar a construir, dos decisiones prácticas: **con qué notación** dibujaremos nuestros diagramas y **sobre qué ejemplo** empezaremos a trabajar.

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> En las siguientes subsecciones iremos presentando los conceptos del modelo entidad-relación. Tradicionalmente, estos conceptos se han plasmado con la notación gráfica de Chen: es el pilar sobre el que se construyó todo el modelado de datos, la "lengua madre" — y de hecho **aprenderemos los conceptos con su vocabulario**. Sin embargo, para dibujar usaremos el lenguaje **BigER** y la notación **Crow's Foot**. ¿Por qué?
Antes de nada, ¿qué es cada cosa?
>- **BigER** es un lenguaje que nos permite escribir la **receta** de nuestro modelo de datos: un texto claro y preciso que describe cada "ingrediente" (entidad, atributo) y cada "paso" (relación).
  > - La **notación Crow's Foot** ("pata de gallo") es una de las formas más populares de "presentar el plato": un estilo de diagrama compacto e intuitivo que permite _ver_ la receta de un vistazo.
> 
> No se trata de que Chen sea "incorrecto", sino de que este flujo de trabajo moderno es más **ágil, colaborativo y mantenible**. Veamos por qué.
> ###### 1. Separa la Lógica de la Presentación: La Receta vs. el Plato 🧠
> Esta es la ventaja más importante.
> - **Método Tradicional (Chen)**: el diagrama _es_ el modelo. La lógica (las reglas del negocio) y la presentación están fusionadas en una sola imagen: mostrar el mismo modelo en otra notación significa **redibujarlo desde cero**.
> - **Método Moderno (BigER + Crow's Foot)**: la receta (el código BigER) es el modelo, la **única fuente de la verdad**; el diagrama es solo una representación visual que se regenera a partir de ella. La lógica permanece intacta aunque cambie la presentación — como una partitura, que es la misma canción la toque una orquesta o un pianista.
> ###### 2. Agilidad y Mantenimiento: Editar Texto es Más Fácil que Redibujar.
> Imagina que necesitas cambiar cualquier elemento del modelo.
> - **Método Tradicional**: abrir el editor gráfico, borrar los elementos implicados, redibujarlos y, si el cambio es grande, reorganizar todo el diagrama para que quepa y se entienda. Manual y lento.
> - **Método Moderno**: abres el archivo de texto, cambias las líneas necesarias, guardas, y el diagrama se **regenera automáticamente**. Un cambio de segundos, preciso y sin esfuerzo.
>  ###### 3. Colaboración y Control de Versiones (Git) 🤝
> El factor decisivo en cualquier proyecto de equipo.
> - **Método Tradicional**: los diagramas viven en archivos binarios (imágenes, Visio…), una pesadilla para Git: es casi imposible ver qué cambió entre dos versiones, y fusionar el trabajo simultáneo de dos personas es prácticamente inviable.
> - **Método Moderno**: el modelo BigER es **texto plano** — perfecto para Git. Historial línea a línea, saber quién cambió qué y por qué, fusionar el trabajo del equipo, revertir si algo sale mal. El diseño de la base de datos pasa a ser tratado como lo que es: **parte del código del proyecto**.
> ###### 4. Claridad y Estándar de la Industria: La Ventaja de Crow's Foot 🏭
>La notación de Chen es magnífica para aprender, pero crece mal: cada atributo es una burbuja aparte y un modelo mediano se convierte en un mural. Crow's Foot coloca los atributos _dentro_ de la caja de la entidad, lo que la hace mucho más compacta en modelos reales — y es el estándar de facto que encontrarás en las herramientas de la industria (MySQL Workbench, dbdiagram, Lucidchart…).
>
>**Una advertencia honesta antes de empezar**: algún concepto de Chen no tiene representación directa en Crow's Foot (por ejemplo, los atributos de las relaciones, que aparecerán en nuestro ejemplo de la Empresa). Cuando ocurra, lo señalaremos explícitamente y veremos cómo lo resuelve BigER: reconocer los límites de una notación también es aprender modelado.
>
> ##### Resumen Comparativo
>
| Característica      | Método Tradicional (Dibujar Chen)           | Método Moderno (BigER + Crow's Foot)                 |
| ------------------- | ------------------------------------------- | ---------------------------------------------------- |
| **Flexibilidad**    | El modelo y el diagrama son lo mismo.         | El modelo (código) y el diagrama están separados.      |
| **Mantenimiento**   | Lento y manual: redibujar para cada cambio. | Rápido y automático: editar texto.                   |
| **Colaboración**    | Difícil de versionar y fusionar (Git).      | Ideal para control de versiones y trabajo en equipo. |
| **Claridad Visual** | Muy explícita pero puede ser densa.         | Compacta y estándar en la industria.                 |
>
>###### 5. Instalación y uso de BigER en VSCode
>En esta nota tienes disponible una pequeña guía para empezar a usar BigER en VSCode: [AL09.1 - Instalación y uso de BigER en VSCode](../labs/anexos/AL09.1%20-%20Instalación%20y%20uso%20de%20BigER%20en%20VSCode.md)

---
> [!example] Ejemplo simple: Empresa
> Tras introducir los distintos conceptos, iremos completando el modelo Entidad/Relación de un ejemplo sencillo de una Empresa.
> Sus requisitos simplificados son:
> >[!exercise] La empresa está organizada en **Departamentos**
> >	- Cada departamento tiene un nombre, un número único y un empleado que lo dirige.
> >	- Llevamos un registro de la fecha de ingreso del director del departamento.
> >	- Un departamento se ubica en varias ubicaciones.
> 
> >[!exercise] Cada departamento controla una serie de **Proyectos**.
> >	- Cada proyecto tiene un nombre único, un número único y se localiza en una única ubicación.
> 
> >[!exercise] La empresa tiene **Empleados** que trabajan para un departamento
> >	-Cada empleado tiene dni, dirección, sueldo, sexo y fecha de nacimiento.
> >	-Cada empleado trabaja para un departamento pero puede trabajar en varios proyectos.
> >	-Se deben registrar las horas semanales que un empleado trabaja actualmente en cada proyecto.
> >	-Existen empleados que supervisan a otros empleados.
> 
> >[!exercise] Cada empleado puede tener **Familiares** a su cargo.
> >	-Para cada familiar, se registra su nombre, sexo, fecha de nacimiento y relación de parentesco con el empleado.

---
## 2. Los Pilares del Modelo - Entidades y Atributos

### 2.1. Entidades y Atributos: Los Sustantivos y sus Adjetivos

Imagina que tienes que organizar la información de un "minimundo" (como una universidad, una empresa o una biblioteca). Lo primero que harías es identificar las "cosas" u "objetos" más importantes que lo componen.

- **Tipo de Entidad**: Es la plantilla o categoría de un objeto importante. Piensa en ello como el nombre de una ficha que vas a rellenar. Por ejemplo: `Empleado`, `Departamento`, `Proyecto`. Son nuestros **sustantivos**.
    
- **Entidad**: Es una "cosa" específica y real de un tipo de entidad. Es la ficha ya rellenada. Por ejemplo, la persona "José Pérez" es una entidad del tipo `Empleado`.
    
- **Atributos**: Son las propiedades o características que describen a un tipo de entidad. Son los campos que tiene la ficha para rellenar. Para la ficha `Empleado`, los atributos serían `nombre`, `dni`, `dirección`, etc. Son nuestros **adjetivos**.
    

> **Analogía clave 🧠:**
> 
> - **Tipo de Entidad** = Una plantilla de ficha en blanco (ej: Ficha de `Empleado`).
>     
> - **Entidad** = Una ficha específica ya rellenada (ej: La ficha de "José Pérez").
>     
> - **Atributos** = Los apartados o campos de la ficha (ej: `nombre`, `dni`, `salario`).
>     

---
### 2.2 El Conjunto de Entidades: De la Plantilla a los Datos Reales

Ya hemos definido el **Tipo de Entidad** como la plantilla, el molde o el esquema (por ejemplo, la estructura de la ficha `Empleado` con todos sus campos). Pero, ¿dónde están los empleados de verdad?

Ahí es donde entra el **Conjunto de Entidades**.

- **Definición**: Es la **colección de todas las entidades** de un tipo específico que existen en la base de datos **en un momento dado**. Es una "foto" del estado actual de los datos.
    
- **Términos Clave**:
    
    - El **Tipo de Entidad** se conoce como la **intención** (el diseño o la idea).
        
    - El **Conjunto de Entidades** se conoce como la **extensión** (la manifestación real y actual de esa idea).
        

> **Analogía del Archivador 🧠:**
> 
> - **Tipo de Entidad `Empleado`**: Es el diseño de una ficha de empleado en blanco, con sus apartados `dni`, `nombre`, etc.
>     
> - **Conjunto de Entidades `Empleado`**: Es el **archivador real que contiene todas las fichas de empleados ya rellenadas** que tienes hoy. Si mañana contratas a alguien, el conjunto crece. Si alguien se va, el conjunto se reduce.
>     

Es común, aunque a veces confuso, usar el mismo nombre (ej. `Empleado`) para referirse tanto al tipo como al conjunto. Lo importante es que entiendas que uno es el **plano** y el otro es el **edificio construido**.

---

### 2.3. Tipos de Atributos: La Anatomía de la Descripción

No todos los atributos son iguales. Se clasifican según cómo guardan la información.

- **Simple o Atómico ⚛️**: Es un atributo que no se puede dividir en partes más pequeñas con significado propio.
    
    - **Ejemplo**: `dni` o `sexo`. No puedes descomponer un dni en sub-partes lógicas.
        
- **Compuesto 🧩**: Es un atributo que está formado por otros atributos más pequeños. Es como un cajón que tiene compartimentos dentro.
    
    - **Ejemplo**: `direccion` puede ser un único campo de texto o estar compuesto por `calle`, `ciudad`, `codigo_postal`.  `nombre_completo` es otro gran ejemplo, compuesto por `nombre`, `apellido_1` y `apellido_2`.
    - Si se sabe que va a ser necesario hacer búsquedas continuas por alguno de los atributos simples que componen el atributo compuesto entonces se modelarán como atributos simples
	    - En nuestro ejemplo, dejaremos `dirección` como un único atributo porque consideramos que no va a haber búsquedas por sus atributos concretos mientras que para el nombre de los empleados usaremos `nombre`,`apellido_1` y `apellido_2` porque en este caso sí se considera que va a haber búsquedas por dichos atributos.
	    - Estrictamente, esta es una consideración de uso que anticipa la fase lógica; la adoptamos ya en el diseño conceptual por pragmatismo, porque cambiar la granularidad de un atributo más tarde es costoso
        
- **Multivalor 📇**: Es un atributo que puede tener varios valores para una misma entidad. Piensa en ello como una "lista" o "bolsa" de valores.
    
    - **Ejemplo**: El atributo `telefono` de un `Empleado`, ya que una persona puede tener un móvil y un fijo. O los `Colores` de un `Coche`.
    
    >[!warning] ⚠️ En esta asignatura adoptamos la convención de que todo atributo multivalor se convertirá, en la versión final del diagrama, en una entidad débil (lo veremos en la sección 5). Otras notaciones permiten mantenerlo como atributo, pero nuestra convención simplifica la traducción posterior al modelo relacional.


---

### 2.4. Tipos de Entidades y Atributos Clave: La Búsqueda del Identificador Único 🔑

Si tienes un archivador con miles de fichas de `Empleado`, ¿cómo encuentras una en concreto sin dudar? No puedes usar el nombre, ¡podría haber varios "José Pérez"! Necesitas un identificador único.

- **Atributo Clave (o Clave Primaria)**: Es un atributo (o un conjunto de ellos) que tiene un valor **único** para cada entidad. Es la garantía de que no hay dos fichas iguales.
    
    - **Ejemplo**: El `dni` para un `Empleado` o la `matrícula` para un `Coche`.
        
- **Clave Candidata**: A veces, un tipo de entidad tiene varios atributos que podrían servir como clave primaria. Todos ellos son "candidatos".
    
    - **Ejemplo**: Para el tipo de entidad `Coche`, tanto el `vin` (Número de Identificación del Vehículo) como la `matrícula` son únicos. Ambos son claves candidatas.
        
- **Elección de la Clave Primaria**: De entre todas las claves candidatas, elegimos una para que sea la clave primaria oficial. Las demás se llaman **claves alternativas**. Generalmente, se prefiere una clave simple (un solo atributo) si es posible.
    
---
### 2.5. Propiedades Adicionales de los Atributos

Además de la clasificación anterior, los atributos tienen otras características importantes:

- **Obligatorios y Opcionales**:
    
    - **Obligatorio**: El atributo debe tener un valor siempre (ej: `dni` de un `Empleado`).
        
    - **Opcional**: El atributo puede dejarse en blanco (ej: `numero_de_planta` en una `direccion`, si es una casa no lo necesita).
        
- **Almacenados y Derivados 🔢**:
    
    - **Almacenado**: Es un atributo que se guarda directamente en la base de datos (ej: `fecha_de_nacimiento`).
        
    - **Derivado**: Es un atributo que **no se almacena en la base de datos**, porque **se puede calcular** a partir de otro. Esto evita inconsistencias.
        
	    - **Ejemplo**: El atributo `edad` no lo **almacenamos**. Lo calculamos restando la `fecha_de_nacimiento` a la fecha actual. Así, `edad` siempre está actualizada y no ocupa espacio.
        
- **Conjuntos de Valores (Dominios)**: Cada atributo tiene un "dominio", que es el conjunto de todos los valores posibles que puede tomar. Es como el "tipo de dato" en programación.
    
    - **Ejemplo**: El dominio del atributo `sexo` podría ser `{'hombre', 'mujer', 'otro'}`. El dominio de `nota_examen` podría ser el conjunto de números reales entre 0 y 10.
        

---

> [!example] Aplicando estos conceptos al caso Empresa
> Al leer los requisitos de la base de datos **Empresa**, hacemos una primera pasada para identificar los "sustantivos" principales:
> >[!exercise] La empresa se organiza en **Departamentos**. -> Tipo de Entidad `Departamento`
> 
> >[!exercise] Cada departamento controla **Proyectos**. -> Tipo de Entidad `Proyecto`
> 
> >[!exercise] La empresa tiene **Empleados**. -> Tipo de Entidad `Empleado`
> 
> >[!exercise] Cada empleado puede tener **Familiares**. -> Tipo de Entidad `Familiar`
>
> Ahora, para cada uno, listamos sus "adjetivos" o atributos según los requisitos:
> 
> >[!exercise] Tipo de Entidad `Departamento`
> >	- nombre (clave candidata)*
> >	- número (clave candidata)*
> >	- ubicaciones (multivalor)
> >	- director
> >	- fecha_ingreso_director
> >	* De estas dos claves candidatas elegiremos una como primaria más adelante.
> 
> >[!exercise] Tipo de Entidad `Proyecto`
> >	- nombre (clave)
> >	- número (clave)
> >	- ubicacion 
> >	- departamento_control
> 
> >[!exercise] Tipo de Entidad `Empleado`
> >	- dni (clave)
> >	- direccion
> >	- sueldo 
> >	- sexo
> >	- nombre
> >	- apellido_1
> >	- apellido_2
> >	- fecha_nacimiento
> >	- supervisor
> >	- departamento
> >	- trabaja_en (multivalor y compuesto)
> >		- proyecto
> >		- horas
> 	
> >[!exercise] Tipo de Entidad `Familiar`
> >	- nombre
> >	- sexo
> >	- fecha_nacimiento
> >	- relacion
> >	

>[!question] Pregunta
>	¿Cuál sería la clave del tipo de Entidad `Familiar`?

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > | Concepto                                                                                                        | Textual en BigER                                                                                                                                                                        | Notación Crow's foot en BigER                                                                                                   |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Entidad                                                                                                 | `entity` **Entidad** {<br>..<br>}                                                                                                                                                       | ![](../imgs/BD%20-%20BigER%20Entidad.png)                                                                                               |
| Atributo clave                                                                                          | `entity` Entidad {<br>**atributo_clave** `key`<br>}                                                                                                                                     | ![](../imgs/BD%20-%20BigER%20atributo%20clave.png)                                                                                      |
| Atributo no clave                                                                                       | `entity` Entidad {<br>atributo_clave `key`<br>**atributo_no_clave**<br>}                                                                                                                | ![](../imgs/BD%20-%20BigER%20atributo%20no%20clave.png)                                                                                 |
| Atributo de clave alternativa<br>**No Soportado por BigER pero haremos lo que se muestra en cada caso** | //**añadimos el comentario UNIQUE al final de la definición del atributo**<br>`entity` Entidad {<br>atributo_clave `key`<br>atributo_no_clave<br>**atributo_clave_alternativa //UNIQUE**<br>} | **Cuando lo dibujemos a mano, pondremos un subrayado discontinuo **<br>![](../imgs/BD%20-%20BigER%20atributo%20clave%20alternativa.png) |

---
### 2.6 Refinando el Diseño: ¿Y los Verbos?

Este primer diseño es un gran comienzo, pero está incompleto. Hemos identificado los sustantivos (`Entidades`) y sus adjetivos (`Atributos`), pero nos falta la parte más importante: las acciones o conexiones entre ellos, es decir, **los verbos**.

Frases como:

- Un `Empleado` **dirige** un `Departamento`.
    
- Un `Departamento` **controla** `Proyecto`.
    
- Un `Empleado` **trabaja en** `Proyecto`.
    
Estas conexiones son el tercer pilar del modelo: las **Relaciones**, y las exploraremos en la siguiente sección.

---
## 3: El Pegamento del Modelo - Relaciones

### 3.1 Introducción: De los Sustantivos a los Verbos

En la sección anterior, identificamos los "sustantivos" de nuestro minimundo (los **Tipos de Entidad**) y sus "adjetivos" (los **Atributos**). Pero un diseño solo con eso es como una lista de personajes sin historia. Nos falta la acción, las conexiones, los **verbos**.

Las **Relaciones** son el tercer y último pilar del modelo ER. Representan las asociaciones con significado que existen entre las entidades.

- `Empleado` "José Pérez" **trabaja en** el `Proyecto` "Producto_Secreto_1".
    
- `Empleado` "Laura Martínez" **dirige** el `Departamento` "Investigación".
    

Esas palabras en negrita son las relaciones. Le dan contexto y sentido a los datos, explicando cómo interactúan las entidades entre sí.

---

### 3.2 Tipos de Relación: Categorizando las Acciones

Al igual que agrupamos entidades similares en "Tipos de Entidad", agrupamos relaciones similares en **Tipos de Relación**.

- **Tipo de Relación**: Es la definición abstracta de una asociación entre tipos de entidad. Por ejemplo, `trabaja_en` es el tipo de relación que conecta `Empleado` y `Proyecto` y `dirige` es el tipo de relación que conecta `Empleado` y `Departamento`.

- **Grado de una Relación**: Es el número de tipos de entidad que conecta la relación. La gran mayoría de las veces trabajarás con relaciones de **grado 2 (binarias)**, como todas las de nuestro ejemplo ( tanto `dirige` como `trabaja_en` son binarias).

- **Instancias de Relación:** Las conexiones *reales* entre entidades *específicas*.

![](../imgs/BD%20-%20Instancias%20del%20conjunto%20de%20relación%20TRABAJA_EN.png)

---

### 3.3 Tipo vs. Conjunto de Relaciones: El Contrato en Blanco vs. los Contratos Firmados

Este concepto es idéntico al que vimos con las entidades, y es crucial para separar el diseño de los datos reales.

- **Tipo de Relación (La Intención)**: Es el "contrato en blanco" o la plantilla. Es la descripción esquemática de una relación. Describe la relación, qué tipos de entidad conecta y qué restricciones tiene. Por ejemplo, la idea de que un `Empleado` puede `dirigir` un `Departamento`.
    
- **Conjunto de Relaciones (La Extensión)**: Es la colección de todos los "contratos firmados" que existen en la base de datos en un momento dado. Es la lista de todas las conexiones reales. Por ejemplo: `(Laura Martínez, Investigación)` es una instancia del conjunto de relaciones `dirige`.
---

> [!example] Aplicando Relaciones al Caso "Empresa"
> Volviendo a nuestro diseño inicial, ahora podemos establecer los "verbos" que conectan nuestras entidades basándonos en los requisitos:
> >[!exercise]  Un empleado **trabaja para** un departamento -> `trabaja_para` (entre `Empleado` y `Departamento`)
> >	-> Desaparece `departamento` como atributo en el diseño inicial del tipo de entidad `Empleado`	
> 
> >[!exercise]  Un empleado **dirige** un departamento -> `dirige` (entre `Empleado` y `Departamento`)
> >	-> Desaparece `director` como atributo en el diseño inicial del tipo de entidad `Departamento`
> >	-> Desaparece `fecha_ingreso_director` como atributo en el diseño inicial del tipo de entidad `Departamento`
> 
> >[!exercise]  Un departamento **controla** proyectos -> `controla` (entre `Departamento` y `Proyecto`)
> >	-> Desaparece `departamento_control` como atributo en el diseño inicial del tipo de entidad `Proyecto`
> 
> >[!exercise]  Un empleado **trabaja en** un proyecto -> `trabaja_en` (entre `Empleado` y `Proyecto`)
> >	-> Desaparece `trabaja_en` como atributo en el diseño inicial del tipo de entidad `Empleado` así como el atributo `proyecto` y el atributo `horas`
> 
> >[!exercise]  Un empleado **supervisa** a otros empleados -> `supervisa` (entre `Empleado` y `Empleado`)	
> >	-> Desaparece `supervisor` como atributo en el diseño inicial del tipo de entidad `Empleado`
> 
> >[!exercise]  Un empleado tiene **familiares a su cargo** -> `familiar_de` (entre `Empleado` y `Familiar`)
>
> Tras eliminar los atributos como consecuencia de la creación de las relaciones, ahora las entidades tienen estos atributos:
> 
> >[!exercise] Tipo de Entidad `Departamento` 
> >	- nombre (clave)
> >	- numero (clave)
> >	- ubicaciones (multivalor)
> 
> >[!exercise] Tipo de Entidad `Proyecto`
> >	- nombre (clave)
> >	- numero (clave)
> >	- ubicacion 
> 
> >[!exercise] Tipo de Entidad `Empleado`
> >	- dni (clave)
> >	- direccion
> >	- sueldo 
> >	- sexo
> >	- nombre
> >	- apellido_1
> >	- apellido_2
> >	- fecha_nacimiento
> >
> 	
> >[!exercise] Tipo de Entidad `Familiar`
> >	- nombre
> >	- sexo
> >	- fecha_nacimiento
> >	- relacion
> >	

>[!question] Pregunta
>	¿Qué ha pasado con el atributo horas que formaba parte del atributo trabaja_en en el tipo de Entidad `Empleados` y con el atributo fecha_inicio_director en el tipo de Entidad `Departamentos`?

---
### 3.4 Las Reglas del Juego: Restricciones Estructurales

Aquí es donde el modelo se vuelve realmente potente. No basta con decir que dos entidades se conectan; debemos especificar las **reglas de negocio** de esa conexión. Hay dos tipos de reglas principales.

#### A) Razón de Cardinalidad (Cardinalidad Máxima)

Esta regla responde a la pregunta: **"¿Con cuántas entidades te puedes relacionar como máximo?"**.

- **Uno a Uno (1:1)**: una entidad de un lado solo puede relacionarse con, como máximo, una del otro, y viceversa.
    
    - **Ejemplo (dirige)**: un `Empleado` solo puede dirigir **un** `Departamento`. Y un `Departamento` solo puede tener **un** director.
        
- **Uno a Muchos (1:N)**: una entidad del lado "1" se relaciona con muchas del lado "N", pero una del lado "N" solo se relaciona con una del lado "1".
    
    - **Ejemplo (trabaja_para)**: un `Departamento` puede tener **muchos** `Empleados`, pero un `Empleado` solo trabaja para **un** `Departamento`.
        
- **Muchos a Muchos (M:N)**: sin restricción de máximo en ninguno de los dos lados: una entidad de un lado puede relacionarse con muchas del otro, y viceversa.
    
    - **Ejemplo (trabaja_en)**: un `Empleado` puede trabajar en **varios** `Proyectos`, y un `Proyecto` puede tener **varios** `Empleados`.

>[!tip] **Cómo leer las cardinalidades en BigER**
>En `relationship trabaja_para   
> { Empleado[1..N] -> Departamento[1..1] }`,
> la cardinalidad escrita junto a una entidad indica **cuántas instancias de esa entidad** pueden asociarse a **una** instancia de la otra.
> - `Empleado[1..N]` se lee: «un departamento tiene entre 1 y N empleados».
> - `Departamento[1..1]` se lee: «un empleado trabaja para exactamente un departamento». Fíjate en que la cardinalidad "pertenece" a la entidad junto a la que está escrita, no a la de enfrente. En el diagrama Crow's Foot ocurre lo mismo: el símbolo dibujado junto a una entidad indica cuántas instancias de _esa_ entidad participan.

#### B) Restricción de Participación (Cardinalidad Mínima)

Esta regla responde a la pregunta: **"¿Es obligatorio participar en esta relación?"**.

- **Participación Total (Obligatoria)**: Significa que cada entidad de un tipo **debe** participar en la relación. 
    
    - **Ejemplo**: Si la política es que "todo `Empleado` debe pertenecer a un `Departamento`", la participación del `Empleado` en la relación `trabaja_para` es total. No puede existir un empleado sin departamento.
        
- **Participación Parcial (Opcional)**: Significa que una entidad puede existir sin necesidad de participar en la relación. 
    
    - **Ejemplo**: La participación de `Empleado` en la relación `dirige` es parcial. Un empleado no tiene por qué dirigir un departamento para existir en la base de datos. ¡La mayoría no lo hacen!      

---

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > | Concepto                                                                      | Textual en BigER                                                                                                                                                                      | Notación Crow's foot en BigER                                         |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Relación                                                                      | `relationship` **relacion** {<br>Entidad_1[`MIN..MAX`] -> Entidad_2[`MIN..MAX`]<br>}                                                                                                    | ![](../imgs/BD%20-%20BigER%20Relacion%20Crows%20Foot.png)                              |
| Relación con  cardinalidad mínima y máxima 1..1 en ambos lados                | `entity` Automovil {<br>..<br>}<br>`entity` Seguro {<br>..<br>}<br>`relationship` tiene {<br>Automovil[`1..1`] -> Seguro[`1..1`]<br>}<br><br>                                         | ![](../imgs/BD%20-%20BigER%20Relación%2011%2011%20Crows%20Foot.png) |
| Relación con cardinalidad mínima y máxima 1..1 en un lado y 1..N  en el otro  | `entity` Provincia {<br>..<br>}<br>`entity` Municipio {<br>..<br>}<br>`relationship` contiene {<br>Provincia[`1..1`] -> Municipio[`1..N`]<br>}<br>                                    | ![](../imgs/BD%20-%20BigER%20Relación%2011%201N%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima  1..N en ambos lados                | `entity` Cliente {<br>..<br>}<br>`entity` Cuenta_Bancaria {<br>..<br>}<br>`relationship` es_titular_de {<br>Cliente[`1..N`] -> Cuenta_Bancaria[`1..N`]<br>}                           | ![](../imgs/BD%20-%20BigER%20Relación%201N%201N%20Crows%20Foot.png)           |
| Relación con  cardinalidad mínima y máxima 0..1 en ambos lados                | `entity` Trabajador {<br>..<br>}<br>`entity` Plaza_Aparcamiento {<br>..<br>}<br>`relationship` tiene_asignada {<br>Trabajador[`0..1`] -> Plaza_Aparcamiento[`0..1`]<br>}              | ![](../imgs/BD%20-%20BigER%20Relación%2001%2001%20Crows%20Foot.png)           |
| Relación con Cardinalidad Opcional 0..1 en un lado y 1..1  en el otro         | `entity` Provincia {<br>..<br>}<br>`entity` Municipio {<br>..<br>}<br>`relationship` tiene_capital {<br>Provincia[`0..1`] -> Municipio[`1..1`]<br>}                                   | ![](../imgs/BD%20-%20BigER%20Relación%2001%2011%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..1 en un lado y 1..N  en el otro  | `entity` Cooperativa {<br>..<br>}<br>`entity` Agricultor {<br>..<br>}<br>`relationship` formada_por {<br>Cooperativa[`0..1`] -> Agricultor[`1..N`]<br>}                               | ![](../imgs/BD%20-%20BigER%20Relación%2001%201N%20Crows%20Foot.png)           |
| Relación con  cardinalidad mínima y máxima 0..N en ambos lados                | `entity` Turista {<br>..<br>}<br>`entity` Monumento {<br>..<br>}<br>`relationship` visita {<br>Turista[`0..N`] -> Monumento[`0..N`]<br>}                                              | ![](../imgs/BD%20-%20BigER%20Relación%200N%200N%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..N en un lado y 1..1  en el otro  | `entity` Vehiculo_Matriculado {<br>..<br>}<br>`entity` Persona {<br>..<br>}<br>`relationship` es_propiedad_de {<br>Vehiculo_Matriculado[`0..N`] -> Persona[`1..1`]<br>}               | ![](../imgs/BD%20-%20BigER%20Relación%200N%2011%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..N en un lado y 1..N  en el otro  | `entity` Interprete {<br>..<br>}<br>`entity` Pelicula {<br>..<br>}<br>`relationship` actua_en {<br>Interprete[`0..N`] -> Pelicula[`1..N`]<br>}                                        | ![](../imgs/BD%20-%20BigER%20Relación%200N%201N%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..1 en un lado y  0..N  en el otro | `entity` Socio_Biblioteca {<br>..<br>}<br>`entity` Ejemplar_de_Libro {<br>..<br>}<br>`relationship` tiene_en_prestamo {<br>Socio_Biblioteca[`0..1`] -> Ejemplar_de_Libro[`0..N`]<br>} | ![](../imgs/BD%20-%20BigER%20Relación%2001%200N%20Crows%20Foot.png)           |

---
### 3.5 Relaciones Recursivas: Cuando una Entidad se Relaciona Consigo Misma

El caso de la relación `supervisa` es especial. Aquí, el tipo de entidad `Empleado` se relaciona consigo mismo. Esto se llama **relación recursiva**.

Para que tenga sentido, la entidad participa en la relación desempeñando distintos **roles**.

> **Analogía del Teatro 🎭**: Piensa en el tipo de entidad `Empleado` como un grupo de actores. En la relación `supervisa`, un actor interpreta el **rol de "supervisor"** y otro actor interpreta el **rol de "supervisado"**. La relación conecta a un empleado en un rol con otro empleado en otro rol.

Cada instancia de la relación (ej: "Ana supervisa a Juan") siempre implicará a dos entidades distintas, aunque ambas pertenezcan al mismo tipo `Empleado`.

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > |  Concepto                             | Textual en BigER                                                                                                                  | Notación Crow's foot en BigER                                           |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Relación recursiva con roles | `entity` Usuario {<br>..<br>}<br>`relationship` sigue {<br>Usuario[`1..N` \|  `"Seguidor"`] -> Usuario[`1..N`\| `"Seguido"`]<br>} | ![](../imgs/BD%20-%20BigER%20Relación%20Recursiva%20Crows%20Foot.png) |

---
### 3.6 Atributos en las Relaciones: Describiendo la Conexión

A veces, una característica no describe a una entidad, sino a la **interacción entre ellas**. En esos casos, el atributo pertenece a la relación.

> **Ejemplo  (trabaja_en)**: Un empleado trabaja un número de `horas` semanales en un proyecto.
> 
> - Las `horas` no son un atributo del `Empleado`, porque esa persona trabajará un número diferente de horas en cada proyecto.
>     
> - Las `horas` no son un atributo del `Proyecto`, porque diferentes empleados le dedican diferentes horas.
>     
> 
> Las `horas` son una propiedad de la **conexión específica** entre un empleado y un proyecto. Por lo tanto, `Horas` es un atributo del tipo de relación `trabaja_en`.

>[!tip] **Regla de colocación de atributos de relación**:
>- En relaciones **M:N**, los atributos que describen la interacción **deben** ir en la relación.
>- En relaciones **1:N**, el atributo se puede mover al tipo de entidad del lado "N". 
>- En relaciones **1:1**, el atributo se puede mover a cualquiera de las dos entidades. 

---

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > | Concepto                      | Textual en BigER                                                     | Notación Crow's foot en BigER                                                                                                                                                                                                                                                           |
| -------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Atributo de relación | `relationship` **relacion** {<br>..<br>**atributo_de_relacion**<br>} | **Cuando pasemos el cursor en BigER por encima de la relación veremos que nos muestra el atributo**<br>![](../imgs/BD%20-%20BigER%20Atributo%20de%20Relación%20marcado.png)<br>**Cuando lo dibujemos a mano, pondremos una línea conectando al atributo**<br>![](../imgs/BD%20-%20BigER%20Atributo%20de%20Relacion.png)<br> |

---
### 3.7 Primera versión (incompleta e incorrecta) del Diagrama ER de Empresa

Con lo que sabemos hasta ahora, si tuvierámos que modelar el Diagrama Entidad Relación de la Empresa, podríamos obtener el siguiente diagrama que, aunque se acerca bastante a la versión final, no está completo ni es totalmente correcto.

![BD Empresa versión inicial incorrecta - BigER](anexos/BD%20Empresa%20versión%20inicial%20incorrecta%20-%20BigER.md)


>[!question] Pregunta
>	¿Es correcto el atributo `nombre` como clave primaria de la Entidad `Familiar`?

>[!question] Pregunta
>	¿Es correcto el atributo multivaluado `ubicaciones` en la Entidad `Departamento`?

Guarda mentalmente esta versión: cuando terminemos la sección 5 volveremos sobre ella y verás exactamente qué cosas estaban mal y por qué.
## 4: El Arte del Buen Diseño - Criterios y Decisiones Clave

Saber qué son las entidades, atributos y relaciones es solo la mitad del camino. La otra mitad es saber **cuándo y cómo usarlos correctamente**. Un buen diseñador de bases de datos es como un buen arquitecto: toma decisiones informadas para crear un modelo que sea sólido, lógico y fácil de entender. A continuación, exploramos los criterios más importantes.

### 4.1 ¿Entidad o Atributo? La Primera Gran Decisión 🤔

A menudo te encontrarás con un concepto y dudarás: ¿debería ser su propia entidad con un rectángulo en el diagrama, o simplemente un atributo (un óvalo) dentro de otra entidad?

**La regla de oro**: Será una **entidad** si necesitas almacenar información descriptiva sobre ese concepto. Si solo necesitas su valor, será un **atributo**.

- **Ejemplo**: Consideremos la "capital" de un `Pais`.
               
    - **Si necesitamos almacenar información descriptiva -> (Entidad)**: Si creamos una entidad `Ciudad` y la conectamos con `Pais` a través de una relación `es_capital`, ahora podemos almacenar todos los detalles que queramos sobre la capital (habitantes, extensión, etc.).
    
    - **Si solo su valor -> Atributo**: Si `capital` es solo un atributo de `Pais`, de la que no necesitamos más información, podemos dejarlo como atributo.
        

### 4.2 El Error Más Común: Usar Claves Ajenas como Atributos 🚫

Este es un **==error crítico en la fase de diseño conceptual==**. La regla es simple: **==el identificador de una entidad NUNCA debe aparecer como un atributo normal en otra entidad==**. La conexión entre ellas siempre debe ser una **relación**.

- **Ejemplo**: Queremos registrar qué profesor es el `responsable` de una `ASIGNATURA`.
    
    - **Mal diseño (Atributo)**: Incluir un atributo llamado `responsable` dentro de la entidad `Asignatura` donde guardaríamos el nif del profesor. Esto es incorrecto porque mezcla el nivel conceptual (¿quién es responsable?) con el nivel de implementación (guardar un nif). Este error se corresponde con el fallo grave **G2** de los criterios de evaluación (sección 8): merece la pena recordarlo desde ya.
        
    - **Buen diseño (Relación)**: Creamos una relación llamada `responsable` que conecta `Profesor` y `Asignatura`. Esto describe la realidad de forma mucho más clara y correcta.

### 4.3 Otros Criterios Fundamentales

1. **Estandarizar con Entidades**: Si un concepto, aunque sea solo un código o un nombre, se va a asociar con muchas otras entidades, es una buena práctica modelarlo como una entidad propia. Esto ayuda a estandarizar los valores y evitar errores de escritura. Por ejemplo, en lugar de tener un atributo `provincia` en la entidad `Municipio`, es mejor crear una entidad `Provincia` y relacionarlas.
    
2. **Evita la "Super Entidad"**: **==Es un error grave intentar modelar toda la organización (por ejemplo, la "Empresa") como una única entidad gigante==**. El objetivo es descomponer el minimundo en sus partes conceptuales más pequeñas y significativas.
    
3. **La Claridad es la Reina**: Usa siempre nombres adecuados, legibles y fáciles de entender para tus entidades, relaciones y atributos. Un diagrama bien nombrado se documenta a sí mismo. Piensa en quien tenga que leer tu modelo dentro de seis meses, incluso tú mismo puedes ser ese lector que ya no lo recuerde.

---
## 5: Entidades Débiles - Cuando una Entidad Necesita Ayuda para Identificarse

### 5.1 Entidades Fuertes vs. Débiles: La Diferencia Clave

Hasta ahora, todos los tipos de entidad que hemos visto son **fuertes** (o regulares). ¿Qué significa esto? Que tienen su propio **atributo clave** (como `dni` para `Empleado` o `isbn` para `Libro`) que les permite identificar de forma única a cada una de sus instancias. Son independientes y se valen por sí mismas.

Pero a veces, nos encontramos con entidades que no pueden ser identificadas de forma única solo con sus propios atributos. Estas se conocen como **tipos de entidad débiles**.

 ** Veamos qué ocurre con Empleado y Familiar:**
 
 - **Entidad Fuerte:**  La entidad `Empleado` es una **entidad fuerte** porque posee un atributo clave, el `dni`, que la identifica de forma única en toda la empresa. Cada empleado tiene un dni y no hay dos iguales.
 - **Entidad Débil:** En cambio, en la primera versión de la Empresa, `Familiar` es un ejemplo perfecto de una **entidad débil**. Si intentamos usar su atributo `nombre` como clave, nos topamos con un problema evidente: es muy probable que varios empleados tengan familiares con el mismo nombre, como "María García" o "Carlos Sánchez". Por sí solo, el `nombre` no garantiza la unicidad en toda la base de datos. Para resolver esto, la entidad `Familiar` se apoya en su entidad propietaria (`Empleado`). Su identificador único y completo no es solo su `nombre` (que actúa como clave parcial), sino la combinación de su clave parcial con la clave de su "dueño". Así, la identidad inequívoca de un familiar es la combinación del **`dni` del empleado** + el **`nombre` del familiar**.

En definitiva, una entidad débil necesita "apoyarse" en otra entidad para poder ser identificada 🤝. 

---
### 5.2 Las dos condiciones a cumplir: Existencia e Identificación

Para que una entidad sea débil, debe cumplir con dos condiciones muy específicas.
#### A) Dependencia de Existencia

Esto significa que una entidad no puede existir si la entidad de la que depende desaparece.

Pensemos en la entidad `Familiar`. Es evidente que un familiar solo tiene sentido en nuestra base de datos si está asociado a un `Empleado`. Si un empleado deja la empresa y eliminamos su registro, los registros de sus familiares a cargo ya no tienen razón de ser. Esto es una clara **dependencia de existencia**.

**¡Pero Cuidado!** Este es solo el primer paso. Tener dependencia de existencia no convierte automáticamente a una entidad en débil. Necesita cumplir la segunda condición, que es la más importante.
#### B) Dependencia de Identificación

Esta es la verdadera marca de una entidad débil. Significa que, además de depender para existir, **no se puede identificar de forma única por sí misma** usando solo sus propios atributos.

Volvamos a la entidad `Familiar`. Como ya vimos, su atributo `nombre` no es suficiente para identificar a una persona de forma inequívoca en toda la empresa, ya que podría haber muchos familiares con el mismo nombre. Para encontrar a un familiar específico (por ejemplo, "Ana García"), necesitamos preguntar: "¿Ana García, familiar de qué empleado?".

La identidad única solo se consigue al combinar la clave de su entidad "dueña" (`dni` del `Empleado`) con su propio atributo discriminante (`nombre`). Esta necesidad de "tomar prestada" la clave de la entidad fuerte para poder identificarse es lo que se conoce como **dependencia de identificación**. Es esta segunda condición la que convierte a `Familiar` en una entidad débil.

---
### 5.3 La Anatomía de una Entidad Débil

Una entidad débil siempre viene acompañada de dos elementos que la definen:

1. **Entidad Propietaria (o Fuerte)**: Es la entidad de la que depende (ej: `Empleado`).
    
2. **Relación débil de Identificación**: la conexión entre una entidad fuerte y su entidad débil **siempre** es mediante una relación, también denominada débil, que tendrá cardinalidad  `(1,1)` en el lado de la entidad fuerte y `(0,N)` o `(1,N)` en el lado de la entidad débil. Es decir, una instancia de la entidad débil no puede existir sin su entidad fuerte de la que depende y solo tiene una. Mientras que una instancia de la entidad fuerte puede no estar relacionada con ninguna de la entidad débil, con una o con muchas.
    
3. **Clave Parcial (o Discriminante)**: Es el atributo (o conjunto de atributos) dentro de la entidad débil que la identifica de forma única **entre sus hermanas**, es decir, entre todas las que dependen del mismo propietario. En nuestro ejemplo, el `Nombre` del `Familiar` es la clave parcial.
    
**Clave Primaria de la Entidad Débil**: La clave primaria completa de una entidad débil se forma combinando la **clave primaria de su entidad propietaria** con su propia **clave parcial**.

---

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
>
> | Concepto               | Textual en BigER                                                                                                                                           | Notación Crow's foot en BigER                        |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Entidad débil  | `weak entity` **Entidad_Debil** {<br>clave_parcial `partial-key`<br>..<br>}                                                                                | ![](../imgs/BD%20-%20BigER%20Entidad%20Débil.png)  |
| Relación débil | `weak relationship` **relacion_debil** {<br>Entidad[`1..1`] -> Entidad_Debil[`0..N`]<br>//dependiendo del caso, también puede ser Entidad_Debil[`1..N`]<br>} | ![](../imgs/BD%20-%20BigER%20Relación%20débil.png) |


---
### 5.4 Casos de Uso de Entidades Débiles

Aunque el concepto puede parecer complejo, las entidades débiles son la solución elegante a varios problemas de modelado muy comunes. Sin embargo, no se debe hacer un mal uso de ellas: **solo es correcto usarlas** en estos casos:

#### 5.4.1 Caso 1: Para modelar atributos multivalor de una entidad

Queremos modelar `Curso` y las distintas repeticiones de dicho curso a lo largo del tiempo en un atributo `edicion`.
Además, cada edición tomará sus propios valores en `fecha_inicio`, `fecha_fin` y `aula`. 
No podemos modelarlo como se ve a continuación

>[!warning] ⚠️ Modelado erróneo:

| BigER                                                                                                   | Crow's foot                                              |
| ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| `entity` Curso {<br>codigo `key`<br>nombre<br>¿edicion?<br>¿fecha_inicio?<br>¿fecha_fin?<br>¿aula?<br>} | ![](../imgs/BD%20-%20BigER%20Entidad%20Curso%20Foot.png) |
**Solución**: El posible atributo multivalor `edicion` hay que convertirlo en la entidad débil `Edicion` que depende de `Curso`. Su clave parcial puede ser `fecha_inicio` si sabemos que no hay dos o más ediciones que comiencen en la misma fecha. Así, podemos almacenar toda la información de cada edición de forma ordenada.

>[!tip] Modelado correcto:

| BigER                                                                                                                                                                                                                            | Crow's foot                                                      |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| ```entity``` Curso {<br>codigo ```key```<br>nombre<br>}<br>==weak entity== Edicion {<br>fecha_inicio ==partial-key==<br>fecha_fin<br>aula<br>}<br>==weak relationship== tiene {<br>Curso[```1..1```] -> Edicion[```0..N```]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Edicion%20Crows%20Foot.png) |
#### 5.4.2 Caso 2: Para gestionar atributos multivaluados en una relación M:N con repetición de relaciones entre instancias.

En el mundo real, las relaciones no son estáticas; muchas cambian con el tiempo. Un empleado cambia de departamento, un médico deja de estar adscrito a un hospital, un proveedor suministra la misma pieza a un proyecto en diferentes fechas. Hasta ahora sabemos cómo hacer la "foto" actual, pero ¿cómo guardamos el **álbum de fotos completo**?

Por ejemplo, un `Profesor` imparte una `Asignatura`. Esta es una relación M:N. Ahora queremos registrar en qué **año académico (`curso`)** se impartió cada docencia. El problema es que un profesor puede impartir la asignatura de "BD" en múltiples cursos (20/21, 22/23, 23/24). Es decir, el mismo par (Profesor, Asignatura) se puede repetir por lo que el atributo curso de la relación `imparte` realmente sería multivaluado.

>[!warning] ⚠️ Modelado erróneo:

| BigER                                                                                                                                                                                                                               | Crow's foot                                                            |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `entity` Profesor {<br>nif `key`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Asignatura {<br>codigo `key`<br>nombre<br>}<br><br>```relationship``` imparte {<br>Profesor[`0..N`] -> Asignatura[`0..N`]<br>curso<br>} | ![](../imgs/BD%20-%20BigER%20Profesor%20Asignatura%20Crows%20Foot.png) |
**Solución**: la solución es un patrón de diseño muy potente: "reificar" la relación. Esto significa convertir la interacción a lo largo del tiempo en una **entidad débil con repetición** (o histórica). Esta nueva entidad guardará cada "evento" de la relación, y su clave primaria casi siempre incluirá un atributo de tiempo (como `curso`o `fecha`).

En nuestro ejemplo de un `Profesor` imparte una `Asignatura` reificamos" la relación, es decir, la convertimos en una entidad débil llamada `Docencia`. Esta entidad depende de `Profesor` y `Asignatura`, y su clave parcial es `curso`. Su clave completa sería:
	- `nif` por la relación débil que tiene con Profesor
	- `codigo` por la relación débil que tiene con Asignatura
	- `curso` la clave parcial que aporta Docencia
Esta clave compuesta por 3 atributos ahora sí es única.

>[!tip] Modelado correcto:

| BigER                                                                                                                                                                                                                                                                                                                                                                            | Crow's foot                                                       |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `entity` Profesor {<br>nif `key`<br>nombre<br>apellido_1<br>apellido_2<br>}<br>`entity` Asignatura {<br>codigo `key`<br>nombre<br>}<br><br>==weak entity== Docencia {<br>curso ==partial-key==<br>}<br><br>==weak relationship== imparte {<br>Profesor[`1..1`] -> Docencia[`0..N`]<br>}<br><br>==weak relationship== relativa_a {<br>Docencia[`0..N`] -> Asignatura[`1..1`]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Docencia%20Crows%20Foot.png) |

Sin embargo, la clave para un diseño correcto es decidir de qué entidad o entidades debe depender esta nueva entidad histórica. La respuesta depende de la cardinalidad de la relación **en un instante de tiempo**. Analicemos los tres casos posibles.

---
##### 5.4.2.1 Caso 2.1: Dependencia Doble

- **La Regla de Negocio**: En una fecha concreta, una instancia de la Entidad_1 puede relacionarse con **varias** instancias de la Entidad_2, y viceversa.
    
- **Análisis**: Para identificar un evento único, no basta con saber `(Entidad_1, Fecha)`, porque en esa fecha podría estar relacionada con varias Entidad_2. Tampoco basta con `(Entidad_2, Fecha)`. Necesitamos la combinación de los tres elementos para garantizar la unicidad.
    
- **Solución de Diseño**: La entidad débil histórica **debe depender de ambas entidades propietarias**. Es el caso que se ve en el ejemplo que ya hemos visto de un `Profesor` imparte una `Asignatura`.

- **Error a Evitar**: Hacerla depender de una sola entidad.    
###### Ejemplo 5.4.2.1.1: Matrículas en una Universidad

- **Entidades**: `Estudiante`, `Asignatura`.
    
- **Contexto Histórico**: Necesitamos guardar un registro de qué estudiantes se han matriculado en qué asignaturas a lo largo de los diferentes cursos académicos.
    
- **Regla de Negocio**: En un curso académico concreto, un `Estudiante` se matricula en **varias instancias** de `Asignatura`. A su vez, una `Asignatura` tiene **varias instancias** de `Estudiante` matriculados en ese mismo año.
    
- **Análisis de Unicidad**: Para identificar una matrícula específica, ¿es suficiente con `(numero_expediente, curso_academico)`? No, porque ese estudiante cursó varias asignaturas ese año. ¿Y con `(codigo, curso_academico)`? Tampoco, porque en esa asignatura había muchos alumnos. Para identificar de forma inequívoca una única matrícula, necesitamos saber **qué estudiante**, en **qué asignatura** y en **qué curso**.
    
- **Solución de Diseño**:
    
    1. Creamos una entidad débil llamada `Matricula`.
        
    2. Esta entidad débil debe depender tanto de `Estudiante` como de `Asignatura`.
        
    3. Su clave primaria se forma con las claves de ambas propietarias más el atributo de tiempo: 
	    - `numero_expediente` por la relación débil que tiene con `Estudiante`
	    - `codigo` por la relación débil que tiene con `Asignatura`
	    - `curso_academico` la clave parcial que aporta `Matricula`

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Crow's foot                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| ```entity``` Estudiante {<br>numero_expediente `key`<br>dni `//UNIQUE`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Asignatura {<br>codigo ```key```<br>nombre<br>}<br><br>==weak entity== Matricula {<br>curso_academico ==partial-key==<br>}<br><br>==weak relationship== ha_realizado {<br>Estudiante[`1..1`] -> Matricula[`0..N`]<br>}<br><br>==weak relationship== corresponde_a {<br>Matricula[`0..N`] -> Asignatura[`1..1`]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Matricula%20Crows%20Foot.png) |
###### Ejemplo 5.4.2.1.2: Reservas de Vuelos

- **Entidades**: `Pasajero`, `Vuelo`.
    
- **Contexto Histórico**: Una aerolínea necesita un registro de cada reserva individual.
    
- **Regla de Negocio**: En una transacción de reserva realizada en una `Fecha_Reserva` específica, un `Pasajero` (o un grupo de ellos bajo el mismo localizador) se reserva en **varios** `VueloS` (ej: un viaje con escalas). A su vez, un `Vuelo` tiene **varios** `Pasajeros` a bordo.
    
- **Análisis de Unicidad**: Una reserva concreta no puede ser identificada solo por `(ID_Pasajero, Fecha_Reserva)` si el pasajero reservó un viaje con múltiples vuelos. Tampoco por `(ID_Vuelo, Fecha_Reserva)`, ya que en ese vuelo hay muchos pasajeros. La reserva es la combinación de un pasajero específico en un vuelo específico en un momento dado.
    
- **Solución de Diseño**:
    
    1. Creamos la entidad débil histórica `Reserva`.
        
    2. `Reserva` depende de `Pasajero` y `Vuelo`.
        
    3. Su clave primaria es la combinación de las tres piezas de información:
	    - `id_pasajero` por la relación débil que tiene con `Pasajero`
	    - `id_vuelo` por la relación débil que tiene con `Vuelo`
	    - `fecha_reserva` la clave parcial que aporta `Reserva`

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Crow's foot                                                      |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| ```entity``` Pasajero {<br>id_pasajero ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>```entity``` Vuelo {<br>id_vuelo ```key```<br>origen<br>destino<br>fecha_hora_salida<br>}<br><br>==weak entity== Reserva {<br>fecha_reserva ==partial-key==<br>}<br><br>==weak relationship== es_realizada_por {<br>Pasajero[```1..1```] -> Reserva[```1..N```]<br>}<br><br>==weak relationship== incluye {<br>Vuelo[```1..1```] -> Reserva[```0..N```]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Reserva%20Crows%20Foot.png) |

---

##### 5.4.2.2 Caso 2.2: Dependencia Simple a Elegir

- **La Regla de Negocio**: En una fecha concreta, una instancia de la Entidad1 solo puede relacionarse con **una** instancia de la Entidad2, y viceversa.
    
- **Análisis**: Dado que la conexión es única por ambos lados en cualquier fecha, tanto `(Entidad1, Fecha)` como `(Entidad2, Fecha)` son combinaciones que identifican unívocamente el evento.
    
- **Solución de Diseño**: La entidad débil histórica puede depender **o de la Entidad1 o de la Entidad2**. La elección depende del significado del negocio.

- **Error a Evitar**: Hacerla depender de ambas entidades a la vez.
    
###### Ejemplo 5.4.2.2.1: Asignación de Coche de Empresa

- **Entidades**: `Empleado`, `Vehiculo`.
    
- **Contexto Histórico**: La empresa necesita un historial de qué empleado ha tenido asignado cada coche de la flota a lo largo del tiempo.
    
- **Regla de Negocio**: En una `fecha_asignacion` concreta, un `Empleado` solo puede tener asignado **un** `Vehiculo` de la empresa. A su vez, un `Vehiculo` solo puede estar asignado a **un** `Empleado` en esa fecha.
    
- **Análisis de Unicidad**: Si conocemos al `Empleado` y la `fecha_asignacion`, sabemos inequívocamente qué coche tenía. Del mismo modo, si conocemos el `Vehiculo` y la `fecha_asignacion`, sabemos quién lo conducía. Ambos pares, `(id_empleado, fecha_asignacion)` y `(matricula, fecha_asignacion)`, son únicos.
    
- **Solución de Diseño**:
    
    1. Creamos la entidad débil `Asignacion_Historica`.
        
    2. Podemos elegir de quien depende. 
	    1. Si el foco del sistema es el historial del empleado, la hacemos depender de `Empleado`. 
	    2. Si el foco es el historial del vehículo, la hacemos depender de `Vehiculo`.
        
    3. Su clave primaria es la combinación de dos piezas de información:
	    1. **Opción A (foco en Empleado)**: 
		    - `id_empleado` por la relación débil que tiene con `Empleado`
		    - `fecha_asignacion` la clave parcial que aporta `Asignacion_Historica`
	
	    2. **Opción B (foco en Vehículo)**: 
		    - `matricula` por la relación débil que tiene con  `Vehiculo`
		    - `fecha_asignacion` la clave parcial que aporta `Asignacion_Historica`

**Opción A (foco en Empleado)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Crow's foot                                                                     |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `entity` Empleado {<br>id_empleado `key`<br>dni `//UNIQUE`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Vehiculo {<br>matricula `key`<br>marca<br>modelo<br>}<br><br>==weak entity== Asignacion_Historica {<br>fecha_asignacion ==partial-key==<br>}<br><br>==weak relationship== ha_tenido {<br>Empleado[`1..1`] -> Asignacion_Historica[`0..N`]<br>}<br><br>```relationship``` incluye {<br>Vehiculo[`1..1`] -> Asignacion_Historica[`0..N`]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Asignacion%20Historica%20Crows%20Foot.png) |

   **Opción B (foco en Vehículo)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                         | Crow's foot                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `entity` Empleado {<br>id_empleado `key`<br>dni `//UNIQUE`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Vehiculo {<br>matricula `key`<br>marca<br>modelo<br>}<br><br>==weak entity== Asignacion_Historica {<br>fecha_asignacion ==partial-key==<br>}<br><br>`relationship` ha_tenido {<br>Empleado[`1..1`] -> Asignacion_Historica[`0..N`]<br>}<br><br>==weak relationship== incluye {<br>Vehiculo[`1..1`] -> Asignacion_Historica[`0..N`]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20AsignacionHistorica2%20Crows%20Foot.png) |

###### Ejemplo 5.4.2.2.2: Gestor de Clientes VIP

- **Entidades**: `Gestor_Cuentas`, `Cliente_Vip`.
    
- **Contexto Histórico**: Una empresa de servicios premium asigna un gestor de cuentas exclusivo a cada uno de sus clientes más importantes. Esta asignación se revisa y puede cambiar cada trimestre fiscal. Se necesita un historial de estas asignaciones.
    
- **Regla de Negocio**: Durante un `trimestre` específico (ej: "2026-T3"), un `Gestor_Cuentas` solo puede ser el responsable principal de **un** `Cliente_Vip`, y un `Cliente_Vip` solo tiene asignado a **un** `Gestor_Cuentas` principal. 
    
- **Análisis de Unicidad**: Si conocemos al `Gestor_Cuentas` y el `trimestre`, sabemos de qué cliente era responsable. De la misma manera, si conocemos al `Cliente_Vip` y el `trimestre`, sabemos quién era su gestor asignado. Ambas perspectivas son válidas y únicas.
    
- **Solución de Diseño**:
    
    1. Creamos la entidad débil `Asignacion_Vip`.
        
    2. Podemos elegir de quien depende. 
	    1. Si el foco del sistema es la **cartera de clientes de cada gestor**, la hacemos depender de `Gestor_Cuentas`. 
	    2. Si el foco es el el **historial de gestores que ha tenido un cliente**, la entidad dependerá de `Cliente_Vip`.
                
    3. Su clave primaria es la combinación de dos piezas de información:
	    1. **Opción A (foco en Gestor)**: 
		    - `id_gestor` por la relación débil que tiene con `Gestor_Cuentas`
		    - `trimestre` la clave parcial que aporta `Asignacion_Vip`
	
	    2. **Opción B (foco en Vehículo)**: 
		    - `id_cliente_vip` por la relación débil que tiene con  `Cliente_Vip`
		    - `trimestre` la clave parcial que aporta `Asignacion_Vip`
        
**Opción A (foco en Gestor)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Crow's foot                                                                |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| `entity` Gestor_Cuentas {<br>id_gestor `key`<br>dni `//UNIQUE`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Cliente_Vip {<br>id_cliente_vip `key`<br>nombre_empresa<br>}<br><br>==weak entity== Asignacion_Vip {<br>trimestre ==partial-key==<br>}<br><br>==weak relationship== es_responsable_de {<br>Gestor_Cuentas[`1..1`] -> Asignacion_Vip[`1..N`]<br>}<br><br>`relationship` asigna_a{<br>Asignacion_Vip[`1..N`] -><br>Cliente_Vip[`1..1`]  <br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Asignacion%20VIP2%20Crows%20Foot.png) |

   **Opción B (foco en Cliente)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Crow's foot                                                               |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `entity` Gestor_Cuentas {<br>id_gestor `key`<br>dni `//UNIQUE`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Cliente_Vip {<br>id_cliente_vip `key`<br>nombre_empresa<br>}<br><br>==weak entity== Asignacion_Vip {<br>trimestre ==partial-key==<br>}<br><br>`relationship` es_responsable_de {<br>Gestor_Cuentas[`1..1`] -> Asignacion_Vip[`1..N`]<br>}<br><br>==weak relationship== asigna_a{<br>Asignacion_Vip[`1..N`] -><br>Cliente_Vip[`1..1`]  <br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Asignacion%20VIP%20Crows%20Foot.png) |

---
##### 5.4.2.3 Caso 2.3: Dependencia Simple Obligatoria

- **La Regla de Negocio**: En una fecha concreta, una instancia de la Entidad1 solo puede relacionarse con **una** instancia de la Entidad2, pero una instancia de la Entidad2 puede relacionarse con **varias** de la Entidad1.
    
- **Análisis**: Debemos encontrar el lado de la relación que garantiza la unicidad cuando se combina con la fecha. El lado "1" de la relación es el que nos sirve.
    
- **Solución de Diseño**: La entidad débil histórica **debe depender obligatoriamente de la Entidad1**, es decir, la que solo puede tener una relación en esa fecha.

- **Error a Evitar**: hacerla depender de la Entidad2, es decir, de la que puede tener varias relaciones en esa fecha, o hacerla depender de ambas.
    
###### Ejemplo 5.4.2.3.1: Asignación Departamental de Empleados

- **Entidades**: `Empleado`, `Departamento`.
    
- **Contexto Histórico**: Recursos Humanos necesita el historial completo de los departamentos por los que ha pasado cada empleado.
    
- **Regla de Negocio**: En una `fecha_inicio` concreta, un `Empleado` solo puede pertenecer a **un** `Departamento`. Sin embargo, un `Departamento` está formado por **varios** `Empleados` en esa misma fecha.
    
- **Análisis de Unicidad**:
    
    - ¿El par `(id_empleado, fecha_inicio)` es único? **Sí**. Nos dice exactamente en qué departamento empezó a trabajar ese empleado en esa fecha.
        
    - ¿El par `(id_dpto, fecha_inicio)` es único? **No**. En esa fecha, varios empleados pudieron empezar a trabajar en ese mismo departamento.
        
- **Solución de Diseño**:
    
    1. Creamos la entidad débil `Historial_Departamento`.
        
    2. La dependencia es forzada. Debe depender de `Empleado`.
                  
    3. Su clave primaria es la combinación de dos piezas de información:
	    - `id_empleado` por la relación débil que tiene con `Empleado`
	    - `fecha_inicio` la clave parcial que aporta `Historial_Departamento`
        

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Crow's foot                                                               |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `entity` Empleado {<br>id_empleado `key`<br>dni `//UNIQUE`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>`entity` Departamento {<br>id_departamento `key`<br>nombre<br>}<br><br>==weak entity== Historial_Departamento {<br>fecha_inicio ==partial-key==<br>}<br><br>==weak relationship== ha_trabajado {<br>Empleado[`1..1`] -> Historial_Departamento[`0..N`]<br>}<br><br>`relationship` pertenece_a {<br>Historial_Departamento[`0..N`] -> Departamento[`1..1`]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Historial%20Dpto%20Crows%20Foot.png) |
###### Ejemplo 5.4.2.3.2: Asignación de Tutor Académico 

- **Entidades**: `Estudiante`, `Tutor`.
    
- **Contexto Histórico**: Una universidad necesita mantener un historial de qué `Tutor` ha sido asignado a cada `Estudiante` a lo largo de los diferentes semestres.
    
- **Regla de Negocio**: En un `semestre` concreto, un `Estudiante` solo puede tener asignado a **un** `Tutor` académico. Sin embargo, un `Tutor` puede ser responsable de **varios** `Estudiantes` en ese mismo semestre.
    

**Análisis de Unicidad**:

- ¿El par `(id_estudiante, semestre)` es único? **Sí**. Identifica de forma inequívoca quién era el tutor de ese estudiante en ese semestre específico.
    
- ¿El par `(id_tutor, semestre)` es único? **No**. En un semestre, un tutor tiene a su cargo a múltiples estudiantes, por lo que esta combinación se repetiría.
    

**Solución de Diseño**:

1. Creamos la entidad débil `Asignacion_Tutoria`.
    
2. La dependencia es obligatoria y debe ser sobre la entidad `Estudiante`, que es el lado "N" de la relación.

3. Su clave primaria es la combinación de dos piezas de información:
	    - `id_estudiante` por la relación débil que tiene con `Estudiante`
	    - `semestre` la clave parcial que aporta `Asignacion_tutoria`

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Crow's foot                                                                   |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `entity` Estudiante {<br>id_estudiante `key`<br>nombre<br>apellido_1<br>apellido2<br>}<br><br>`entity` Tutor {<br>id_tutor `key`<br>nombre<br>apellido_1<br>apellido_2<br>}<br><br>==weak entity== Asignacion_Tutoria {<br>semestre `partial-key`<br>}<br><br>==weak relationship== tiene_asignado {<br>Estudiante[`1..1`] -> Asignacion_Tutoria[`0..N`]<br>}<br><br>`relationship` es_realizada_por {<br>Asignacion_Tutoria[`0..N`] -> Tutor[`1..1`]<br>} | ![](../imgs/BD%20-%20BigER%20Debil%20Asignacion%20Tutoria%20Crows%20Foot.png) |

---
### 5.5: Un caso especial: las entidades asociativas para  relacionar una entidad con una  relación M:N.

El modelo Entidad-Relación tiene una regla fundamental e inquebrantable: **es imposible conectar usando una relación a una otra relación directamente con una entidad**. Un verbo no puede ser el sujeto de otra acción. Pero, en el modelado de datos, a veces nos encontramos que una interacción de **muchos-a-muchos (M:N) sin repetición** es tan importante que necesitamos conectarla con otras entidades.

#### El Problema Guiado: Asignación de Competencias a Puestos de Trabajo

**El Escenario Inicial:** En el departamento de Recursos Humanos, se definen los `Puestos_de_Trabajo` y un catálogo de `Competencias` profesionales. La relación entre ellos es de muchos-a-muchos (M:N):

- Un `Puesto_de_Trabajo` (ej: "Analista de Datos") requiere **varias** `Competencias` (ej: "SQL Avanzado", "Visualización de Datos").
    
- Una `Competencia` (ej: "Liderazgo de Equipos") es requerida por **varios** `Puestos_de_Trabajo`.
    

La regla de negocio es que una competencia se asigna una sola vez a cada puesto. Este es el modelo inicial:

**El Nuevo Requisito (El Muro Conceptual):** Ahora, RRHH necesita que cada una de estas asignaciones sea certificada. Es decir, se debe registrar qué `Validador` (un directivo o experto) ha aprobado que la competencia "SQL Avanzado" es necesaria para el puesto "Analista de Datos".

El `Validador` no certifica el puesto en general, ni la competencia en general, sino la **regla de asignación** entre ambos. Si intentamos conectar la entidad `Validador` directamente a la relación "requiere", nos topamos con el muro conceptual del modelo E/R: **es imposible conectar una relación con otra relación **.

---
#### La Solución: "Cosificar" la Relación en una Entidad Asociativa

La solución es transformar la relación "requiere" en una entidad asociativa que actúe como un conector.

Una entidad asociativa es, por tanto, un híbrido: nace de una relación M:N para darle cuerpo y permitir que se conecte con otras partes del modelo. Se comporta como una **tabla `join` en el mundo real**, existiendo únicamente para conectar dos conceptos y cuya identidad es la combinación de las dos cosas que conecta.

**Paso 1: Convertir la Relación en una Entidad (el Puente)** Transformamos la relación en una entidad. Es decir, la relación M:N se convierte en la entidad asociativa `Requisito_de_Puesto`. Cada instancia de esta nueva entidad representa el hecho único y no repetible de que un puesto específico requiere una competencia específica.

**Paso 2: Analizar la Identidad del Puente (La Clave Compuesta)** Esta nueva entidad `Requisito_de_Puesto` **no aporta ningún identificador propio**. Es, por naturaleza, una entidad débil. Su única identidad es la combinación de las claves de las entidades que une. No necesita una clave parcial propia porque la relación no se repite; un puesto solo requiere una competencia una vez.

- **Clave Primaria 🔑 = {id_puesto (FK), id_competencia (FK)}**
    

**Paso 3: Usar el Puente para Conectar** Ahora que `Requisito_de_Puesto` es una entidad, ya podemos conectarla con `Validador` a través de una nueva relación binaria, `es_validado_por`.

**El Modelo Final y su Significado:** El diagrama final representa la realidad de forma lógica y correcta:

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: Se define un `Requisito_de_Puesto` (un puesto de trabajo necesita una competencia).
    
2. **Hecho 2**: Ese `Requisito` específico es certificado por un `Validador`.


>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
> | Concepto                     | BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Entidad asociativa en Big ER | entity ```Entidad_1``` {<br>id_1 ```key```<br>atributos_de_1<br>}<br>entity ```Entidad_2``` {<br>id_2 ```key```<br>atributos_de_2<br>}<br><br>// Entidad asociativa que "cosifica" la relación M:N<br>// Su clave primaria es la combinación de id_1 e id_2<br>//**No es soportada por BigER, lo que haremos es comenzar siempre el nombre de este tipo de entidades con Asoc_ y pondremos los dos atributos claves de las entidades que asocia como claves de la entidad asociativa**<br>```entity``` ==Asoc_Entidad_12 =={<br>==id_1_ key==<br>==id_2 key==<br>}<br><br>// Las relación original M:N ahora son dos relaciones 1:N hacia la entidad asociativa.<br>relationship entidad1_participa_en {<br>Entidad_1[```1..1```] -> ==Asoc_Entidad_12==[```0..N```]<br>}<br><br>relationship entidad2_participa_en {<br>Entidad_2[```1..1```] -> ==Asoc_Entidad_12==[```0..N```]<br>}<br><br>// Una tercera entidad que necesita relacionarse con la *interacción* entre A y B.<br>```entity``` ENTIDAD_EXTERNA {<br>id_entidad_a_relacionar ```key```<br>atributos_de_entidad_a_relacionar<br>}  <br><br>// Ahora, la entidad asociativa, al ser una entidad, puede participar en otras relaciones.<br>relationship relacion_entidad_externa_entidad_asociativa {<br>==Asoc_Entidad_12==[```0..N```] -> ENTIDAD_EXTERNA[```1..1```]<br>} |
|                              | **En el diagrama generado por BigER lo veremos gráficamente como una entidad normal**<br>![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Crows%20Foot.png)<br>**Cuando lo dibujemos a mano, pondremos un rombo dentro del rectángulo de la entidad asociativa**<br>![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Rombo%20Crows%20Foot.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

Ahora ya podemos modelar correctamente el ejemplo:

| BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `entity` PUESTO_DE_TRABAJO {<br>id_puesto `key`<br>nombre_puesto<br>}<br><br>`entity` Competencia {<br>id_competencia `key`<br>nombre_competencia<br>}<br><br>// Entidad asociativa que "cosifica" la relación M:N<br>// Su clave primaria es la combinación de id_puesto e id_competencia<br>//**No es soportada por BigER, lo que haremos es comenzar siempre el nombre de este tipo de entidades con Asoc_ y pondremos los dos atributos claves de las entidades que asocia como claves de la entidad asociativa**<br>`entity` ==Asoc_Requisito_de_Puesto== {<br>==id_puesto key==<br>==id_competencia key==<br>}<br><br>// Relaciones que forman la entidad asociativa<br>`relationship` requiere {<br>PUESTO_DE_TRABAJO[`1..1`] -> ==Asoc_Requisito_de_Puesto==[`1..N`]<br>}<br><br>```relationship``` es_requerida_por {<br>Competencia[`1..1`] -> ==Asoc_Requisito_de_Puesto==[`1..N`]<br>}<br><br>// La entidad asociativa ahora puede relacionarse con otras entidades<br><br>`entity` Validador {<br>id_validador `key`<br>rol_empresa<br>}<br><br>```relationship``` validada_por {<br>==Asoc_Requisito_de_Puesto==[`0..N`] -> Validador[`1..1`]<br>} |
| **En el diagrama generado por BigER lo veremos gráficamente como una entidad normal**<br><br> ![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Crows%20Foot.png)<br><br>**Cuando lo dibujemos a mano, pondremos un rombo dentro del rectángulo de la entidad asociativa**<br><br>![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Rombo%20Crows%20Foot.png)<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
**El Modelo Final y su Significado:** El diagrama final representa la realidad de forma lógica y correcta:

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: Se define un `Requisito_de_Puesto` (un puesto de trabajo necesita una competencia).
    
2. **Hecho 2**: Ese `Requisito` específico es certificado por un `Validador`.

#### Nota Importante: La Ausencia de Repetición en el Tiempo

Es crucial entender que este patrón funciona porque asumimos que la relación M:N **no se repite**. Un puesto requiere una competencia una sola vez. Si la relación pudiera repetirse (por ejemplo, si se revisaran las competencias de los puestos cada año y quisiéramos guardar el histórico), la entidad `Requisito_de_Puesto` necesitaría un atributo adicional en su clave (`Año`) para distinguir las repeticiones. En ese momento, estaríamos en el caso de **modelar una relación M:N con repetición de relaciones entre instancias**, donde en este caso aplicaríamos lo que hemos visto sobre entidades y relaciones débiles que hemos visto anteriormente.

---
### 5.6 Versión final correcta del Diagrama ER de Empresa

Con los nuevos conocimientos, ya podemos hacer el diagrama ER de Empresa completamente correcto.

![BD Empresa versión final - BigER](../imgs/BD%20Empresa%20versión%20final%20Crows%20Foot.png)
>[!exercise] Ejercicio
> Prueba la solución propuesta en  [BigER]( https://marketplace.visualstudio.com/items?itemName=BIGModelingTools.erdiagram) en Visual Code y prueba a mostrar la solución con las distintas notaciones además de en Crow's foot. Intenta ver las diferencias más significativas entre cada notación soportada.


---
## 6: Relaciones de Grado Superior (N-arias)

### 6.1 El Concepto Clave: El "Hecho Indivisible"

Hasta ahora, todas las relaciones que hemos modelado han sido **binarias** (conectan dos entidades). Sin embargo, a veces nos encontramos con "hechos" o "eventos" que unen a tres o más entidades al mismo tiempo. A estas las llamamos relaciones **n-arias** (o **ternarias**, si son de grado 3).

El error más grave y común es pensar que una relación ternaria es simplemente un "atajo" para dibujar tres relaciones binarias. **No lo son. No son equivalentes.**

> **Analogía de la Videoconferencia 📞:**
> 
> - **Tres Relaciones Binarias:**
>     
>     1. Tú tienes una videoconferencia con Ana (Relación 1).
>         
>     2. Por separado, Ana tiene una videoconferencia con Carlos (Relación 2).
>         
>     3. Por separado, tú tienes una videoconferencia con Carlos (Relación 3).
>         
>         Son tres eventos distintos.
>         
> - **Una Relación Ternaria:**
>     
>     - Tú, Ana y Carlos estáis en una videoconferencia al mismo tiempo.
>         
>         Es un único evento indivisible que os une a los tres.
>         

Una relación ternaria **solo** debe usarse en ese segundo caso: cuando el hecho que registras requiere que todos los participantes existan simultáneamente para tener sentido.

---

### 6.2 Caso de Uso Correcto: La Verdadera Relación Ternaria

Usamos una relación ternaria cuando existe un atributo descriptivo (como `Cantidad`, `Precio`, `Nota`) que no pertenece a ninguna de las entidades ni a ninguna pareja de ellas, sino **única y exclusivamente a la combinación de todas**.

#### Ejemplo: La Relación `SUMINISTRA`

Imagina que queremos modelar una cadena de suministro. Tenemos tres entidades:

- `Proveedor` (ej: "Tornillos Acme")
    
- `Proyecto` (ej: "Construcción Puente A")
    
- `Repuesto` (ej: "Tornillo M8")
    

Y necesitamos registrar la `Cantidad` de repuestos que un proveedor suministra a un proyecto.

**La Prueba del Atributo:** ¿Dónde ponemos el atributo `Cantidad`?

1. ¿En una relación `Proveedor`-`Repuesto`? No. "Tornillos Acme" no suministra la misma cantidad de "Tornillo M8" a todos sus proyectos.
    
2. ¿En una relación `Proyecto`-`Repuesto`? No. El "Puente A" no recibe todos sus "Tornillos M8" del mismo proveedor.
    
3. ¿En una relación `Proveedor`-`Proyecto`? No. "Tornillos Acme" suministra muchos tipos de repuestos diferentes al "Puente A".
    

La `Cantidad` **solo tiene sentido** cuando conocemos a los tres participantes a la vez. Describe el hecho indivisible:

> "El `Proveedor` 'Tornillos Acme' suministró una `Cantidad` de 5000 'Tornillos M8' al `Proyecto` 'Puente A'."

Este es un evento ternario. Si intentáramos "descomponer" esto en tres relaciones binarias, perderíamos el hecho central. Registraríamos que el proveedor vende esa pieza, que el proyecto la usa y que el proveedor trabaja en el proyecto, pero **no podríamos saber** si ese proveedor suministró _esa pieza_ a _ese proyecto_.

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> | Concepto          | BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Relación ternaria | ```entity``` Entidad_1 {<br>id1 ```key```<br>}<br>```entity``` Entidad_2 {<br>id2 ```key```<br>}<br>```entity``` Entidad_3 {<br>id3 ```key```<br>}<br>// simplemente añadimos una nueva flecha y la tercera entidad a relacionar junto con su cardinalidad mínima y máxima con respecto al otro par de entidades<br>```relationship``` relacion_ternaria {<br>Entidad_1[```1..N```] ==->== Entidad_2[```1..N```] ==->== Entidad_3[```1..N```]<br>atributos_relacion_ternaria<br>} |
|                   | ![](../imgs/BD%20-%20BigER%20Ternaria%20Crows%20Foot.png)                                                                                                                                                                                                                                                                                                                                                                                                                                    |

Nuestro ejemplo quedaría modelado así:

| BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ```entity``` Proveedor {<br>id_proveedor_ ```key```<br>}<br>```entity``` Proyecto {<br>id_proyecto ```key```<br>}<br>```entity``` Repuesto {<br>id_repuesto ```key```<br>}<br>// simplemente añadimos una nueva flecha y la tercera entidad a relacionar junto con su cardinalidad mínima y máxima con respecto al otro par de entidades<br>```relationship``` suministra {<br>Proveedor[```1..N```] ==->== Proyecto[```1..N```] ==->== Repuesto[```1..N```]<br>cantidad<br>} |
| ![](../imgs/BD%20-%20BigER%20Ternaria%20Proveedor%20Proyecto%20Suministro%20Crows%20Foot.png)                                                                                                                                                                                                                                                                                                                                                                                    |

---
### 6.3 Caso de Uso Incorrecto: El "Falso Ternario"

Este es el error más común. Ocurre cuando un escenario _parece_ ternario, pero en realidad se puede descomponer en hechos binarios independientes.
#### Contraejemplo: La Relación `imparte`

Imagina que necesitamos modelar el horario de clases. Tenemos tres entidades:

- `Profesor`
    
- `Asignatura`
    
- `Aula`
    

El "hecho" que queremos registrar parece un único evento: "La `Profesora Sanz` imparte `Bases de Datos` en el `Aula 101`."

**La Prueba de la Descomposición:** ¿Podemos separar este "hecho" en reglas de negocio más pequeñas que sigan teniendo sentido por sí solas?

1. Hecho 1: ¿Profesor imparte Asignatura?
    
    ¿Es "La Profesora Sanz es la responsable de Bases de Datos" un hecho válido por sí mismo, incluso si aún no sabemos el aula? Sí. Es la asignación docente.
    
2. Hecho 2: ¿Asignatura se da en un Aula?
    
    ¿Es "La asignatura Bases de Datos se imparte en el Aula 101" un hecho válido? Sí. Es la asignación de recursos, independientemente de qué profesor la imparta ese año.
    

Como podemos separar el "hecho" principal en dos reglas de negocio independientes, **no debemos usar una relación ternaria**.

La Solución Correcta (y más flexible):

Lo correcto es modelar esto como dos relaciones binarias. La forma más correcta de hacerlo es convertir la relación Profesor-Asignatura en una entidad asociativa (que podemos llamar Asoc_Docencia) y luego conectar esta nueva entidad con Aula.

Este modelo es superior porque:

- **Es preciso**: Refleja las dos reglas de negocio separadas.
    
- **Es flexible**: Permite que un `Profesor` esté asignado a una `Asignatura` (Hecho 1) incluso antes de que se le asigne un `Aula` (Hecho 2).
    
- **Maneja excepciones**: ¿Qué pasa si la `Asignatura` es "Prácticas Externas" y no tiene `Aula`? Este modelo lo permite; el ternario no.
    

---
### 6.4 La Gran Lección: ¿Ternaria o Binaria?

Para decidir, hazte esta pregunta:

**¿El "hecho" que estoy modelando es un evento único e indivisible, o se puede descomponer en reglas de negocio más pequeñas que sigan teniendo sentido por sí solas?**

- Si es **indivisible** (como `suministra`, donde la `Cantidad` une a los tres), usa una **relación ternaria**.
    
- Si es **descomponible** (como `imparte`, que se divide en "asignación docente" y "asignación de aula"), usa **relaciones binarias**, conectándolas a través de una entidad asociativa cuando sea necesario.

---

## 7: Más Allá del E/R Básico - El Modelo Extendido (EER)

### 7.1 Introducción: Enriqueciendo el Modelo

El modelo Entidad-Relación (E/R) que hemos visto hasta ahora es la base del diseño conceptual. Sin embargo, a veces necesitamos herramientas más potentes para representar clasificaciones y tipos dentro de nuestras entidades. El **Modelo Entidad-Relación Extendido (EER)** añade precisamente esas herramientas.

El EER incluye **todos los conceptos del E/R básico** (entidades, atributos, relaciones) y los **amplía** con ideas como:

- **Subclases y Superclases**: Para representar tipos específicos dentro de una entidad general.
    
- **Especialización y Generalización**: Los procesos para crear estas jerarquías.
    
- **Herencia**: Cómo las subclases heredan propiedades.
    
- **Restricciones**: Reglas que definen cómo se relacionan las subclases.
    

Estos conceptos nos permiten modelar aplicaciones de forma más completa y precisa, incorporando ideas del mundo de la orientación a objetos.

---

### 7.2 Subclases y Superclases: La Relación "ES UN" (IS-A)

A menudo, dentro de un tipo de entidad general, existen subgrupos con características o relaciones particulares.

- **Ejemplo**: En la entidad `Empleado`, podemos identificar subgrupos como:
	- `Administrativo`, `Ingeniero`, `Tecnico` (basados en el puesto)
	- `GERENTE` (basado en el rol) 
	- `TIEMPO_COMPLETO`, `TIEMPO_PARCIAL` (basados en la jornada).
    
Estos subgrupos se llaman **subclases** (o subtipos), y la entidad general de la que forman parte se llama **superclase** .

La relación entre una superclase y sus subclases se conoce como **"ES UN"** (IS-A):

- Un `Administrativo` **ES UN** `Empleado`.
    
- Un `GERENTE` **ES UN** `Empleado`.
    

**Principios Fundamentales**:

1. **Misma Entidad, Rol Específico**: Una instancia en una subclase (un ingeniero concreto) es la **misma instancia** del mundo real que pertenece a la superclase (ese mismo ingeniero es también un empleado).
    
2. **Existencia Dependiente**: Una instancia no puede existir _solo_ como miembro de una subclase; **debe** pertenecer también a la superclase.
    
3. **Pertenencia Opcional (por defecto)**: Una instancia de la superclase puede pertenecer a ninguna, una, o varias subclases, dependiendo de las reglas específicas que definamos Por ejemplo, un empleado que es gerente e ingeniero pertenecería a ambas subclases (`GERENTE` e `Ingeniero`) además de a `TIEMPO_COMPLETO` si esa es su jornada.
    

---

### 7.3 Herencia: Recibiendo Propiedades

Una de las grandes ventajas del modelo EER es la **herencia** 🧬. Una entidad que pertenece a una subclase **hereda automáticamente**:

1. **Todos los atributos** de su superclase (incluyendo la clave primaria). Por ejemplo, un `Ingeniero` hereda atributos como `dni`, `nombre`, `direccion`, etc., de `Empleado` .
    
2. **Todas las relaciones** en las que participa su superclase. Si `Empleado` se relaciona con `Departamento` a través de `trabaja_para`, entonces un `Ingeniero` también `trabaja_para` un `Departamento`.
    
#### **¡Criterio Fundamental de Diseño!** 💡

Precisamente por la herencia, **solo debemos crear una subclase si esta aporta algo nuevo y significativo** que no se aplica a toda la superclase. Es decir, una subclase se justifica **únicamente** si cumple al menos una de estas condiciones:

- **Tiene Atributos Específicos (o Locales)**: Atributos que solo tienen sentido para ese subgrupo.
    
    - _Ejemplo_: `categoria` para `Administrativo`, `tipo_ingeniero` para `Ingeniero`. Estos atributos no aplican a todos los `Empleados`.
        
- **Participa en Relaciones Específicas**: Relaciones en las que solo participa la subclase, no la superclase general.
    
    - _Ejemplo_: La relación `afiliado_a` con `Sindicato` solo aplica a los `Empleados` que son `TIEMPO_PARCIAL`, no a todos.
        

**¿Por qué es tan importante esta regla?** Si creamos subclases que no tienen atributos ni relaciones propias, simplemente estamos añadiendo complejidad al diagrama sin ganar ninguna capacidad descriptiva. La información que distingue a esas subclases (como el `TipoTrabajo`) ya está representada como un atributo en la superclase. Crear subclases vacías es redundante y dificulta la lectura del modelo.

**En resumen**: No crees una subclase si no va a tener "contenido propio" (atributos o relaciones específicas).

---
### 7.4 Restricciones: Las Reglas de la Jerarquía

Podemos (y debemos) definir reglas precisas sobre cómo funcionan nuestras especializaciones. Hay dos tipos de restricciones:
#### A) Restricción de Completitud (¿Obligatorias o No?)

Controla si todos los miembros de la superclase deben pertenecer a alguna subclase.

- **Total (t)** : **Toda** entidad de la superclase **debe** pertenecer a **al menos una** de las subclases. No hay "huecos". Las generalizaciones suelen ser totales.
    
    - _Ejemplo_: `{Tiempo_Completo, Tiempo_Parcial}` es total. Todo empleado tiene una de esas dos jornadas.
        
- **Parcial (p)** : Una entidad de la superclase **puede no pertenecer a ninguna** de las subclases.
    
    - _Ejemplo_: `{Administrativo, Tecnico, Ingeniero}` es parcial. Puede haber empleados que no sean ninguno de los tres (como un `GERENTE`).
        
#### B) Restricción de Disyunción (¿Exclusivas o No?)

Controla si las subclases pueden compartir miembros.

- **Disjunta (d)** : Las subclases son **mutuamente excluyentes**. Una entidad de la superclase puede pertenecer, como máximo, a **una** de las subclases.
    
    - _Ejemplo_: `{Administrativo, Tecnico, Ingeniero}` es disjunta. No puedes ser dos a la vez.
        
- **Solapada (s)** : Las subclases **pueden compartir miembros**. Una entidad puede pertenecer a **más de una** subclase simultáneamente.
    
    - _Ejemplo_: Si tuviéramos una especialización `{Investigador, Docente}`, podría ser solapada, ya que un empleado podría ser ambas cosas.
        

#### Combinaciones Posibles

|                                                                                                                                       | total (t)                          | parcial (p)                      |
| ------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | -------------------------------- |
| Toda instancia del supertipo está en alguno de los subtipos <br>=<br>La unión de todas las instancias de los subtipos da el supertipo | SÍ  <br>∪ Subtipos <br>= Supertipo | NO <br>∪ Subtipos ≠<br>Supertipo |

|                                                                                                                                         | disjunta (d)               | solapada (s)                |
| --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | --------------------------- |
| Cada instancia del supertipo puede estar como máximo en un subtipo <br>= <br>La intersección de las instancias de los subtipos es vacía | SÍ  <br>∩ Subtipos <br>= ∅ | NO <br>∩ Subtipos  <br>≠  ∅ |

Estas restricciones se combinan dando lugar a cuatro tipos de especialización:

1. **Total y Disjunta (t, d)**: Pertenencia obligatoria y a exactamente una subclase.
    
2. **Total y Solapada (t, s)**: Pertenencia obligatoria y a una o más subclases.
    
3. **Parcial y Disjunta (p, d)**: Pertenencia opcional y a como máximo una subclase.
    
4. **Parcial y Solapada (p, s)**: Pertenencia opcional y a cero, una o varias subclases.
    
    (Existe un caso especial si la especialización solo tiene una subclase, donde solo aplica la restricción parcial).
    

---

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
> | Concepto                         | BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Especialización / Generalización | ```entity``` ==SUPERCLASE== {<br>==id_superclase== ```key```<br>==atributos_superclase==<br>}<br><br>//SUBCLASE1 se crea porque tiene atributos propios distintos a los de la superclase<br><br>//indicar con un comentario delante de la subclase si es ==total== o ==parcial== y si es ==solapada== o ==disjunta==<br>```entity``` ==SUBCLASE1 extends SUPERCLASE== {<br>==id_superclase== ```key```<br>==atributos_subclase==<br>}<br><br>//SUBCLASE2 se crea porque participa en una relacion distinta a la de la superclase<br><br>//indicar con un comentario delante de la subclase si es ==total== o ==parcial== y si es ==solapada== o ==disjunta==<br>```entity``` ==SUBCLASE2 extends SUPERCLASE== {<br>==id_superclase== ```key```<br>}<br><br>//otra entidad distinta para relacionar con la subclase2<br>```entity``` OTRA_ENTIDAD {<br>id_otra_entidad ```key```<br>}<br><br>//relacion entre subclase2 y otra entidad<br>```relationship``` relacion {<br>==SUBCLASE2==[```1..N```] -> OTRA_ENTIDAD[```1..N```]<br>} | **En el diagrama generado por BigER lo veremos gráficamente como unas flechas blancas de las subclases a la superclase**<br>![](../imgs/BD%20-%20BigER%20Superclase%20y%20subclase%20Crows%20Foot.png)<br>**Cuando lo dibujemos a mano, pondremos una línea blanca perpendicular las flechas que pertenecen a la misma especialización/generalización y las letras t (total) o p (parcial) y s (solapada) o d (disjunta) según corresponda**<br>![](../imgs/BD%20-%20BigER%20Superclase%20y%20subclase%20Crows%20Foot%20modificado.png) |

---

### 7.5 Especialización y Generalización: Creando las Jerarquías

Estos son los dos procesos para _identificar_ y _crear_ las relaciones superclase/subclase.

#### A) Especialización (Top-Down) 🔽

Es el proceso de partir de una entidad general (superclase) e identificar subgrupos con características distintivas, creando las subclases. Es un refinamiento **de arriba hacia abajo**.

- **Ejemplo**: Empezamos con `Empleado` y definimos las subclases `{Administrativo, Tecnico, Ingeniero}` basándonos en el tipo de puesto de trabajo. Luego, identificamos el rol `Gerente` y creamos esa subclase. Podemos tener múltiples especializaciones de la misma superclase, como `{Tiempo_Completo, Tiempo_Parcial}` basada en la jornada.

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Crow's foot                                                       |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| //superclase Empleado<br>`entity` ==Empleado== {<br>==dni== `key`<br>nombre<br>fecha_nacimiento<br>direccion<br>telefono<br>}<br><br>//==Especializacion Tipo Puesto== <br><br>//==parcial,disjunta==<br>```entity``` ==Administrativo extends Empleado== {<br>==dni== ```key```<br>categoria<br>}<br><br>//==parcial,disjunta==<br>```entity``` ==Tecnico extends Empleado =={<br>==dni== ```key```<br>nivel<br>}<br><br>//==parcial,disjunta==<br>```entity``` ==Ingeniero extends Empleado== {<br>==dni== ```key```<br>tipoIng<br>}<br><br>//==Especializacion Relacion dirige==<br><br>//==parcial==<br>```entity``` ==Gerente extends Empleado== {<br>==dni== key<br>}<br><br>```entity``` Proyecto {<br>cod_proyecto ```key```<br>}<br><br>```relationship``` dirige{<br>==Gerente==[```1..1```] -> Proyecto[```1..N```]<br>}<br><br>//==Especializacion Tipo Jornada==<br><br>//==total,disjunta==<br>```entity``` ==TiempoCompleto extends Empleado=={<br>==dni== ```key```<br>salario<br>}<br><br>//==total,disjunta==<br>```entity``` ==TiempoParcial extends Empleado=={<br>==dni== ```key```<br>horas_semanales<br>}<br><br>```relationship``` afiliadoA{<br>==TiempoParcial==[```1..N```] -> Sindicato[```1..1```]<br>}<br><br>```entity``` Sindicato {<br>cod_sindicato ```key```<br>} | ![](../imgs/BD%20-%20BigER%20Jerarquia%20Empleado%20retocada.png) |

#### B) Generalización (Bottom-Up)🔼

Es el proceso inverso. Empezamos con varias entidades distintas, notamos que comparten atributos y relaciones comunes, y creamos una superclase general para agrupar esas características comunes. Es una síntesis **de abajo hacia arriba**.

- **Ejemplo**: Modelamos `Coche` con los atributos (`id_coche`, `matricula`, `precio`, `max_velocidad` y `numero_pasajeros`) y `Camion` por separado con los atributos (`id_camion`, `matricula`, `precio`, `tonelaje` y `numero_ejes`). Al ver que comparten un id numérico, `Matricula` y `Precio`, creamos la superclase `Vehiculo` con un id llamado `id_vehiculo ` y los atributos comunes. `Coche` y `Camion` se convierten en subclases con solo sus atributos específicos (`max_velocidad`y `numero_pasajeros` para `Coche`, `tonelaje` y `numero_ejes` para `Camion`).
    

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Crow's foot                                               |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| //superclase Vehiculo<br>```entity``` ==Vehiculo== {<br>==id_vehiculo== ```key```<br>matricula<br>precio<br>}<br><br>//==Especializacion Tipo Vehículo, total, disjunta==<br>```entity``` ==Coche extends Vehiculo== {<br>==id_vehiculo== ```key```<br>max_velocidad<br>numero_pasajeros<br>}<br><br>//==Especializacion Tipo Vehículo, total, disjunta==<br>```entity``` ==Camion extends Vehiculo== {<br>==id_vehiculo== ```key```<br>tonelaje<br>numero_ejes<br>} | ![300](../imgs/BD%20-%20Big%20ER%20Jerarquia%20Vehiculo.png) |

En la práctica, ambos procesos se suelen usar combinados. El resultado final, ya sea por especialización o generalización, es una **jerarquía** o **red** de clases.

---

### 7.6 Tipos de Especialización: ¿Reglas Automáticas o Manuales?

¿Cómo determina el sistema a qué subclase pertenece una entidad de la superclase? Hay dos formas:

#### A) Especialización Definida por Atributo ✅

Existe un atributo en la superclase cuyo valor **determina automáticamente** la pertenencia a una subclase. Estas subclases se llaman **subclases de predicado definido**.

- **Ejemplo**: La especialización `{Administrativo, Tecnico, Ingeniero}` de `Empleado` está definida por el atributo `TipoTrabajo`. Si `TipoTrabajo = 'Ingeniero'`, la entidad pertenece a la subclase `Ingeniero`.
    
- Cuando todas las subclases de una especialización usan el mismo atributo definitorio, se llama **especialización definida por atributo**, y el atributo es el **atributo definitorio**.

#### B) Especialización Definida por el Usuario 👤

No existe un atributo que determine la pertenencia. Es el **usuario** quien **especifica manualmente** a qué subclase(s) pertenece una entidad cuando la añade o modifica.

- **Ejemplo**: La especialización `Gerente` de `Empleado` podría ser definida por el usuario, indicando si un empleado específico desempeña ese rol o no.
    
---
### 7.7 Estructuras: Jerarquías vs. Redes (Entramados)

Las relaciones superclase/subclase pueden organizarse de dos maneras:

#### A) Jerarquía de Especialización (Árbol 🌳)

- Una subclase tiene **una única superclase directa** (herencia simple).
    
- Forma una estructura de árbol.
    
- Una subclase hereda de su padre, abuelo, etc., siguiendo la rama hacia arriba.
    
- **Ejemplo**: La estructura de la Universidad que muestra en la siguiente figura es una jerarquía.

![](../imgs/BD%20-%20BigER%20Jerarquia%20Universidad.png)

>[!exercise] Ejercicio
> Prueba a modelar en  [BigER]( https://marketplace.visualstudio.com/items?itemName=BIGModelingTools.erdiagram) la jerarquía de la Universidad de la figura anterior.
#### B) Red o Entramado de Especialización (Malla 🕸️)

- Una subclase puede tener **varias superclases directas** (herencia múltiple).
    
- La subclase con múltiples padres se llama **subclase compartida**.
    
- **Ejemplo**: Imagina que en nuestro ejemplo de `Empleado`, quisiéramos crear la subclase compartida `Ingeniero_JEFE` que herede de `Ingeniero`, `Gerente` y `Tiempo_Completo`.
    
- Aunque EER lo permite, muchos sistemas pueden tener limitaciones con la herencia múltiple. BigER no lo soporta
    

>[!question] Pregunta
>	Explica el concepto de herencia en el modelo EER. ¿Qué heredan las subclases?


>[!question] Pregunta
>	¿Cuándo modelaremos subclases?

---
## 8. El Arte del Buen Diseño - Principios Fundamentales y Errores a Evitar

![El Arte del Buen Diseño - Principios Fundamentales y Errores a Evitar](../evaluacion/El%20Arte%20del%20Buen%20Diseño%20-%20Principios%20Fundamentales%20y%20Errores%20a%20Evitar.md)

---

## Anexo I. Resumen BigER

![AL09.2 - Notación BigER](../labs/anexos/AL09.2%20-%20Notación%20BigER.md)

