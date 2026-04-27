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

El [Modelo Relacional](T02%20-%20Modelo%20Relacional.md#Modelo%20Relacional) propuesto por Edgar F. Codd, fue una revolución. Ofreció una forma matemática, consistente y robusta de almacenar y gestionar datos, superando el [problema de la rigidez](Problema%20Modelo%20Jerárquico.md) del [Modelo Jerárquico](Historia%20Bases%20de%20Datos.md#Modelo%20Jerárquico)  y el  [el problema de la complejidad](Problema%20Modelo%20Red.md) del [Modelo en Red](Historia%20Bases%20de%20Datos.md#Modelo%20de%20Red%20(o%20grafo)). 

Sin embargo, su enfoque estaba en la **implementación** y la **consistencia lógica** de los datos, no en el proceso de diseño conceptual previo. Esto dejó abiertos varios desafíos importantes:

1. **El Desafío Semántico: ¿Qué significan realmente los datos?** El **Modelo Relacional**, por decirlo de forma sencilla, trabaja con tablas, filas y columnas. Aunque es eficiente, carece de "semántica", es decir, de un significado inherente sobre el mundo real. Una tabla `T01` con columnas `C1` y `C2` no dice nada sobre si representa a un "cliente" comprando un "producto" o un "paciente" asignado a un "doctor". Somos nosotros quienes le damos la interpretación. Para un negocio, la realidad no son "tablas", son "clientes", "productos" y "pedidos". La pregunta era: ¿cómo podemos modelar el _significado_ de los datos del negocio antes de pensar en tablas?
      
2. **La Barrera de la Comunicación: ¿Cómo diseñar con personas no técnicas?**  Diseñar una **base de datos relacional** requiere una comunicación fluida con los expertos del negocio (gerentes, empleados, etc.). Es prácticamente imposible que una persona no técnica valide un diseño basado en un conjunto de tablas normalizadas, claves foráneas y tipos de datos. Es un lenguaje demasiado técnico y orientado a la implementación. Se necesitaba un lenguaje común, preferiblemente visual, que permitiera a diseñadores y usuarios del negocio hablar sobre la misma realidad sin una barrera técnica.
            
3. **La Dependencia del Modelo de Datos: ¿Y si no quiero usar tablas?** El **Modelo relacional** es una forma específica, excelente y popular,  de implementar una base de datos pero el diseño conceptual de un negocio no debería depender de la tecnología final. ¿Cómo podíamos crear un "plano" del universo de los datos de una empresa que fuera independiente de si al final se iba a construir usando una base de datos relacional, una orientada a objetos o cualquier otra tecnología?    

---
### **1.2. La Solución de Chen: Un Modelo para el Diseño Conceptual**

Frente a estos desafíos, Peter Chen propuso en 1976 el **Modelo Entidad-Relación**[^1].

[^1]: Nos referiremos a menudo a él también como modelo ER a secas.

> [!INFO] Artículo 
> Peter P. S. Chen. 1976. _[The Entity-Relationship Model—Toward a Unified View of Data](https://doi.org/10.1145/320434.320440)_, ACM Trans. Database Syst. 1, 1 (March 1976), 9-36.

Su objetivo no era reemplazar el modelo relacional, sino crear un paso previo y fundamental: un modelo de alto nivel para el **diseño conceptual**.

El modelo Entidad Relación de Chen resolvió directamente los problemas anteriores:

1. **El Desafío Semántico: ¿Qué significan realmente los datos?** Chen creó un **modelo para capturar la semántica** del mundo real antes de pensar en tablas, permitiendo hablar de "Entidades" y "Relaciones" que existen en el mundo real. Permite modelar directamente conceptos como `CLIENTE` (una entidad) que tiene un `Nombre` (un atributo) y que `REALIZA` (una relación) un `PEDIDO` (otra entidad). Se centra en el "qué" significa la información.
    
2. **La Barrera de la Comunicación: ¿Cómo diseñar con personas no técnicas?** Chen creó un **lenguaje visual y común**, los diagramas ER, para la comunicación entre técnicos y no-técnicos. Son una herramienta **visual y de alto nivel**. Permite a los diseñadores de bases de datos y a los expertos del negocio hablar el mismo idioma. Un gerente puede ver un diagrama con cajas y rombos y decir: "No, un cliente puede tener muchos pedidos, no solo uno", permitiendo corregir la lógica del negocio en la fase de diseño, cuando es más barato y fácil.

3. **La Dependencia del Modelo de Datos: ¿Y si no quiero usar tablas?** Chen **ofreció independencia**, ya que un mismo diagrama ER puede ser implementado en diferentes sistemas de bases de datos ya que es puramente conceptual. Puedes tomar un diagrama E-R y decidir implementarlo en una base de datos relacional, en una orientada a objetos, o incluso en una base de datos en red. Describe la realidad del negocio de forma independiente a la tecnología que se usará para almacenarla.

La mejor forma de entender la diferencia es con la analogía de construir una casa:

- **El Modelo ER es el plano del arquitecto.** Es un diseño conceptual. Es el primer boceto que se diseña hablando con el cliente. Define las habitaciones (`Entidades` como "Cliente" o "Producto"), sus características (`Atributos` como "Nombre" o "Precio") y los pasillos que las conectan (`Relaciones` como "Compra"). Su propósito es capturar las necesidades del negocio y ser una herramienta de comunicación. Este plano se usa para hablar con el cliente (los usuarios del negocio), entender sus necesidades y asegurarse de que la estructura general tiene sentido antes de empezar a poner ladrillos. Es fácil de entender para personas no técnicas.
    
- **El Modelo Relacional es el plano de construcción detallado.** Es un diseño lógico/físico. Es el documento técnico que usan los ingenieros. Traduce el plano del arquitecto en especificaciones concretas: el número de vigas, el tipo de ladrillos y las conexiones de tuberías (`Tablas`, `Columnas`, `Claves Primarias y Foráneas`). Su propósito es la implementación técnica. Este plano es para los ingenieros y constructores (el sistema gestor de la base de datos, o DBMS), no para el cliente.
---
### **1.3. El Flujo de Trabajo: Cómo se Complementan Ambos Modelos**

El modelo relacional y el modelo entidad-relación **operan en niveles de abstracción diferentes y resuelven problemas distintos**. No son competidores, sino herramientas complementarias que se usan en fases diferentes del diseño de una base de datos. En la práctica, ambos modelos son las dos caras de la misma moneda y se utilizan en un proceso secuencial:

1. **Fase 1. Conceptual:** Nos comunicamos con los usuarios para entender sus necesidades y creamos un **Diagrama ER** para modelar la realidad del negocio.
    
2. **Fase 2. Lógica:** Traducimos sistemáticamente el **Diagrama ER** a un conjunto de relaciones, siguiendo unas reglas claras. El resultado es un **esquema relacional**.
    
3. **Fase 3. Física:** Implementamos el **esquema relacional** se implementa en un sistema gestor de bases de datos concreto (MySQL, Oracle, PostgreSQL, etc.).
    
En resumen, **Chen no creó una alternativa al modelo relacional, sino el puente que faltaba entre la comprensión humana de la realidad desordenada de un negocio y la estructura lógica, ordenada y matemática del modelo relacional.** Codd nos dio una forma soberbia de construir la base de datos, y Chen nos dio una forma soberbia de diseñarla primero.

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> En las siguientes subsecciones iremos explicando los conceptos del modelo Entidad-Relación. Para plasmar estos conceptos tradicionalmente se ha utilizado la notación gráfica de Chen. La notación de Chen es el pilar sobre el que se construyó todo el modelado de datos; es la "lengua madre". 
> Sin embargo, nosotros vamos a usar el lenguaje BigER y la notación Crow's Foot en Lugar de Chen. ¿Por qué?
> Antes de nada, ¿Qué es **BigER** y qué es la notación **Crow's Foot**?
>- **BigER** es un lenguaje que nos permite escribir la **receta** de nuestro modelo de datos. Es un texto claro y preciso que describe cada "ingrediente" (entidad, atributo) y cada "paso" (relación).
  > - La **Notación Crow's Foot (Pata de Gallo)** es una de las formas más populares de "presentar el plato". Es un estilo de diagrama muy intuitivo que nos permite "ver" la receta de un solo vistazo.
> 
> En el desarrollo de proyectos reales, la combinación de un lenguaje textual como **BigER** y una notación industrial como **Crow's Foot** ofrece ventajas prácticas inmensas. No se trata de que Chen sea "incorrecto", sino de que este flujo de trabajo moderno es más **ágil, colaborativo y mantenible**. Veamos por qué.
> ###### 1. Separa la Lógica de la Presentación: La Receta vs. el Plato 🧠
> Esta es la ventaja más importante.
> - **Método Tradicional (Chen)**: El diagrama _es_ el modelo. La lógica (las reglas del negocio) y la presentación están fusionadas en una sola imagen. Si quieres mostrar el mismo modelo en otra notación, tienes que **redibujarlo desde cero**.
> - **Método Moderno (BigER + Crow's Foot)**: La "receta" (el código BigER) es el modelo, la única fuente de la verdad. El diagrama es solo una **representación visual** de esa receta. Puedes pedirle a BigER que te muestre el mismo código en notación Chen, Crow's Foot o UML con un solo clic. La lógica permanece intacta, solo cambia la presentación.
> 	- **Analogía**: Es como tener la partitura de una canción (el código BigER). Puedes pedirle a una orquesta que la toque (diagrama Crow's Foot) o a un pianista que la interprete (Chen). La canción es la misma.
> ###### 2. Agilidad y Mantenimiento: Editar Texto es Más Fácil que Redibujar.
> Imagina que necesitas cambiar cualquier elemento de un diagrama.
> - **Método Tradicional**: Debes abrir un editor gráfico, borrar los elementos implicados y volver a dibujarlos. Si el cambio es grande, puede que necesites reorganizar todo el diagrama para que quepa y se entienda. Es un proceso manual y lento.
> - **Método Moderno**: Abres el archivo de texto y cambias las líneas de código necesarias. Guardas el archivo y el diagrama se **regenera automáticamente**. Es un cambio de segundos, preciso y sin esfuerzo.
>  ###### 3. Colaboración y Control de Versiones (Git) 🤝
> Este es un factor decisivo en cualquier proyecto de equipo.
> - **Método Tradicional**: Los diagramas se guardan como archivos binarios (imágenes, archivos de Visio, etc.). Estos archivos son una pesadilla para sistemas de control de versiones como Git. Es casi imposible ver qué cambió exactamente entre dos versiones de un diagrama, y si dos personas modifican el mismo diagrama a la vez, fusionar sus cambios es prácticamente imposible.
> - **Método Moderno**: El modelo BigER es un **archivo de texto plano**. Esto es perfecto para Git. Puedes ver el historial completo de cambios línea por línea, saber quién cambió qué y por qué, fusionar el trabajo de varios desarrolladores y revertir a una versión anterior si algo sale mal. Permite que el diseño de la base de datos sea tratado como lo que es: una parte fundamental del código del proyecto.
> ###### 4. Claridad y Estándar de la Industria: La Ventaja de Crow's Foot 🏭
> Si bien la notación de Chen puede ser perfecta para aprender, la de Crow's Foot está optimizada para la claridad en diagramas complejos y es un estándar de facto en la industria.
> ##### Resumen Comparativo
>
| Característica      | Método Tradicional (Dibujar Chen)           | Método Moderno (BigER + Crow's Foot)                 |
| ------------------- | ------------------------------------------- | ---------------------------------------------------- |
| **Flexibilidad**    | El modelo y el diagrama son lo mismo.         | El modelo (código) y el diagrama están separados.      |
| **Mantenimiento**   | Lento y manual. Redibujar para cada cambio. | Rápido y automático. Editar texto.                   |
| **Colaboración**    | Difícil de versionar y fusionar (Git).      | Ideal para control de versiones y trabajo en equipo. |
| **Claridad Visual** | Muy explícito pero puede ser denso.         | Compacto y estándar en la industria.                 |
>
>###### 5. Instalación y uso de BigER en VSCode
>En esta nota tienes disponible una pequeña guía para empezar a usar BigER en VSCode: [AL09.1 - Instalación y uso de BigER en VSCode](AL09.1%20-%20Instalación%20y%20uso%20de%20BigER%20en%20VSCode.md)

---
> [!example] Ejemplo simple: EMPRESA
> Tras introducir los distintos conceptos, iremos completando el modelo Entidad/Relación de un ejemplo sencillo de una EMPRESA.
> Sus requisitos simplificados son:
> >[!exercise] La empresa está organizada en **DEPARTAMENTOS**
> >	- Cada departamento tiene un nombre, un número y un empleado que lo dirige.
> >	- Llevamos un registro de la fecha de inicio del director del departamento.
> >	- Un departamento se ubica en varias ubicaciones.
> 
> >[!exercise] Cada departamento controla una serie de **PROYECTOS**.
> >	- Cada proyecto tiene un nombre único, un número único y se localiza en una única ubicación.
> 
> >[!exercise] La empresa tiene **EMPLEADOS** que trabajan para un departamento
> >	-Cada empleado tiene dni, dirección, salario, sexo y fecha de nacimiento.
> >	-Cada empleado trabaja para un departamento pero puede trabajar en varios proyectos.
> >	-Se deben registrar las horas semanales que un empleado trabaja actualmente en cada proyecto.
> >	-Existen empleados que supervisan a otros empleados.
> 
> >[!exercise] Cada empleado puede tener **FAMILIARES** a su cargo.
> >	-Para cada familiar, se registra su nombre, sexo, fecha de nacimiento y relación de parentesco con el empleado.

---
## 2. Los Pilares del Modelo - Entidades y Atributos

### 2.1. Entidades y Atributos: Los Sustantivos y sus Adjetivos

Imagina que tienes que organizar la información de un "minimundo" (como una universidad, una empresa o una biblioteca). Lo primero que harías es identificar las "cosas" u "objetos" más importantes que lo componen.

- **Tipo de Entidad**: Es la plantilla o categoría de un objeto importante. Piensa en ello como el nombre de una ficha que vas a rellenar. Por ejemplo: `EMPLEADO`, `DEPARTAMENTO`, `PROYECTO`. Son nuestros **sustantivos**.
    
- **Entidad**: Es una "cosa" específica y real de un tipo de entidad. Es la ficha ya rellenada. Por ejemplo, la persona "José Pérez" es una entidad del tipo `EMPLEADO`.
    
- **Atributos**: Son las propiedades o características que describen a un tipo de entidad. Son los campos que tiene la ficha para rellenar. Para la ficha `EMPLEADO`, los atributos serían `Nombre`, `DNI`, `Dirección`, etc. Son nuestros **adjetivos**.
    

> **Analogía clave 🧠:**
> 
> - **Tipo de Entidad** = Una plantilla de ficha en blanco (ej: Ficha de `EMPLEADO`).
>     
> - **Entidad** = Una ficha específica ya rellenada (ej: La ficha de "José Pérez").
>     
> - **Atributos** = Los apartados o campos de la ficha (ej: `Nombre`, `DNI`, `Salario`).
>     

---
### 2.2 El Conjunto de Entidades: De la Plantilla a los Datos Reales

Ya hemos definido el **Tipo de Entidad** como la plantilla, el molde o el esquema (por ejemplo, la estructura de la ficha `EMPLEADO` con todos sus campos). Pero, ¿dónde están los empleados de verdad?

Ahí es donde entra el **Conjunto de Entidades**.

- **Definición**: Es la **colección de todas las entidades** de un tipo específico que existen en la base de datos **en un momento dado**. Es una "foto" del estado actual de los datos.
    
- **Términos Clave**:
    
    - El **Tipo de Entidad** se conoce como la **intención** (el diseño o la idea).
        
    - El **Conjunto de Entidades** se conoce como la **extensión** (la manifestación real y actual de esa idea).
        

> **Analogía del Archivador 🧠:**
> 
> - **Tipo de Entidad `EMPLEADO`**: Es el diseño de una ficha de empleado en blanco, con sus apartados `DNI`, `Nombre`, etc.
>     
> - **Conjunto de Entidades `EMPLEADO`**: Es el **archivador real que contiene todas las fichas de empleados ya rellenadas** que tienes hoy. Si mañana contratas a alguien, el conjunto crece. Si alguien se va, el conjunto se reduce.
>     

Es común, aunque a veces confuso, usar el mismo nombre (ej. `EMPLEADO`) para referirse tanto al tipo como al conjunto. Lo importante es que entiendas que uno es el **plano** y el otro es el **edificio construido**.

---

### 2.3. Tipos de Atributos: La Anatomía de la Descripción

No todos los atributos son iguales. Se clasifican según cómo guardan la información.

- **Simple o Atómico ⚛️**: Es un atributo que no se puede dividir en partes más pequeñas con significado propio.
    
    - **Ejemplo**: `DNI` o `Sexo`. No puedes descomponer un DNI en sub-partes lógicas.
        
- **Compuesto 🧩**: Es un atributo que está formado por otros atributos más pequeños. Es como un cajón que tiene compartimentos dentro.
    
    - **Ejemplo**: `Dirección` puede ser un único campo de texto o estar compuesto `Calle`, `Ciudad`, `Código Postal`.  `NombreCompleto` es otro gran ejemplo, compuesto por `Nombre`, `Apellido1` y `Apellido2`.
    - Si se sabe que va a ser necesario hacer búsquedas continuas por alguno de los atributos simples que componen el atributo compuesto entonces se modelarán como atributos simples
	    - En nuestro ejemplo, dejaremos `Dirección` como un único atributo porque consideramos que no va a haber búsquedas por sus atributos concretos mientras que para el nombre de los empleados usaremos `Nombre`,`Apellido1` y `Apellido2` porque en este casi sí se considera que va a haber búsquedas por dichos atributos.
        
- **Multivalor 📇**: Es un atributo que puede tener varios valores para una misma entidad. Piensa en ello como una "lista" o "bolsa" de valores.
    
    - **Ejemplo**: El atributo `Teléfono` de un `EMPLEADO`, ya que una persona puede tener un móvil y un fijo. O los `Colores` de un `COCHE`.
    
    >[!warning] ⚠️ Un atributo multivalor **siempre se convertirá a un tipo de Entidad denominado débil o dependiente** en la versión final de una diagrama Entidad Relación. Este tipo de entidad se explica en una sección posterior.


---

### 2.4. Tipos de Entidades y Atributos Clave: La Búsqueda del Identificador Único 🔑

Si tienes un archivador con miles de fichas de `EMPLEADO`, ¿cómo encuentras una en concreto sin dudar? No puedes usar el nombre, ¡podría haber varios "José Pérez"! Necesitas un identificador único.

- **Atributo Clave (o Clave Primaria)**: Es un atributo (o un conjunto de ellos) que tiene un valor **único** para cada entidad. Es la garantía de que no hay dos fichas iguales.
    
    - **Ejemplo**: El `DNI` para un `EMPLEADO` o la `Matrícula` para un `COCHE`.
        
- **Clave Candidata**: A veces, un tipo de entidad tiene varios atributos que podrían servir como clave primaria. Todos ellos son "candidatos".
    
    - **Ejemplo**: Para el tipo de entidad `COCHE`, tanto el `VIN` (Número de Identificación del Vehículo) como la `Matrícula` son únicos. Ambos son claves candidatas.
        
- **Elección de la Clave Primaria**: De entre todas las claves candidatas, elegimos una para que sea la clave primaria oficial. Las demás se llaman **claves alternativas**. Generalmente, se prefiere una clave simple (un solo atributo) si es posible.
    
---
### 2.5. Propiedades Adicionales de los Atributos

Además de la clasificación anterior, los atributos tienen otras características importantes:

- **Obligatorios y Opcionales**:
    
    - **Obligatorio**: El atributo debe tener un valor siempre (ej: `DNI` de un `EMPLEADO`).
        
    - **Opcional**: El atributo puede dejarse en blanco (ej: `NúmeroDePlanta` en una `Dirección`, si es una casa no lo necesita).
        
- **Almacenados y Derivados 🔢**:
    
    - **Almacenado**: Es un atributo que se guarda directamente en la base de datos (ej: `FechaDeNacimiento`).
        
    - **Derivado**: Es un atributo que **no se almacena en la base de datos**, porque **se puede calcular** a partir de otro. Esto evita inconsistencias.
        
	    - **Ejemplo**: El atributo `Edad`. No lo %% guardamos %%. Lo calculamos restando la `FechaDeNacimiento` a la fecha actual. Así, `Edad` siempre está actualizada y no ocupa espacio.
        
- **Conjuntos de Valores (Dominios)**: Cada atributo tiene un "dominio", que es el conjunto de todos los valores posibles que puede tomar. Es como el "tipo de dato" en programación.
    
    - **Ejemplo**: El dominio del atributo `Sexo` podría ser `{'Hombre', 'Mujer', 'Otro'}`. El dominio de `NotaExamen` podría ser el conjunto de números reales entre 0 y 10.
        

---

> [!example] Aplicando estos conceptos al caso EMPRESA
> Al leer los requisitos de la base de datos **EMPRESA**, hacemos una primera pasada para identificar los "sustantivos" principales:
> >[!exercise] La empresa se organiza en **DEPARTAMENTOS**. -> Tipo de Entidad `DEPARTAMENTO`
> 
> >[!exercise] Cada departamento controla **PROYECTOS**. -> Tipo de Entidad `PROYECTO`
> 
> >[!exercise] La empresa tiene **EMPLEADOS**. -> Tipo de Entidad `EMPLEADO`
> 
> >[!exercise] Cada empleado puede tener **FAMILIARES**. -> Tipo de Entidad `FAMILIAR`
>
> Ahora, para cada uno, listamos sus "adjetivos" o atributos según los requisitos:
> 
> >[!exercise] Tipo de Entidad `DEPARTAMENTO`
> >	- Nombre (clave)
> >	- Número (clave)
> >	- Ubicaciones (multivalor)
> >	- Director
> >	- FechaIngresoDirector
> 
> >[!exercise] Tipo de Entidad `PROYECTO`
> >	- Nombre (clave)
> >	- Número (clave)
> >	- Ubicacion 
> >	- DepartamentoControl
> 
> >[!exercise] Tipo de Entidad `EMPLEADO`
> >	- DNI (clave)
> >	- Direccion
> >	- Sueldo 
> >	- Sexo
> >	- Nombre
> >	- Apellido1
> >	- Apellido2
> >	- Supervisor
> >	- Departamento
> >	- Trabaja_en (multivalor y compuesto)
> >		- Proyecto
> >		- Horas
> 	
> >[!exercise] Tipo de Entidad `FAMILIAR`
> >	- Nombre
> >	- Sexo
> >	- FechaNac
> >	- Relacion
> >	

>[!question] Pregunta
>	¿Cuál sería la clave del tipo de Entidad `FAMILIAR`?

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > | Concepto                                                                                                        | Textual en BigER                                                                                                                                                                        | Notación Crow's foot en BigER                                                                                                   |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Entidad                                                                                                 | `entity` **Entidad** {<br>..<br>}                                                                                                                                                       | ![](BD%20-%20BigER%20Entidad.png)                                                                                               |
| Atributo clave                                                                                          | `entity` Entidad {<br>**atributo_clave** `key`<br>}                                                                                                                                     | ![](BD%20-%20BigER%20atributo%20clave.png)                                                                                      |
| Atributo no clave                                                                                       | `entity` Entidad {<br>atributo_clave `key`<br>**atributo_no_clave**<br>}                                                                                                                | ![](BD%20-%20BigER%20atributo%20no%20clave.png)                                                                                 |
| Atributo de clave alternativa<br>**No Soportado por BigER pero haremos lo que se muestra en cada caso** | //**añadimos el comentario UNIQUE al final de la definición del atributo**<br>`entity` Entidad {<br>atributo_clave `key`<br>atributo_no_clave<br>**atributo_clave_alternativa //UNIQUE**<br>} | **Cuando lo dibujemos a mano, pondremos un subrayado discontinuo **<br>![](BD%20-%20BigER%20atributo%20clave%20alternativa.png) |

---
### 2.6 Refinando el Diseño: ¿Y los Verbos?

Este primer diseño es un gran comienzo, pero está incompleto. Hemos identificado los sustantivos (`Entidades`) y sus adjetivos (`Atributos`), pero nos falta la parte más importante: las acciones o conexiones entre ellos, es decir, **los verbos**.

Frases como:

- Un `EMPLEADO` **dirige** un `DEPARTAMENTO`.
    
- Un `DEPARTAMENTO` **controla** `PROYECTO`.
    
- Un `EMPLEADO` **trabaja en** `PROYECTO`.
    
Estas conexiones son el tercer pilar del modelo: las **Relaciones**, y las exploraremos en la siguiente sección.

---
## 3: El Pegamento del Modelo - Relaciones

### 3.1 Introducción: De los Sustantivos a los Verbos

En la sección anterior, identificamos los "sustantivos" de nuestro minimundo (los **Tipos de Entidad**) y sus "adjetivos" (los **Atributos**). Pero un diseño solo con eso es como una lista de personajes sin historia. Nos falta la acción, las conexiones, los **verbos**.

Las **Relaciones** son el tercer y último pilar del modelo ER. Representan las asociaciones con significado que existen entre las entidades.

- `EMPLEADO` "José Pérez" **trabaja en** el `PROYECTO` "ProductoSecreto1".
    
- `EMPLEADO` "Laura Martínez" **dirige** el `DEPARTAMENTO` "Investigación".
    

Esas palabras en negrita son las relaciones. Le dan contexto y sentido a los datos, explicando cómo interactúan las entidades entre sí.

---

### 3.2 Tipos de Relación: Categorizando las Acciones

Al igual que agrupamos entidades similares en "Tipos de Entidad", agrupamos relaciones similares en **Tipos de Relación**.

- **Tipo de Relación**: Es la definición abstracta de una asociación entre tipos de entidad. Por ejemplo, `TRABAJA_EN` es el tipo de relación que conecta `EMPLEADO` y `PROYECTO` y `DIRIGE` es el tipo de relación que conecta `EMPLEADO` y `DEPARTAMENTO`.

- **Grado de una Relación**: Es el número de tipos de entidad que conecta la relación. La gran mayoría de las veces trabajarás con relaciones de **grado 2 (binarias)**, como todas las de nuestro ejemplo ( tanto `DIRIGE` como `TRABAJA_EN` son binarias).

- **Instancias de Relación:** Las conexiones *reales* entre entidades *específicas*.

![](BD%20-%20Instancias%20del%20conjunto%20de%20relación%20TRABAJA_EN.png)

---

### 3.3 Tipo vs. Conjunto de Relaciones: El Contrato en Blanco vs. los Contratos Firmados

Este concepto es idéntico al que vimos con las entidades, y es crucial para separar el diseño de los datos reales.

- **Tipo de Relación (La Intención)**: Es el "contrato en blanco" o la plantilla. Es la descripción esquemática de una relación. Describe la relación, qué tipos de entidad conecta y qué restricciones tiene. Por ejemplo, la idea de que un `EMPLEADO` puede `DIRIGIR` un `DEPARTAMENTO`.
    
- **Conjunto de Relaciones (La Extensión)**: Es la colección de todos los "contratos firmados" que existen en la base de datos en un momento dado. Es la lista de todas las conexiones reales. Por ejemplo: `(Laura Martínez, Investigación)` es una instancia del conjunto de relaciones `DIRIGE`.
---

> [!example] Aplicando Relaciones al Caso "EMPRESA"
> Volviendo a nuestro diseño inicial, ahora podemos establecer los "verbos" que conectan nuestras entidades basándonos en los requisitos:
> >[!exercise]  Un empleado **trabaja para** un departamento -> `TRABAJA_PARA` (entre `EMPLEADO` y `DEPARTAMENTO`)
> >	-> Desaparece `Departamento` como atributo en el diseño inicial del tipo de entidad `EMPLEADO`	
> 
> >[!exercise]  Un empleado **dirige** un departamento -> `DIRIGE` (entre `EMPLEADO` y `DEPARTAMENTO`)
> >	-> Desaparece `Director` como atributo en el diseño inicial del tipo de entidad `DEPARTAMENTO`
> >	-> Desaparece `FechaInicioDirector` como atributo en el diseño inicial del tipo de entidad `DEPARTAMENTO`
> 
> >[!exercise]  Un departamento **controla** proyectos -> `CONTROLA` (entre `DEPARTAMENTO` y `PROYECTO`)
> >	-> Desaparece `DepartamentoControl` como atributo en el diseño inicial del tipo de entidad `PROYECTO`
> 
> >[!exercise]  Un empleado **trabaja en** un proyecto -> `TRABAJA_EN` (entre `EMPLEADO` y `PROYECTO`)
> >	-> Desaparece `Trabaja_en` como atributo en el diseño inicial del tipo de entidad `EMPLEADO` así como el atributo `Proyecto` y el atributo `Horas`
> 
> >[!exercise]  Un empleado **supervisa** a otros empleados -> `SUPERVISA` (entre `EMPLEADO` y `EMPLEADO`)	
> >	-> Desaparece `Supervisor` como atributo en el diseño inicial del tipo de entidad `EMPLEADO`
> 
> >[!exercise]  Un empleado tiene **familiares a su cargo** -> `FAMILIAR_DE` (entre `EMPLEADO` y `FAMILIAR`)
>
> Tras eliminar los atributos como consecuencia de la creación de las relaciones, ahora las entidades tienen estos atributos:
> 
> >[!exercise] Tipo de Entidad `DEPARTAMENTO` 
> >	- Nombre (clave)
> >	- Número (clave)
> >	- Ubicaciones (multivalor)
> 
> >[!exercise] Tipo de Entidad `PROYECTO`
> >	- Nombre (clave)
> >	- Número (clave)
> >	- Ubicacion 
> 
> >[!exercise] Tipo de Entidad `EMPLEADO`
> >	- DNI (clave)
> >	- Direccion
> >	- Sueldo 
> >	- Sexo
> >	- Nombre
> >	- Apellido1
> >	- Apellido2
> >
> 	
> >[!exercise] Tipo de Entidad `FAMILIAR`
> >	- Nombre
> >	- Sexo
> >	- FechaNac
> >	- Relacion
> >	

>[!info] Más de una relación entre las mismas entidades
>Fíjate que entre `EMPLEADO` y `DEPARTAMENTO` hay dos relaciones distintas `TRABAJA_PARA` y `DIRIGE`. Esto es perfectamente válido porque describen dos asociaciones con un significado y unas reglas completamente diferentes.

>[!question] Pregunta
>	¿Qué ha pasado con el atributo Horas que formaba parte del atributo Trabaja_en en el tipo de Entidad `EMPLEADOS` y con el atributo FechaInicioDirector en el tipo de Entidad `DEPARTAMENTOS`?

---
### 3.4 Las Reglas del Juego: Restricciones Estructurales

Aquí es donde el modelo se vuelve realmente potente. No basta con decir que dos entidades se conectan; debemos especificar las **reglas de negocio** de esa conexión. Hay dos tipos de reglas principales.

#### A) Razón de Cardinalidad (Cardinalidad Máxima)

Esta regla responde a la pregunta: **"¿Con cuántas entidades te puedes relacionar como máximo?"**.

- **Uno a Uno (1:1)**: Una entidad de un lado solo puede relacionarse con, como máximo, una del otro, y viceversa.
    
    - **Ejemplo (DIRIGE)**: Un `EMPLEADO` solo puede dirigir **un** `DEPARTAMENTO`. Y un `DEPARTAMENTO` solo puede tener **un** director.
        
- **Uno a Muchos (1:N)**: Una entidad del lado "1" se relaciona con muchas del lado "N", pero una del lado "N" solo se relaciona con una del lado "1".
    
    - **Ejemplo (TRABAJA_PARA)**: Un `DEPARTAMENTO` puede tener **muchos** `EMPLEADOS`, pero un `EMPLEADO` solo trabaja para **un** `DEPARTAMENTO`.
        
- **Muchos a Muchos (M:N)**: Sin restricciones. Una entidad de un lado se puede relacionar con muchas del otro, y viceversa.
    
    - **Ejemplo (TRABAJA_EN)**: Un `EMPLEADO` puede trabajar en **varios** `PROYECTOS`, y un `PROYECTO` puede tener **varios** `EMPLEADOS`.
        
#### B) Restricción de Participación (Cardinalidad Mínima)

Esta regla responde a la pregunta: **"¿Es obligatorio participar en esta relación?"**.

- **Participación Total (Obligatoria)**: Significa que cada entidad de un tipo **debe** participar en la relación. 
    
    - **Ejemplo**: Si la política es que "todo `EMPLEADO` debe pertenecer a un `DEPARTAMENTO`", la participación del `EMPLEADO` en la relación `TRABAJA_PARA` es total. No puede existir un empleado sin departamento.
        
- **Participación Parcial (Opcional)**: Significa que una entidad puede existir sin necesidad de participar en la relación. 
    
    - **Ejemplo**: La participación de `EMPLEADO` en la relación `DIRIGE` es parcial. Un empleado no tiene por qué dirigir un departamento para existir en la base de datos. ¡La mayoría no lo hacen!      

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > | Concepto                                                                      | Textual en BigER                                                                                                                                                                      | Notación Crow's foot en BigER                                         |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Relación                                                                      | `relationship` **relacion** {<br>Entidad1[`MIN..MAX`] -> Entidad2[`MIN..MAX`]<br>}                                                                                                    | ![](BD%20-%20BigER%20Relacion%20Crows%20Foot.png)                              |
| Relación con  cardinalidad mínima y máxima 1..1 en ambos lados                | `entity` Automovil {<br>..<br>}<br>`entity` Seguro {<br>..<br>}<br>`relationship` tiene {<br>Automovil[`1..1`] -> Seguro[`1..1`]<br>}<br><br>                                         | ![](BD%20-%20BigER%20Relación%2011%2011%20Crows%20Foot.png) |
| Relación con cardinalidad mínima y máxima 1..1 en un lado y 1..N  en el otro  | `entity` Provincia {<br>..<br>}<br>`entity` Municipio {<br>..<br>}<br>`relationship` contiene {<br>Provincia[`1..1`] -> Municipio[`1..N`]<br>}<br>                                    | ![](BD%20-%20BigER%20Relación%2011%201N%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima  1..N en ambos lados                | `entity` Cliente {<br>..<br>}<br>`entity` Cuenta_Bancaria {<br>..<br>}<br>`relationship` es_titular_de {<br>Cliente[`1..N`] -> Cuenta_Bancaria[`1..N`]<br>}                           | ![](BD%20-%20BigER%20Relación%201N%201N%20Crows%20Foot.png)           |
| Relación con  cardinalidad mínima y máxima 0..1 en ambos lados                | `entity` Trabajador {<br>..<br>}<br>`entity` Plaza_Aparcamiento {<br>..<br>}<br>`relationship` tiene_asignada {<br>Trabajador[`0..1`] -> Plaza_Aparcamiento[`0..1`]<br>}              | ![](BD%20-%20BigER%20Relación%2001%2001%20Crows%20Foot.png)           |
| Relación con Cardinalidad Opcional 0..1 en un lado y 1..1  en el otro         | `entity` Provincia {<br>..<br>}<br>`entity` Municipio {<br>..<br>}<br>`relationship` tiene_capital {<br>Provincia[`0..1`] -> Municipio[`1..1`]<br>}                                   | ![](BD%20-%20BigER%20Relación%2001%2011%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..1 en un lado y 1..N  en el otro  | `entity` Cooperativa {<br>..<br>}<br>`entity` Agricultor {<br>..<br>}<br>`relationship` formada_por {<br>Cooperativa[`0..1`] -> Agricultor[`1..N`]<br>}                               | ![](BD%20-%20BigER%20Relación%2001%201N%20Crows%20Foot.png)           |
| Relación con  cardinalidad mínima y máxima 0..N en ambos lados                | `entity` Turista {<br>..<br>}<br>`entity` Monumento {<br>..<br>}<br>`relationship` visita {<br>Turista[`0..N`] -> Monumento[`0..N`]<br>}                                              | ![](BD%20-%20BigER%20Relación%200N%200N%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..N en un lado y 1..1  en el otro  | `entity` Vehiculo_Matriculado {<br>..<br>}<br>`entity` Persona {<br>..<br>}<br>`relationship` es_propiedad_de {<br>Vehiculo_Matriculado[`0..N`] -> Persona[`1..1`]<br>}               | ![](BD%20-%20BigER%20Relación%200N%2011%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..N en un lado y 1..N  en el otro  | `entity` Interprete {<br>..<br>}<br>`entity` Pelicula {<br>..<br>}<br>`relationship` actua_en {<br>Interprete[`0..N`] -> Pelicula[`1..N`]<br>}                                        | ![](BD%20-%20BigER%20Relación%200N%201N%20Crows%20Foot.png)           |
| Relación con cardinalidad mínima y máxima 0..1 en un lado y  0..N  en el otro | `entity` Socio_Biblioteca {<br>..<br>}<br>`entity` Ejemplar_de_Libro {<br>..<br>}<br>`relationship` tiene_en_prestamo {<br>Socio_Biblioteca[`0..1`] -> Ejemplar_de_Libro[`0..N`]<br>} | ![](BD%20-%20BigER%20Relación%2001%200N%20Crows%20Foot.png)           |

---
### 3.5 Relaciones Recursivas: Cuando una Entidad se Relaciona Consigo Misma

El caso de la relación `SUPERVISA` es especial. Aquí, el tipo de entidad `EMPLEADO` se relaciona consigo mismo. Esto se llama **relación recursiva**.

Para que tenga sentido, la entidad participa en la relación desempeñando distintos **roles**.

> **Analogía del Teatro 🎭**: Piensa en el tipo de entidad `EMPLEADO` como un grupo de actores. En la relación `SUPERVISA`, un actor interpreta el **rol de "supervisor"** y otro actor interpreta el **rol de "supervisado"**. La relación conecta a un empleado en un rol con otro empleado en otro rol.

Cada instancia de la relación (ej: "Ana supervisa a Juan") siempre implicará a dos entidades distintas, aunque ambas pertenezcan al mismo tipo `EMPLEADO`.

---
>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > |  Concepto                             | Textual en BigER                                                                                                                  | Notación Crow's foot en BigER                                           |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Relación recursiva con roles | `entity` Usuario {<br>..<br>}<br>`relationship` sigue {<br>Usuario[`1..N` \|  `"Seguidor"`] -> Usuario[`1..N`\| `"Seguido"`]<br>} | ![](BD%20-%20BigER%20Relación%20Recursiva%20Crows%20Foot.png) |

---
### 3.6 Atributos en las Relaciones: Describiendo la Conexión

A veces, una característica no describe a una entidad, sino a la **interacción entre ellas**. En esos casos, el atributo pertenece a la relación.

> **Ejemplo  (TRABAJA_EN)**: Un empleado trabaja un número de `Horas` semanales en un proyecto.
> 
> - Las `Horas` no son un atributo del `EMPLEADO`, porque esa persona trabajará un número diferente de horas en cada proyecto.
>     
> - Las `Horas` no son un atributo del `PROYECTO`, porque diferentes empleados le dedican diferentes horas.
>     
> 
> Las `Horas` son una propiedad de la **conexión específica** entre un empleado y un proyecto. Por lo tanto, `Horas` es un atributo del tipo de relación `TRABAJA_EN`.

**Regla general**:

- En relaciones **M:N**, los atributos que describen la interacción **deben** ir en la relación.
    
- En relaciones **1:N**, el atributo se puede mover al tipo de entidad del lado "N".
    
- En relaciones **1:1**, el atributo se puede mover a cualquiera de las dos entidades.

---

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
  > | Concepto                      | Textual en BigER                                                     | Notación Crow's foot en BigER                                                                                                                                                                                                                                                           |
| -------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Atributo de relación | `relationship` **relacion** {<br>..<br>**atributo_de_relacion**<br>} | **Cuando pasemos el cursor en BigER por encima de la relación veremos que nos muestra el atributo**<br>![](BD%20-%20BigER%20Atributo%20de%20Relación%20marcado.png)<br>**Cuando lo dibujemos a mano, pondremos una línea conectando al atributo**<br>![](BD%20-%20BigER%20Atributo%20de%20Relación.png)<br> |

---
### 3.7 Primera versión (incompleta e incorrecta) del Diagrama ER de Empresa

Con lo que sabemos hasta ahora, si tuvierámos que modelar el Diagrama Entidad Relación de la Empresa, podríamos obtener el siguiente diagrama que, aunque se acerca bastante a la versión final, no está completo ni es totalmente correcto.

![BD Empresa versión inicial incorrecta - BigER](BD%20Empresa%20versión%20inicial%20incorrecta%20-%20BigER.md)



>[!question] Pregunta
>	¿Es correcto el atributo `nombre` como clave primaria de la Entidad `FAMILIAR`?

>[!question] Pregunta
>	¿Es correcto el atributo multivaluado `ubicaciones` en la Entidad `DEPARTAMENTO`?

## 4: El Arte del Buen Diseño - Criterios y Decisiones Clave

Saber qué son las entidades, atributos y relaciones es solo la mitad del camino. La otra mitad es saber **cuándo y cómo usarlos correctamente**. Un buen diseñador de bases de datos es como un buen arquitecto: toma decisiones informadas para crear un modelo que sea sólido, lógico y fácil de entender. A continuación, exploramos los criterios más importantes.

### 4.1 ¿Entidad o Atributo? La Primera Gran Decisión 🤔

A menudo te encontrarás con un concepto y dudarás: ¿debería ser su propia entidad con un rectángulo en el diagrama, o simplemente un atributo (un óvalo) dentro de otra entidad?

**La regla de oro**: Será una **entidad** si necesitas almacenar información descriptiva sobre ese concepto. Si solo necesitas su valor, será un **atributo**.

- **Ejemplo**: Consideremos la "capital" de un `PAIS`.
               
    - **Si necesitamos almacenar información descriptiva -> (Entidad)**: Si creamos una entidad `CIUDAD` y la conectamos con `PAIS` a través de una relación `esCapital`, ahora podemos almacenar todos los detalles que queramos sobre la capital (habitantes, extensión, etc.).
    
    - **Si solo su valor -> Atributo**: Si `Capital` es solo un atributo de `PAIS`, de la que no necesitamos más información, podemos dejarlo como atributo.
        

### 4.2 El Error Más Común: Usar Claves Ajenas como Atributos 🚫

Este es un **==error crítico en la fase de diseño conceptual==**. La regla es simple: **==el identificador de una entidad NUNCA debe aparecer como un atributo normal en otra entidad==**. La conexión entre ellas siempre debe ser una **relación**.

- **Ejemplo**: Queremos registrar qué profesor es el `responsable` de una `ASIGNATURA`.
    
    - **Mal diseño (Atributo)**: Incluir un atributo llamado `responsable` dentro de la entidad `ASIGNATURA` donde guardaríamos el NIF del profesor. Esto es incorrecto porque mezcla el nivel conceptual (¿quién es responsable?) con el nivel de implementación (guardar un NIF).
        
    - **Buen diseño (Relación)**: Creamos una relación llamada `RESPONSABLE` que conecta `PROFESOR` y `ASIGNATURA`. Esto describe la realidad de forma mucho más clara y correcta.

### 4.3 Otros Criterios Fundamentales

1. **Estandarizar con Entidades**: Si un concepto, aunque sea solo un código o un nombre, se va a asociar con muchas otras entidades, es una buena práctica modelarlo como una entidad propia. Esto ayuda a estandarizar los valores y evitar errores de escritura. Por ejemplo, en lugar de tener un atributo `Provincia` en la entidad `MUNICIPIO`, es mejor crear una entidad `PROVINCIA` y relacionarlas.
    
2. **Evita la "Super Entidad"**: **==Es un error grave intentar modelar toda la organización (por ejemplo, la "EMPRESA") como una única entidad gigante==**. El objetivo es descomponer el minimundo en sus partes conceptuales más pequeñas y significativas.
    
3. **La Claridad es la Reina**: Usa siempre nombres adecuados, legibles y fáciles de entender para tus entidades, relaciones y atributos. Un diagrama bien nombrado se documenta a sí mismo. Piensa en quien tenga que leer tu modelo dentro de seis meses, incluso tu mismo puedes ser ese lector que ya no lo recuerde.

---
## 5: Entidades Débiles - Cuando una Entidad Necesita Ayuda para Identificarse

### 5.1 Entidades Fuertes vs. Débiles: La Diferencia Clave

Hasta ahora, todos los tipos de entidad que hemos visto son **fuertes** (o regulares). ¿Qué significa esto? Que tienen su propio **atributo clave** (como `DNI` para `EMPLEADO` o `ISBN` para `LIBRO`) que les permite identificar de forma única a cada una de sus instancias. Son independientes y se valen por sí mismas.

Pero a veces, nos encontramos con entidades que no pueden ser identificadas de forma única solo con sus propios atributos. Estas se conocen como **tipos de entidad débiles**.

 ** Veamos qué ocurre con Empleado y Familiar:**
 
 - **Entidad Fuerte:**  La entidad `EMPLEADO` es una **entidad fuerte** porque posee un atributo clave, el `dni`, que la identifica de forma única en toda la empresa. Cada empleado tiene un DNI y no hay dos iguales.
 - **Entidad Débil:** En cambio, en la primera versión de la Empresa, `FAMILIAR` es un ejemplo perfecto de una **entidad débil**. Si intentamos usar su atributo `nombre` como clave, nos topamos con un problema evidente: es muy probable que varios empleados tengan familiares con el mismo nombre, como "María García" o "Carlos Sánchez". Por sí solo, el `nombre` no garantiza la unicidad en toda la base de datos. Para resolver esto, la entidad `FAMILIAR` se apoya en su entidad propietaria (`EMPLEADO`). Su identificador único y completo no es solo su `nombre` (que actúa como clave parcial), sino la combinación de su clave parcial con la clave de su "dueño". Así, la identidad inequívoca de un familiar es la combinación del **`dni` del empleado** + el **`nombre` del familiar**.

En definitiva, una entidad débil necesita "apoyarse" en otra entidad para poder ser identificada 🤝. 

---
### 5.2 Las dos condiciones a cumplir: Existencia e Identificación

Para que una entidad sea débil, debe cumplir con dos condiciones muy específicas.
#### A) Dependencia de Existencia

Esto significa que una entidad no puede existir si la entidad de la que depende desaparece.

Pensemos en la entidad `FAMILIAR`. Es evidente que un familiar solo tiene sentido en nuestra base de datos si está asociado a un `EMPLEADO`. Si un empleado deja la empresa y eliminamos su registro, los registros de sus familiares a cargo ya no tienen razón de ser. Esto es una clara **dependencia de existencia**.

**¡Pero Cuidado!** Este es solo el primer paso. Tener dependencia de existencia no convierte automáticamente a una entidad en débil. Necesita cumplir la segunda condición, que es la más importante.
#### B) Dependencia de Identificación

Esta es la verdadera marca de una entidad débil. Significa que, además de depender para existir, **no se puede identificar de forma única por sí misma** usando solo sus propios atributos.

Volvamos a la entidad `FAMILIAR`. Como ya vimos, su atributo `nombre` no es suficiente para identificar a una persona de forma inequívoca en toda la empresa, ya que podría haber muchos familiares con el mismo nombre. Para encontrar a un familiar específico (por ejemplo, "Ana García"), necesitamos preguntar: "¿Ana García, familiar de qué empleado?".

La identidad única solo se consigue al combinar la clave de su entidad "dueña" (`dni` del `EMPLEADO`) con su propio atributo discriminante (`nombre`). Esta necesidad de "tomar prestada" la clave de la entidad fuerte para poder identificarse es lo que se conoce como **dependencia de identificación**. Es esta segunda condición la que convierte a `FAMILIAR` en una entidad débil.

---
### 5.3 La Anatomía de una Entidad Débil

Una entidad débil siempre viene acompañada de dos elementos que la definen:

1. **Entidad Propietaria (o Fuerte)**: Es la entidad de la que depende (ej: `EMPLEADO`).
    
2. **Relación débil de Identificación**: la conexión entre una entidad fuerte y su entidad débil **siempre** es mediante una relación, también denominada débil, que tendrá cardinalidad  `(1,1)` en el lado de la entidad fuerte y `(0,N)` o `(1,N)` en la lado de la entidad débil. Es decir, una instancia de la entidad débil no puede existir sin su entidad fuerte de la que depende y solo tiene una. Mientras que una instancia de la entidad fuerte puede no estar relacionada con ninguna de la entidad débil, con una o con muchas.
    
3. **Clave Parcial (o Discriminante)**: Es el atributo (o conjunto de atributos) dentro de la entidad débil que la identifica de forma única **entre sus hermanas**, es decir, entre todas las que dependen del mismo propietario. En nuestro ejemplo, el `Nombre` del `FAMILIAR` es la clave parcial.
    
**Clave Primaria de la Entidad Débil**: La clave primaria completa de una entidad débil se forma combinando la **clave primaria de su entidad propietaria** con su propia **clave parcial**.

---

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
>
> | Concepto               | Textual en BigER                                                                                                                                           | Notación Crow's foot en BigER                        |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Entidad débil  | `weak entity` **Entidad_Debil** {<br>clave_parcial `partial-key`<br>..<br>}                                                                                | ![](BD%20-%20BigER%20Entidad%20Débil.png)  |
| Relación débil | `weak relationship` **relacion_debil** {<br>Entidad[`1..1`] -> Entidad_Debil[`0..N`]<br>//dependiendo del caso, también puede ser Entidad_Debil[`1..N`]<br>} | ![](BD%20-%20BigER%20Relación%20débil.png) |


---
### 5.4 Casos de Uso de Entidades Débiles

Aunque el concepto puede parecer complejo, las entidades débiles son la solución elegante a varios problemas de modelado muy comunes. Sin embargo, no se debe hacer un mal uso de ellas y solo el correcto usarlas en estos casos:

#### 5.4.1 Caso 1: Para modelar atributos multivalor de una entidad

Queremos modelar `CURSO` y las distintas repeticiones de dicho curso a lo largo del tiempo en un atributo `edicion`.
Además, cada edición tomará sus propios valores en `FechaInicio`, `FechaFin` y `Aula`. 
No podemos modelarlo como se ve a continuación

| BigER                                                                                                       | Crow's foot                                                |
| ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `entity` Curso {<br>idCurso `key`<br>nombreCurso<br>¿edicion?<br>¿fechaInicio?<br>¿fechaFin?<br>¿aula?<br>} | ![](BD%20-%20BigER%20Entidad%20Curso%20Foot.png) |
**Solución**: El posible atributo multivalor `edicion` hay que convertirlo en la entidad débil `EDICION` que depende de `CURSO`. Su clave parcial puede ser `FechaInicio` si sabemos que no hay dos o más ediciones que comiencen en la misma fecha. Así, podemos almacenar toda la información de cada edición de forma ordenada.

| BigER                                                                                                                                                                                                                                | Crow's foot                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
| ```entity``` Curso {<br>idCurso ```key```<br>nombreCurso<br>}<br>==weak entity== Edicion {<br>fechaInicio ==partial-key==<br>fechaFin<br>aula<br>}<br>==weak relationship== tiene {<br>Curso[```1..1```] -> Edicion[```0..N```]<br>} | ![](BD%20-%20BigER%20Debil%20Edicion%20Crows%20Foot.png) |
#### 5.4.2 Caso 2: Para gestionar atributos multivaluados en una relación M:N con repetición de relaciones entre instancias.

En el mundo real, las relaciones no son estáticas; muchas cambian con el tiempo. Un empleado cambia de departamento, un médico deja de estar adscrito a un hospital, un proveedor suministra la misma pieza a un proyecto en diferentes fechas. Hasta ahora sabemos cómo hacer la "foto" actual, pero ¿cómo guardamos el **álbum de fotos completo**?

Por ejemplo, un `PROFESOR` imparte una `ASIGNATURA`. Esta es una relación M:N. Ahora queremos registrar en qué **año académico (`curso`)** se impartió cada docencia. El problema es que un profesor puede impartir la asignatura de "BD" en múltiples cursos (20/21, 22/23, 23/24). Es decir, el mismo par (Profesor, Asignatura) se puede repetir por lo que el atributo curso de la relación `imparte` realmente sería multivaluado.

| BigER                                                                                                                                                                                                                                     | Crow's foot                                                              |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `entity` PROFESOR {<br>nif `key`<br>nombre<br>apellido1<br>apellido2<br>}<br><br>`entity` ASIGNATURA {<br>cod_asignatura `key`<br>nombre<br>}<br><br>```relationship``` imparte {<br>PROFESOR[`1..N`] -> ASIGNATURA[`1..N`]<br>curso<br>} | ![](BD%20-%20BigER%20Profesor%20Asignatura%20Crows%20Foot.png) |
**Solución**: la solución es un patrón de diseño muy potente: "reificar" la relación. Esto significa convertir la interacción a lo largo del tiempo en una **entidad débil con repetición** (o histórica). Esta nueva entidad guardará cada "evento" de la relación, y su clave primaria casi siempre incluirá un atributo de tiempo (como `curso`o `fecha`).

En nuestro ejemplo de un `PROFESOR` imparte una `ASIGNATURA` reificamos" la relación, es decir, la convertimos en una entidad débil llamada `DOCENCIA`. Esta entidad depende de `PROFESOR` y `ASIGNATURA`, y su clave parcial es `curso`. Su clave completa sería (`nif_profesor`, `cod_asignatura`, `curso`), que ahora sí es única.

| BigER                                                                                                                                                                                                                                                                                                                                                                                 | Crow's foot                                                         |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `entity` PROFESOR {<br>nif `key`<br>nombre<br>apellido1<br>apellido2<br>}<br>`entity` ASIGNATURA {<br>cod_asignatura `key`<br>nombre<br>}<br><br>==weak entity== DOCENCIA {<br>curso ==partial-key==<br>}<br><br>==weak relationship== imparte {<br>PROFESOR[`1..1`] -> DOCENCIA[`1..N`]<br>}<br><br>==weak relationship== relativaA {<br>DOCENCIA[`1..N`] -> ASIGNATURA[`1..1`]<br>} | ![](BD%20-%20BigER%20Debil%20Docencia%20Crows%20Foot.png) |

Sin embargo, la clave para un diseño correcto es decidir de qué entidad o entidades debe depender esta nueva entidad histórica. La respuesta depende de la cardinalidad de la relación **en un instante de tiempo**. Analicemos los tres casos posibles.

---
##### 5.4.2.1 Caso 2.1: Dependencia Doble

- **La Regla de Negocio**: En una fecha concreta, una instancia de la Entidad1 puede relacionarse con **varias** instancias de la Entidad2, y viceversa.
    
- **Análisis**: Para identificar un evento único, no basta con saber `(Entidad1, Fecha)`, porque en esa fecha podría estar relacionada con varias Entidad2. Tampoco basta con `(Entidad2, Fecha)`. Necesitamos la combinación de los tres elementos para garantizar la unicidad.
    
- **Solución de Diseño**: La entidad débil histórica **debe depender de ambas entidades propietarias**. Es el caso que se en ejemplo que ya hemos visto de un `PROFESOR` imparte una `ASIGNATURA`.

- **Error a Evitar**: Hacerla depender de una sola entidad.    
###### Ejemplo 5.4.2.1.1: Matrículas en una Universidad

- **Entidades**: `ESTUDIANTE`, `ASIGNATURA`.
    
- **Contexto Histórico**: Necesitamos guardar un registro de qué estudiantes se han matriculado en qué asignaturas a lo largo de los diferentes cursos académicos.
    
- **Regla de Negocio**: En un `AñoAcadémico` concreto, un `ESTUDIANTE` se matricula en **varias instancias** de `ASIGNATURA`. A su vez, una `ASIGNATURA` tiene **varias instancias** de `ESTUDIANTE` matriculados en ese mismo año.
    
- **Análisis de Unicidad**: Para identificar una matrícula específica, ¿es suficiente con `(ID_Estudiante, CursoAcadémico)`? No, porque ese estudiante cursó varias asignaturas ese año. ¿Y con `(Cod_Asignatura, CursoAcadémico)`? Tampoco, porque en esa asignatura había muchos alumnos. Para identificar de forma inequívoca una única matrícula, necesitamos saber **qué alumno**, en **qué asignatura** y en **qué curso**.
    
- **Solución de Diseño**:
    
    1. Creamos una entidad débil llamada `MATRICULA`.
        
    2. Esta entidad debe depender tanto de `ESTUDIANTE` como de `ASIGNATURA`.
        
    3. Su clave primaria se forma con las claves de ambas propietarias más el atributo de tiempo: **{ID_Estudiante (FK), Cod_Asignatura (FK), CursoAcadémico}**.


| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Crow's foot                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| ```entity``` ESTUDIANTE {<br>id_estudiante ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` ASIGNATURA {<br>cod_asignatura ```key```<br>nombre<br>}<br><br>==weak entity== MATRICULA {<br>curso_academico ==partial-key==<br>}<br><br>==weak relationship== ha_realizado {<br>ESTUDIANTE[```1..1```] -> MATRICULA[```1..N```]<br>}<br><br>==weak relationship== corresponde_a {<br>MATRICULA[```1..N```] -> ASIGNATURA[```1..1```]<br>} | ![](BD%20-%20BigER%20Debil%20Matricula%20Crows%20Foot.png) |
###### Ejemplo 5.4.2.1.2: Reservas de Vuelos

- **Entidades**: `PASAJERO`, `VUELO`.
    
- **Contexto Histórico**: Una aerolínea necesita un registro de cada reserva individual.
    
- **Regla de Negocio**: En una transacción de reserva realizada en una `Fecha_Reserva` específica, un `PASAJERO` (o un grupo de ellos bajo el mismo localizador) se reserva en **varios** `VUELOS` (ej: un viaje con escalas). A su vez, un `VUELO` tiene **varios** `PASAJEROS` a bordo.
    
- **Análisis de Unicidad**: Una reserva concreta no puede ser identificada solo por `(ID_Pasajero, Fecha_Reserva)` si el pasajero reservó un viaje con múltiples vuelos. Tampoco por `(ID_Vuelo, Fecha_Reserva)`, ya que en ese vuelo hay muchos pasajeros. La reserva es la combinación de un pasajero específico en un vuelo específico en un momento dado.
    
- **Solución de Diseño**:
    
    1. Creamos la entidad débil histórica `RESERVA`.
        
    2. `RESERVA` depende de `PASAJERO` y `VUELO`.
        
    3. Su clave primaria es la combinación de las tres piezas de información: **{ID_Pasajero (FK), ID_Vuelo (FK), Fecha_Reserva}**.

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Crow's foot                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| ```entity``` PASAJERO {<br>id_pasajero ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` VUELO {<br>id_vuelo ```key```<br>origen<br>destino<br>fecha_hora_salida<br>}<br><br>==weak entity== RESERVA {<br>fecha_reserva ==partial-key==<br>}<br><br>==weak relationship== es_realizada_por {<br>PASAJERO[```1..1```] -> RESERVA[```1..N```]<br>}<br><br>==weak relationship== incluye {<br>VUELO[```1..1```] -> RESERVA[```0..N```]<br>} | ![](BD%20-%20BigER%20Debil%20Reserva%20Crows%20Foot.png) |
   
---

##### 5.4.2.2 Caso 2.2: Dependencia Simple a Elegir

- **La Regla de Negocio**: En una fecha concreta, una instancia de la Entidad1 solo puede relacionarse con **una** instancia de la Entidad2, y viceversa.
    
- **Análisis**: Dado que la conexión es única por ambos lados en cualquier fecha, tanto `(Entidad1, Fecha)` como `(Entidad2, Fecha)` son combinaciones que identifican unívocamente el evento.
    
- **Solución de Diseño**: La entidad débil histórica puede depender **o de la Entidad1 o de la Entidad2**. La elección depende del significado del negocio.

- **Error a Evitar**: Hacerla depender de ambas entidades a la vez.
    
###### Ejemplo 5.4.2.2.1: Asignación de Coche de Empresa

- **Entidades**: `EMPLEADO`, `VEHICULO`.
    
- **Contexto Histórico**: La empresa necesita un historial de qué empleado ha tenido asignado cada coche de la flota a lo largo del tiempo.
    
- **Regla de Negocio**: En una `FechaAsignacion` concreta, un `EMPLEADO` solo puede tener asignado **un** `VEHICULO` de la empresa. A su vez, un `VEHICULO` solo puede estar asignado a **un** `EMPLEADO` en esa fecha.
    
- **Análisis de Unicidad**: Si conocemos al `EMPLEADO` y la `FechaAsignacion`, sabemos inequívocamente qué coche tenía. Del mismo modo, si conocemos el `VEHICULO` y la `FechaAsignacion`, sabemos quién lo conducía. Ambos pares, `(ID_Empleado, FechaAsignacion)` y `(Matricula_Vehiculo, FechaAsignacion)`, son únicos.
    
- **Solución de Diseño**:
    
    1. Creamos la entidad débil `ASIGNACION_HISTORICA`.
        
    2. Podemos elegir de quien depende. 
	    1. Si el foco del sistema es el historial del empleado, la hacemos depender de `EMPLEADO`. 
	    2. Si el foco es el historial del vehículo, la hacemos depender de `VEHICULO`.
        
    3. Podemos elegir la clave primaria:
	    1. **Opción A (foco en Empleado)**: Clave Primaria = **{ID_Empleado (FK), FechaAsignacion}**.
	    2. **Opción B (foco en Vehículo)**: Clave Primaria = **{Matricula_Vehiculo (FK), FechaAsignacion}**.
        

**Opción A (foco en Empleado)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | Crow's foot                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| ```entity``` EMPLEADO {<br>id_empleado ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` VEHICULO {<br>matricula_vehiculo ```key```<br>marca<br>modelo<br>}<br><br>==weak entity== ASIGNACION_HISTORICA {<br>fecha_asignacion ==partial-key==<br>}<br><br>==weak relationship== ha_tenido {<br>EMPLEADO[```1..1```] -> ASIGNACION_HISTORICA[```1..N```]<br>}<br><br>```relationship``` incluye {<br>VEHICULO[```1..1```] -> ASIGNACION_HISTORICA[```0..N```]<br>} | ![](BD%20-%20BigER%20Debil%20Asignacion%20Historica%20Crows%20Foot.png) |
   
   **Opción B (foco en Vehículo)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | Crow's foot                                                                     |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| ```entity``` EMPLEADO {<br>id_empleado ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` VEHICULO {<br>matricula_vehiculo ```key```<br>marca<br>modelo<br>}<br><br>==weak entity== ASIGNACION_HISTORICA {<br>fecha_asignacion ==partial-key==<br>}<br><br>```relationship``` ha_tenido {<br>EMPLEADO[```1..1```] -> ASIGNACION_HISTORICA[```1..N```]<br>}<br><br>==weak relationship== incluye {<br>VEHICULO[```1..1```] -> ASIGNACION_HISTORICA[```0..N```]<br>} | ![](BD%20-%20BigER%20Debil%20AsignacionHistorica2%20Crows%20Foot.png) |
   
###### Ejemplo 5.4.2.2.2: Gestor de Clientes VIP

- **Entidades**: `GESTOR_DE_CUENTAS`, `CLIENTE_VIP`.
    
- **Contexto Histórico**: Una empresa de servicios premium asigna un gestor de cuentas exclusivo a cada uno de sus clientes más importantes. Esta asignación se revisa y puede cambiar cada trimestre fiscal. Se necesita un historial de estas asignaciones.
    
- **Regla de Negocio**: Durante un `TRIMESTRE` específico (ej: "2025-T4"), un `GESTOR_DE_CUENTAS` solo puede ser el responsable principal de **un** `CLIENTE_VIP`, y un `CLIENTE_VIP` solo tiene asignado a **un** `GESTOR_DE_CUENTAS` principal. 
    
- **Análisis de Unicidad**: Si conocemos al `GESTOR_DE_CUENTAS` y el `TRIMESTRE`, sabemos de qué cliente era responsable. De la misma manera, si conocemos al `CLIENTE_VIP` y el `TRIMESTRE`, sabemos quién era su gestor asignado. Ambas perspectivas son válidas y únicas.
    
- **Solución de Diseño**:
    
    1. Creamos la entidad débil `ASIGNACION_VIP`.
        
    2. Podemos elegir de quien depende. 
	    1. Si el foco del sistema es la **cartera de clientes de cada gestor**, la hacemos depender de `GESTOR_DE_CUENTAS`. 
	    2. Si el foco es el el **historial de gestores que ha tenido un cliente**, la entidad dependerá de `CLIENTE_VIP`.
        
    3. Podemos elegir la clave primaria:
	    1. **Opción A (Foco en el Gestor)**: La clave primaria de `ASIGNACION_VIP` es **{ID_Gestor (FK), Trimestre}**.
	    2. **Opción B (Foco en el Cliente)**: La clave primaria de `ASIGNACION_VIP` es **{ID_Cliente (FK), Trimestre}**..
        

**Opción A (foco en Gestor)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Crow's foot                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| ```entity``` GESTOR_DE_CUENTAS {<br>id_empleado ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` CLIENTE_VIP {<br>id_cliente_vip ```key```<br>nombre_empresa<br>cif ```//UNIQUE```<br>}<br><br>==weak entity== ASIGNACION_VIP {<br>trimestre ==partial-key==<br>}<br><br>==weak relationship== es_responsable_de {<br>GESTOR_DE_CUENTAS[```1..1```] -> ASIGNACION_VIP[```1..N```]<br>}<br><br>```relationship``` asigna_a{<br>ASIGNACION_VIP[```1..N```] -><br>CLIENTE_VIP[```1..1```] -> <br>} | ![](BD%20-%20BigER%20Debil%20Asignacion%20VIP2%20Crows%20Foot.png) |
   
   **Opción B (foco en Cliente)**

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Crow's foot                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| ```entity``` GESTOR_DE_CUENTAS {<br>id_empleado ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` CLIENTE_VIP {<br>id_cliente_vip ```key```<br>nombre_empresa<br>cif ```//UNIQUE```<br>}<br><br>==weak entity== ASIGNACION_VIP {<br>trimestre ==partial-key==<br>}<br><br>```relationship``` es_responsable_de {<br>GESTOR_DE_CUENTAS[```1..1```] -> ASIGNACION_VIP[```1..N```]<br>}<br><br>==weak relationship== asigna_a{<br>ASIGNACION_VIP[```1..N```] -><br>CLIENTE_VIP[```1..1```] -> <br>} | ![](BD%20-%20BigER%20Debil%20Asignacion%20VIP%20Crows%20Foot.png) |
   
---
##### 5.4.2.3 Caso 2.3: Dependencia Simple Obligatoria

- **La Regla de Negocio**: En una fecha concreta, una instancia de la Entidad1 solo puede relacionarse con **una** instancia de la Entidad2, pero una instancia de la Entidad2 puede relacionarse con **varias** de la Entidad1.
    
- **Análisis**: Debemos encontrar el lado de la relación que garantiza la unicidad cuando se combina con la fecha. El lado "1" de la relación es el que nos sirve.
    
- **Solución de Diseño**: La entidad débil histórica **debe depender obligatoriamente de la Entidad1**, es decir, la que solo puede tener una relación en esa fecha.

- **Error a Evitar**: hacerla depender de la Entidad2, es decir, de la que puede tener varias relaciones en esa fecha, o hacerla depender de ambas.
    
###### Ejemplo 5.4.2.3.1: Asignación Departamental de Empleados

- **Entidades**: `EMPLEADO`, `DEPARTAMENTO`.
    
- **Contexto Histórico**: Recursos Humanos necesita el historial completo de los departamentos por los que ha pasado cada empleado.
    
- **Regla de Negocio**: En una `fecha_inicio` concreta, un `EMPLEADO` solo puede pertenecer a **un** `DEPARTAMENTO`. Sin embargo, un `DEPARTAMENTO` está formado por **varios** `EMPLEADOS` en esa misma fecha.
    
- **Análisis de Unicidad**:
    
    - ¿El par `(id_empleado, fecha_inicio)` es único? **Sí**. Nos dice exactamente en qué departamento empezó a trabajar ese empleado en esa fecha.
        
    - ¿El par `(id_dpto, fecha_inicio)` es único? **No**. En esa fecha, varios empleados pudieron empezar a trabajar en ese mismo departamento.
        
- **Solución de Diseño**:
    
    1. Creamos la entidad débil `HISTORIAL_DEPTO`.
        
    2. La dependencia es forzada. Debe depender de `EMPLEADO`.
        
    3. Su clave primaria se forma obligatoriamente con la clave del empleado y la fecha: **{id_empleado (FK), fecha_inicio}**.
        

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Crow's foot                                                                 |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| ```entity``` EMPLEADO {<br>id_empleado ```key```<br>dni ```//UNIQUE```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` DEPARTAMENTO {<br>id_dpto ```key```<br>nombre_dpto<br>}<br><br>==weak entity== HISTORIAL_DPTO {<br>fecha_inicio ==partial-key==<br>}<br><br>==weak relationship== ha_trabajado {<br>EMPLEADO[1..1] -> HISTORIAL_DPTO[0..N]<br>}<br><br>```relationship``` pertenece_a {<br>HISTORIAL_DPTO[1..N] -> DEPARTAMENTO[1..1]<br>} | ![](BD%20-%20BigER%20Debil%20Historial%20Dpto%20Crows%20Foot.png) |
   
###### Ejemplo 5.4.2.3.2: Asignación de Tutor Académico 

- **Entidades**: `ESTUDIANTE`, `TUTOR`.
    
- **Contexto Histórico**: Una universidad necesita mantener un historial de qué `TUTOR` ha sido asignado a cada `ESTUDIANTE` a lo largo de los diferentes semestres.
    
- **Regla de Negocio**: En un `SEMESTRE` concreto, un `ESTUDIANTE` solo puede tener asignado a **un** `TUTOR` académico. Sin embargo, un `TUTOR` puede ser responsable de **varios** `ESTUDIANTES` en ese mismo semestre.
    

**Análisis de Unicidad**:

- ¿El par `(id_estudiante, semestre)` es único? **Sí**. Identifica de forma inequívoca quién era el tutor de ese estudiante en ese semestre específico.
    
- ¿El par `(id_tutor, semestre)` es único? **No**. En un semestre, un tutor tiene a su cargo a múltiples estudiantes, por lo que esta combinación se repetiría.
    

**Solución de Diseño**:

1. Creamos la entidad débil `ASIGNACION_TUTORIA`.
    
2. La dependencia es obligatoria y debe ser sobre la entidad `ESTUDIANTE`, que es el lado "N" de la relación.
    
3. Su clave primaria se forma obligatoriamente con la clave del estudiante y el semestre: **{id_estudiante (FK), semestre}**.


| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Crow's foot                                                                     |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| ```entity``` ESTUDIANTE {<br>id_estudiante ```key```<br>nombre<br>apellido1<br>apellido2<br>}<br><br>```entity``` TUTOR {<br>id_tutor ```key```<br>nombre<br>apellido1<br>apellido2<br>departamento<br>}<br><br>==weak entity== ASIGNACION_TUTORIA {<br>semestre ```partial-key```<br>}<br><br>==weak relationship== tiene_asignado {<br>ESTUDIANTE[```1..1```] -> ASIGNACION_TUTORIA[```0..N```]<br>}<br><br>```relationship``` es_realizada_por {<br>ASIGNACION_TUTORIA[```0..N```] -> TUTOR[```1..1```]<br>} | ![](BD%20-%20BigER%20Debil%20Asignacion%20Tutoria%20Crows%20Foot.png) |
   
---
### 5.5: Un caso especial: las entidades asociativas para  relacionar una entidad con una  relación M:N.

El modelo Entidad-Relación tiene una regla fundamental e inquebrantable: **es imposible conectar usando una relación a una otra relación directamente con una entidad**. Un verbo no puede ser el sujeto de otra acción. Pero, en el modelado de datos, a veces nos encontramos que una interacción de **muchos-a-muchos (M:N) sin repetición** es tan importante que necesitamos conectarla con otras entidades.

#### El Problema Guiado: Asignación de Competencias a Puestos de Trabajo

**El Escenario Inicial:** En el departamento de Recursos Humanos, se definen los `PUESTOS_DE_TRABAJO` y un catálogo de `COMPETENCIAS` profesionales. La relación entre ellos es de muchos-a-muchos (M:N):

- Un `PUESTO_DE_TRABAJO` (ej: "Analista de Datos") requiere **varias** `COMPETENCIAS` (ej: "SQL Avanzado", "Visualización de Datos").
    
- Una `COMPETENCIA` (ej: "Liderazgo de Equipos") es requerida por **varios** `PUESTOS_DE_TRABAJO`.
    

La regla de negocio es que una competencia se asigna una sola vez a cada puesto. Este es el modelo inicial:

**El Nuevo Requisito (El Muro Conceptual):** Ahora, RRHH necesita que cada una de estas asignaciones sea certificada. Es decir, se debe registrar qué `VALIDADOR` (un directivo o experto) ha aprobado que la competencia "SQL Avanzado" es necesaria para el puesto "Analista de Datos".

El `VALIDADOR` no certifica el puesto en general, ni la competencia en general, sino la **regla de asignación** entre ambos. Si intentamos conectar la entidad `VALIDADOR` directamente a la relación "REQUIERE", nos topamos con el muro conceptual del modelo E/R: **es imposible conectar una relación con otra relación **.

---
#### La Solución: "Cosificar" la Relación en una Entidad Asociativa

La solución es transformar la relación "REQUIERE" en una entidad asociativa que actúe como un conector.

Una entidad asociativa es, por tanto, un híbrido: nace de una relación M:N para darle cuerpo y permitir que se conecte con otras partes del modelo. Se comporta como una **tabla `join` en el mundo real**, existiendo únicamente para conectar dos conceptos y cuya identidad es la combinación de las dos cosas que conecta.

**Paso 1: Convertir la Relación en una Entidad (el Puente)** Transformamos la relación en una entidad. Es decir, la relación M:N se convierte en la entidad asociativa `REQUISITO_DE_PUESTO`. Cada instancia de esta nueva entidad representa el hecho único y no repetible de que un puesto específico requiere una competencia específica.

**Paso 2: Analizar la Identidad del Puente (La Clave Compuesta)** Esta nueva entidad `REQUISITO_DE_PUESTO` **no aporta ningún identificador propio**. Es, por naturaleza, una entidad débil. Su única identidad es la combinación de las claves de las entidades que une. No necesita una clave parcial propia porque la relación no se repite; un puesto solo requiere una competencia una vez.

- **Clave Primaria 🔑 = {id_puesto (FK), id_competencia (FK)}**
    

**Paso 3: Usar el Puente para Conectar** Ahora que `REQUISITO_DE_PUESTO` es una entidad, ya podemos conectarla con `VALIDADOR` a través de una nueva relación binaria, `ES_VALIDADO_POR`.

**El Modelo Final y su Significado:** El diagrama final representa la realidad de forma lógica y correcta:

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: Se define un `REQUISITO_DE_PUESTO` (un puesto de trabajo necesita una competencia).
    
2. **Hecho 2**: Ese `REQUISITO` específico es certificado por un `VALIDADOR`.


>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> 
> | Concepto                     | BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Entidad asociativa en Big ER | entity ```ENTIDAD1``` {<br>id_1 ```key```<br>atributos_de_1<br>}<br>entity ```ENTIDAD2``` {<br>id_2 ```key```<br>atributos_de_2<br>}<br><br>// Entidad asociativa que "cosifica" la relación M:N<br>// Su clave primaria es la combinación de id_1 e id_2<br>//**No es soportada por BigER, lo que haremos es comenzar siempre el nombre de este tipo de entidades con ASOC_ y pondremos los dos atributos claves de las entidades que asocia como claves de la entidad asociativa**<br>```entity``` ==ASOC_ENTIDAD_12 =={<br>==id_1_ key==<br>==id_2 key==<br>}<br><br>// Las relación original M:N ahora son dos relaciones 1:N hacia la entidad asociativa.<br>relationship entidad1_participa_en {<br>ENTIDAD1[```1..1```] -> ==ASOC_ENTIDAD_12==[```0..N```]<br>}<br><br>relationship entidad2_participa_en {<br>ENTIDAD2[```1..1```] -> ==ASOC_ENTIDAD_12==[```0..N```]<br>}<br><br>// Una tercera entidad que necesita relacionarse con la *interacción* entre A y B.<br>```entity``` ENTIDAD_EXTERNA {<br>id_entidad_a_relacionar ```key```<br>atributos_de_entidad_a_relacionar<br>}  <br><br>// Ahora, la entidad asociativa, al ser una entidad, puede participar en otras relaciones.<br>relationship relacion_entidad_externa_entidad_asociativa {<br>==ASOC_ENTIDAD_12==[```0..N```] -> ENTIDAD_EXTERNA[```1..1```]<br>} |
|                              | **En el diagrama generado por BigER lo veremos gráficamente como una entidad normal**<br>![](BD%20-%20BigER%20Entidad%20Asociativa%20Crows%20Foot.png)<br>**Cuando lo dibujemos a mano, pondremos un rombo dentro del rectángulo de la entidad asociativa**<br>![](BD%20-%20BigER%20Entidad%20Asociativa%20Rombo%20Crows%20Foot.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

Ahora ya podemos modelar correctamente el ejemplo:

| BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `entity` PUESTO_DE_TRABAJO {<br>id_puesto `key`<br>nombre_puesto<br>}<br><br>`entity` COMPETENCIA {<br>id_competencia `key`<br>nombre_competencia<br>}<br><br>// Entidad asociativa que "cosifica" la relación M:N<br>// Su clave primaria es la combinación de id_puesto e id_competencia<br>//**No es soportada por BigER, lo que haremos es comenzar siempre el nombre de este tipo de entidades con ASOC_ y pondremos los dos atributos claves de las entidades que asocia como claves de la entidad asociativa**<br>`entity` ==ASOC_REQUISITO_DE_PUESTO== {<br>==id_puesto key==<br>==id_competencia key==<br>}<br><br>// Relaciones que forman la entidad asociativa<br>`relationship` requiere {<br>PUESTO_DE_TRABAJO[`1..1`] -> ==ASOC_REQUISITO_DE_PUESTO==[`1..N`]<br>}<br><br>```relationship``` es_requerida_por {<br>COMPETENCIA[`1..1`] -> ==ASOC_REQUISITO_DE_PUESTO==[`1..N`]<br>}<br><br>// La entidad asociativa ahora puede relacionarse con otras entidades<br><br>`entity` VALIDADOR {<br>id_validador `key`<br>rol_empresa<br>}<br><br>```relationship``` validada_por {<br>==ASOC_REQUISITO_DE_PUESTO==[`0..N`] -> VALIDADOR[`1..1`]<br>} |
| **En el diagrama generado por BigER lo veremos gráficamente como una entidad normal**<br><br> ![](BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Crows%20Foot.png)<br><br>**Cuando lo dibujemos a mano, pondremos un rombo dentro del rectángulo de la entidad asociativa**<br><br>![](BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Rombo%20Crows%20Foot.png)<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
**El Modelo Final y su Significado:** El diagrama final representa la realidad de forma lógica y correcta:

Este modelo nos permite leer dos hechos de negocio distintos y conectados:

1. **Hecho 1**: Se define un `REQUISITO_DE_PUESTO` (un puesto de trabajo necesita una competencia).
    
2. **Hecho 2**: Ese `REQUISITO` específico es certificado por un `VALIDADOR`.

#### Nota Importante: La Ausencia de Repetición en el Tiempo

Es crucial entender que este patrón funciona porque asumimos que la relación M:N **no se repite**. Un puesto requiere una competencia una sola vez. Si la relación pudiera repetirse (por ejemplo, si se revisaran las competencias de los puestos cada año y quisiéramos guardar el histórico), la entidad `REQUISITO_DE_PUESTO` necesitaría un atributo adicional en su clave (`Año`) para distinguir las repeticiones. En ese momento, estaríamos en el caso de **modelar una relación M:N con repetición de relaciones entre instancias**, donde en este caso aplicaríamos lo que hemos visto sobre entidades y relaciones débiles que hemos visto anteriormente.

---
### 5.6 Versión final correcta del Diagrama ER de Empresa

Con los nuevos conocimientos, ya podemos hacer el diagrama ER de Empresa completamente correcto.

![BD Empresa versión final - BigER](BD%20Empresa%20versión%20final%20-%20BigER.md)


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

- `PROVEEDOR` (ej: "Tornillos Acme")
    
- `PROYECTO` (ej: "Construcción Puente A")
    
- `REPUESTO` (ej: "Tornillo M8")
    

Y necesitamos registrar la `Cantidad` de repuestos que un proveedor suministra a un proyecto.

**La Prueba del Atributo:** ¿Dónde ponemos el atributo `Cantidad`?

1. ¿En una relación `PROVEEDOR`-`REPUESTO`? No. "Tornillos Acme" no suministra la misma cantidad de "Tornillo M8" a todos sus proyectos.
    
2. ¿En una relación `PROYECTO`-`REPUESTO`? No. El "Puente A" no recibe todos sus "Tornillos M8" del mismo proveedor.
    
3. ¿En una relación `PROVEEDOR`-`PROYECTO`? No. "Tornillos Acme" suministra muchos tipos de repuestos diferentes al "Puente A".
    

La `Cantidad` **solo tiene sentido** cuando conocemos a los tres participantes a la vez. Describe el hecho indivisible:

> "El `PROVEEDOR` 'Tornillos Acme' suministró una `Cantidad` de 5000 'Tornillos M8' al `PROYECTO` 'Puente A'."

Este es un evento ternario. Si intentáramos "descomponer" esto en tres relaciones binarias, perderíamos el hecho central. Registraríamos que el proveedor vende esa pieza, que el proyecto la usa y que el proveedor trabaja en el proyecto, pero **no podríamos saber** si ese proveedor suministró _esa pieza_ a _ese proyecto_.

>[!tip] De la Idea al Diagrama - Modelando con BigER y Crow's Foot
> | Concepto          | BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Relación ternaria | ```entity``` ENTIDAD1 {<br>id1 ```key```<br>}<br>```entity``` ENTIDAD2 {<br>id2 ```key```<br>}<br>```entity``` ENTIDAD3 {<br>id3 ```key```<br>}<br>// simplemente añadimos una nueva flecha y la tercera entidad a relacionar junto con su cardinalidad mínima y máxima con respecto al otro par de entidades<br>```relationship``` relacion_ternaria {<br>ENTIDAD1[```1..N```] ==->== ENTIDAD2[```1..N```] ==->== ENTIDAD3[```1..N```]<br>atributos_relacion_ternaria<br>} |
|                   | ![](BD%20-%20BigER%20Ternaria%20Crows%20Foot.png)                                                                                                                                                                                                                                                                                                                                                                                                                                    |

Nuestro ejemplo quedaría modelado así:

| BigER / Crow's foot                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ```entity``` PROVEEDOR {<br>id_proveedor_ ```key```<br>}<br>```entity``` PROYECTO {<br>id_proyecto ```key```<br>}<br>```entity``` REPUESTO {<br>id_repuesto ```key```<br>}<br>// simplemente añadimos una nueva flecha y la tercera entidad a relacionar junto con su cardinalidad mínima y máxima con respecto al otro par de entidades<br>```relationship``` suministra {<br>PROVEEDOR[```1..N```] ==->== PROYECTO[```1..N```] ==->== REPUESTO[```1..N```]<br>cantidad<br>} |
| ![](BD%20-%20BigER%20Ternaria%20Proveedor%20Proyecto%20Suministro%20Crows%20Foot.png)                                                                                                                                                                                                                                                                                                                                                                               |

---
### 6.3 Caso de Uso Incorrecto: El "Falso Ternario"

Este es el error más común. Ocurre cuando un escenario _parece_ ternario, pero en realidad se puede descomponer en hechos binarios independientes.
#### Contraejemplo: La Relación `IMPARTE`

Imagina que necesitamos modelar el horario de clases. Tenemos tres entidades:

- `PROFESOR`
    
- `ASIGNATURA`
    
- `AULA`
    

El "hecho" que queremos registrar parece un único evento: "La `Profesora Sanz` imparte `Bases de Datos` en el `Aula 101`."

**La Prueba de la Descomposición:** ¿Podemos separar este "hecho" en reglas de negocio más pequeñas que sigan teniendo sentido por sí solas?

1. Hecho 1: ¿PROFESOR imparte ASIGNATURA?
    
    ¿Es "La Profesora Sanz es la responsable de Bases de Datos" un hecho válido por sí mismo, incluso si aún no sabemos el aula? Sí. Es la asignación docente.
    
2. Hecho 2: ¿ASIGNATURA se da en un AULA?
    
    ¿Es "La asignatura Bases de Datos se imparte en el Aula 101" un hecho válido? Sí. Es la asignación de recursos, independientemente de qué profesor la imparta ese año.
    

Como podemos separar el "hecho" principal en dos reglas de negocio independientes, **no debemos usar una relación ternaria**.

La Solución Correcta (y más flexible):

Lo correcto es modelar esto como dos relaciones binarias. La forma más correcta de hacerlo es convertir la relación PROFESOR-ASIGNATURA en una entidad asociativa (que podemos llamar ASOC_DOCENCIA) y luego conectar esta nueva entidad con AULA.

Este modelo es superior porque:

- **Es preciso**: Refleja las dos reglas de negocio separadas.
    
- **Es flexible**: Permite que un `PROFESOR` esté asignado a una `ASIGNATURA` (Hecho 1) incluso antes de que se le asigne un `AULA` (Hecho 2).
    
- **Maneja excepciones**: ¿Qué pasa si la `ASIGNATURA` es "Prácticas Externas" y no tiene `AULA`? Este modelo lo permite; el ternario no.
    

---
### 6.4 La Gran Lección: ¿Ternaria o Binaria?

Para decidir, hazte esta pregunta:

**¿El "hecho" que estoy modelando es un evento único e indivisible, o se puede descomponer en reglas de negocio más pequeñas que sigan teniendo sentido por sí solas?**

- Si es **indivisible** (como `SUMINISTRA`, donde la `Cantidad` une a los tres), usa una **relación ternaria**.
    
- Si es **descomponible** (como `IMPARTE`, que se divide en "asignación docente" y "asignación de aula"), usa **relaciones binarias**, conectándolas a través de una entidad asociativa cuando sea necesario.

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

- **Ejemplo**: En la entidad `EMPLEADO`, podemos identificar subgrupos como:
	- `ADMINISTRATIVO`, `INGENIERO`, `TÉCNICO` (basados en el puesto)
	- `GERENTE` (basado en el rol) 
	- `TIEMPO_COMPLETO`, `TIEMPO_PARCIAL` (basados en la jornada).
    
Estos subgrupos se llaman **subclases** (o subtipos), y la entidad general de la que forman parte se llama **superclase** .

La relación entre una superclase y sus subclases se conoce como **"ES UN"** (IS-A):

- Un `ADMINISTRATIVO` **ES UN** `EMPLEADO`.
    
- Un `GERENTE` **ES UN** `EMPLEADO`.
    

**Principios Fundamentales**:

1. **Misma Entidad, Rol Específico**: Una instancia en una subclase (un ingeniero concreto) es la **misma instancia** del mundo real que pertenece a la superclase (ese mismo ingeniero es también un empleado).
    
2. **Existencia Dependiente**: Una instancia no puede existir _solo_ como miembro de una subclase; **debe** pertenecer también a la superclase.
    
3. **Pertenencia Opcional (por defecto)**: Una instancia de la superclase puede pertenecer a ninguna, una, o varias subclases, dependiendo de las reglas específicas que definamos Por ejemplo, un empleado que es gerente e ingeniero pertenecería a ambas subclases (`GERENTE` e `INGENIERO`) además de a `TIEMPO_COMPLETO` si esa es su jornada.
    

---

### 7.3 Herencia: Recibiendo Propiedades

Una de las grandes ventajas del modelo EER es la **herencia** 🧬. Una entidad que pertenece a una subclase **hereda automáticamente**:

1. **Todos los atributos** de su superclase (incluyendo la clave primaria). Por ejemplo, un `INGENIERO` hereda atributos como `dni`, `nombre`, `direccion`, etc., de `EMPLEADO` .
    
2. **Todas las relaciones** en las que participa su superclase. Si `EMPLEADO` se relaciona con `DEPARTAMENTO` a través de `TRABAJA_PARA`, entonces un `INGENIERO` también `TRABAJA_PARA` un `DEPARTAMENTO`.
    
#### **¡Criterio Fundamental de Diseño!** 💡

Precisamente por la herencia, **solo debemos crear una subclase si esta aporta algo nuevo y significativo** que no se aplica a toda la superclase. Es decir, una subclase se justifica **únicamente** si cumple al menos una de estas condiciones:

- **Tiene Atributos Específicos (o Locales)**: Atributos que solo tienen sentido para ese subgrupo.
    
    - _Ejemplo_: `categoria` para `ADMINISTRATIVO`, `tipoIng` para `INGENIERO`. Estos atributos no aplican a todos los `EMPLEADOS`.
        
- **Participa en Relaciones Específicas**: Relaciones en las que solo participa la subclase, no la superclase general.
    
    - _Ejemplo_: La relación `afiliadoA` con `SINDICATO` solo aplica a los `EMPLEADOS` que son `TIEMPO_PARCIAL`, no a todos.
        

**¿Por qué es tan importante esta regla?** Si creamos subclases que no tienen atributos ni relaciones propias, simplemente estamos añadiendo complejidad al diagrama sin ganar ninguna capacidad descriptiva. La información que distingue a esas subclases (como el `TipoTrabajo`) ya está representada como un atributo en la superclase. Crear subclases vacías es redundante y dificulta la lectura del modelo.

**En resumen**: No crees una subclase si no va a tener "contenido propio" (atributos o relaciones específicas).

---
### 7.4 Restricciones: Las Reglas de la Jerarquía

Podemos (y debemos) definir reglas precisas sobre cómo funcionan nuestras especializaciones. Hay dos tipos de restricciones:
#### A) Restricción de Completitud (¿Obligatorias o No?)

Controla si todos los miembros de la superclase deben pertenecer a alguna subclase.

- **Total (t)** : **Toda** entidad de la superclase **debe** pertenecer a **al menos una** de las subclases. No hay "huecos". Las generalizaciones suelen ser totales.
    
    - _Ejemplo_: `{TIEMPOCOMPLETO, TIEMPOPARCIAL}` es total. Todo empleado tiene una de esas dos jornadas.
        
- **Parcial (p)** : Una entidad de la superclase **puede no pertenecer a ninguna** de las subclases.
    
    - _Ejemplo_: `{ADMINISTRATIVO, TÉCNICO, INGENIERO}` es parcial. Puede haber empleados que no sean ninguno de los tres (como un `GERENTE`).
        
#### B) Restricción de Disyunción (¿Exclusivas o No?)

Controla si las subclases pueden compartir miembros.

- **Disjunta (d)** : Las subclases son **mutuamente excluyentes**. Una entidad de la superclase puede pertenecer, como máximo, a **una** de las subclases.
    
    - _Ejemplo_: `{ADMINISTRATIVO, TÉCNICO, INGENIERO}` es disjunta. No puedes ser dos a la vez.
        
- **Solapada (s)** : Las subclases **pueden compartir miembros**. Una entidad puede pertenecer a **más de una** subclase simultáneamente.
    
    - _Ejemplo_: Si tuviéramos una especialización `{INVESTIGADOR, DOCENTE}`, podría ser solapada, ya que un empleado podría ser ambas cosas.
        

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
| Especialización / Generalización | ```entity``` ==SUPERCLASE== {<br>==id_superclase== ```key```<br>==atributos_superclase==<br>}<br><br>//SUBCLASE1 se crea porque tiene atributos propios distintos a los de la superclase<br><br>//indicar con un comentario delante de la subclase si es ==total== o ==parcial== y si es ==solapada== o ==disjunta==<br>```entity``` ==SUBCLASE1 extends SUPERCLASE== {<br>==id_superclase== ```key```<br>==atributos_subclase==<br>}<br><br>//SUBCLASE2 se crea porque participa en una relacion distinta a la de la superclase<br><br>//indicar con un comentario delante de la subclase si es ==total== o ==parcial== y si es ==solapada== o ==disjunta==<br>```entity``` ==SUBCLASE2 extends SUPERCLASE== {<br>==id_superclase== ```key```<br>}<br><br>//otra entidad distinta para relacionar con la subclase2<br>```entity``` OTRA_ENTIDAD {<br>id_otra_entidad ```key```<br>}<br><br>//relacion entre subclase2 y otra entidad<br>```relationship``` relacion {<br>==SUBCLASE2==[```1..N```] -> OTRA_ENTIDAD[```1..N```]<br>} | **En el diagrama generado por BigER lo veremos gráficamente como unas flechas blancas de las subclases a la superclase**<br>![](BD%20-%20BigER%20Superclase%20y%20subclase%20Crows%20Foot.png)<br>**Cuando lo dibujemos a mano, pondremos una línea blanca perpendicular las flechas que pertenecen a la misma especialización/generalización y las letras t (total) o p (parcial) y s (solapada) o d (disjunta) según corresponda**<br>![](BD%20-%20BigER%20Superclase%20y%20subclase%20Crows%20Foot%20modificado.png) |

---

### 7.5 Especialización y Generalización: Creando las Jerarquías

Estos son los dos procesos para _identificar_ y _crear_ las relaciones superclase/subclase.

#### A) Especialización (Top-Down) 🔽

Es el proceso de partir de una entidad general (superclase) e identificar subgrupos con características distintivas, creando las subclases. Es un refinamiento **de arriba hacia abajo**.

- **Ejemplo**: Empezamos con `EMPLEADO` y definimos las subclases `{ADMINISTRATIVO, TÉCNICO, INGENIERO}` basándonos en el tipo de puesto de trabajo. Luego, identificamos el rol `GERENTE` y creamos esa subclase. Podemos tener múltiples especializaciones de la misma superclase, como `{TIEMPOCOMPLETO, TIEMPOPARCIAL}` basada en la jornada.

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Crow's foot                                                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| //superclase Empleado<br>`entity` ==Empleado== {<br>==dni== `key`<br>nombre<br>fecha_nacimiento<br>direccion<br>telefono<br>}<br><br>//==Especializacion Tipo Puesto== <br><br>//==parcial,disjunta==<br>```entity``` ==Administrativo extends Empleado== {<br>==dni== ```key```<br>categoria<br>}<br><br>//==parcial,disjunta==<br>```entity``` ==Tecnico extends Empleado =={<br>==dni== ```key```<br>nivel<br>}<br><br>//==parcial,disjunta==<br>```entity``` ==Ingeniero extends Empleado== {<br>==dni== ```key```<br>tipoIng<br>}<br><br>//==Especializacion Relacion dirige==<br><br>//==parcial==<br>```entity``` ==Gerente extends Empleado== {<br>==dni== key<br>}<br><br>```entity``` Proyecto {<br>cod_proyecto ```key```<br>}<br><br>```relationship``` dirige{<br>==Gerente==[```1..1```] -> Proyecto[```1..N```]<br>}<br><br>//==Especializacion Tipo Jornada==<br><br>//==total,disjunta==<br>```entity``` ==TiempoCompleto extends Empleado=={<br>==dni== ```key```<br>salario<br>}<br><br>//==total,disjunta==<br>```entity``` ==TiempoParcial extends Empleado=={<br>==dni== ```key```<br>horas_semanales<br>}<br><br>```relationship``` afiliadoA{<br>==TiempoParcial==[```1..N```] -> Sindicato[```1..1```]<br>}<br><br>```entity``` Sindicato {<br>cod_sindicato ```key```<br>} | ![](BD%20-%20BigER%20Jerarquia%20Empleado%20Crows%20Foot.png) |

#### B) Generalización (Bottom-Up)🔼

Es el proceso inverso. Empezamos con varias entidades distintas, notamos que comparten atributos y relaciones comunes, y creamos una superclase general para agrupar esas características comunes. Es una síntesis **de abajo hacia arriba**.

- **Ejemplo**: Modelamos `COCHE` con los atributos (`id_coche`, `matricula`, `precio`, `max_velocidad` y `numero_pasajeros`) y `CAMIÓN` por separado con los atributos (`id_camion`, `matricula`, `precio`, `tonelaje` y `numero_ejes`). Al ver que comparten un id numérico, `Matricula` y `Precio`, creamos la superclase `VEHÍCULO` con un id llamado `id_vehiculo ` y los atributos comunes. `COCHE` y `CAMIÓN` se convierten en subclases con solo sus atributos específicos (`max_velocidad`y `numero_pasajeros` para `COCHE`, `tonelaje` y `numero_ejes` para `CAMIÓN`).
    

| BigER                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Crow's foot                                                    |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| //superclase VEHICULO<br>```entity``` ==VEHICULO== {<br>==id_vehiculo== ```key```<br>matricula<br>precio<br>}<br><br>//==Especializacion Tipo Vehículo, total, disjunta==<br>```entity``` ==COCHE extends VEHICULO== {<br>==id_vehiculo== ```key```<br>max_velocidad<br>numero_pasajeros<br>}<br><br>//==Especializacion Tipo Vehículo, total, disjunta==<br>```entity``` ==CAMION extends VEHICULO== {<br>==id_vehiculo== ```key```<br>tonelaje<br>numero_ejes<br>} | ![300](BD%20-%20Big%20ER%20Jerarquia%20Vehiculo.png) |

En la práctica, ambos procesos se suelen usar combinados. El resultado final, ya sea por especialización o generalización, es una **jerarquía** o **red** de clases.

---

### 7.6 Tipos de Especialización: ¿Reglas Automáticas o Manuales?

¿Cómo determina el sistema a qué subclase pertenece una entidad de la superclase? Hay dos formas:

#### A) Especialización Definida por Atributo ✅

Existe un atributo en la superclase cuyo valor **determina automáticamente** la pertenencia a una subclase. Estas subclases se llaman **subclases de predicado definido**.

- **Ejemplo**: La especialización `{ADMINISTRATIVO, TÉCNICO, INGENIERO}` de `EMPLEADO` está definida por el atributo `TipoTrabajo`. Si `TipoTrabajo = 'Ingeniero'`, la entidad pertenece a la subclase `INGENIERO`.
    
- Cuando todas las subclases de una especialización usan el mismo atributo definitorio, se llama **especialización definida por atributo**, y el atributo es el **atributo definitorio**.

#### B) Especialización Definida por el Usuario 👤

No existe un atributo que determine la pertenencia. Es el **usuario** quien **especifica manualmente** a qué subclase(s) pertenece una entidad cuando la añade o modifica.

- **Ejemplo**: La especialización `GERENTE` de `EMPLEADO` podría ser definida por el usuario, indicando si un empleado específico desempeña ese rol o no.
    
---
### 7.7 Estructuras: Jerarquías vs. Redes (Entramados)

Las relaciones superclase/subclase pueden organizarse de dos maneras:

#### A) Jerarquía de Especialización (Árbol 🌳)

- Una subclase tiene **una única superclase directa** (herencia simple).
    
- Forma una estructura de árbol.
    
- Una subclase hereda de su padre, abuelo, etc., siguiendo la rama hacia arriba.
    
- **Ejemplo**: La estructura de la Universidad que muestra en la siguiente figura es una jerarquía.

![](BD%20-%20BigER%20Jerarquia%20Universidad.png)

>[!exercise] Ejercicio
> Prueba a modelar en  [BigER]( https://marketplace.visualstudio.com/items?itemName=BIGModelingTools.erdiagram) la jerarquía de la Universidad de la figura anterior.
#### B) Red o Entramado de Especialización (Malla 🕸️)

- Una subclase puede tener **varias superclases directas** (herencia múltiple).
    
- La subclase con múltiples padres se llama **subclase compartida**.
    
- **Ejemplo**: Imagina que en nuestro ejemplo de `EMPLEADO`, quisiéramos crear la subclase compartida `INGENIERO_JEFE` que herede de `INGENIERO`, `GERENTE` y `TIEMPOCOMPLETO'.
    
- Aunque EER lo permite, muchos sistemas pueden tener limitaciones con la herencia múltiple. BigER no lo soporta
    

>[!question] Pregunta
>	Explica el concepto de herencia en el modelo EER. ¿Qué heredan las subclases?


>[!question] Pregunta
>	¿Cuándo modelaremos subclases?

---
## 8. El Arte del Buen Diseño - Principios Fundamentales y Errores a Evitar

![El Arte del Buen Diseño - Principios Fundamentales y Errores a Evitar](El%20Arte%20del%20Buen%20Diseño%20-%20Principios%20Fundamentales%20y%20Errores%20a%20Evitar.md)

---

## Anexo I. Resumen BigER

![AL09.2 - Notación BigER](AL09.2%20-%20Notación%20BigER.md)
