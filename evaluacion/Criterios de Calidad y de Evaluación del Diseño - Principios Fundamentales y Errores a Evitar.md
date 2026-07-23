Dominar el modelado Entidad-Relación es como aprender a jugar al ajedrez ♟️: conocer las piezas (entidades, relaciones) es solo el principio. Para crear un diseño **sólido, correcto y eficiente**, necesitas aplicar **principios estratégicos**. Esta sección presenta los **criterios de diseño fundamentales** que debes seguir. Dominarlos te convertirá en un buen diseñador. Ignorarlos o aplicarlos incorrectamente no solo resultará en un modelo deficiente, sino que también acarrea **penalizaciones** en la evaluación de tus diseños. Hemos organizado estos criterios por su importancia: desde los principios esenciales que son la base de un buen modelo, hasta los detalles que marcan la diferencia entre un diseño aceptable y uno excelente.

---
### 1 Principios Fundamentales (Errores Graves y Penalizaciones) 🚨

Estos principios son los cimientos **no negociables** de un diseño Entidad-Relación correcto. Representan la comprensión básica del modelo y su aplicación a los requisitos. Un error en cualquiera de estas áreas se considera **grave (G)**, invalida una parte sustancial del modelo y demuestra una falta de comprensión fundamental. Por lo tanto, su dominio es absolutamente esencial. Violar estos principios resultará en **penalizaciones significativas** en cualquier evaluación del diseño, pudiendo comprometer gravemente la calificación final.

#### 1.1 Requisito Imprescindible de Diseño
##### Principio 1: El Modelo Debe Reflejar la Realidad (G1)

El objetivo final de un diagrama E/R es crear un mapa fiel del "minimundo" que se nos describe. Si el diseño final es globalmente incoherente o no representa los requisitos básicos, se considera un error fundamental, independientemente de los fallos más pequeños.  

#### 1.2 Sobre relaciones
##### Principio 2: Las Conexiones Conceptuales son Relaciones, NO Atributos de Implementación (G2)

Este es un error conceptual **crítico y grave (G2)** que surge de confundir los niveles de abstracción del diseño de bases de datos. En el **modelo conceptual E/R**, estamos describiendo la _semántica_ del minimundo, las conexiones lógicas entre conceptos. Por tanto, **jamás** debes usar un atributo dentro de una entidad para referenciar o "apuntar" a otra entidad. Las conexiones lógicas entre entidades **siempre** deben modelarse explícitamente como **relaciones**.

Es **fundamental** entender que, aunque **más tarde**, en el **modelo relacional** (el diseño de las tablas), una relación 1:N pueda traducirse en una clave externa (un atributo en una tabla que referencia a otra), **eso es una decisión de implementación, NO de diseño conceptual**. Incluir claves externas como atributos en el diagrama E/R:

1. **Viola el nivel de abstracción**: El modelo conceptual debe ser independiente de la implementación final.
    
2. **Oculta el significado**: Una relación explícita (ej: `trabaja_para`) tiene una semántica clara. Un atributo `id_departamento` es solo un dato, no expresa la naturaleza de la conexión.
    
3. **Anticipa decisiones**: El mapeo de E/R a relacional tiene reglas específicas. Poner claves externas en el E/R es saltarse el proceso y puede llevar a errores.
    
- ❌ **Incorrecto y Penalizable (Fallo G2)**: Crear una entidad `Asignatura` y añadir un atributo `Profesor_Responsable` (o `dni`) pensando en la futura clave externa. Esto es un grave error conceptual.
    
- ✅ **Correcto**: Crear las entidades `Profesor` y `Asignatura` y conectarlas con una relación explícita como `es_responsable_de`. La _traducción_ de esta relación a una clave externa se hará en la fase de diseño lógico, no aquí.

#### 1.3 Sobre jerarquías
##### Principio 3: Las Jerarquías Representan "ES UN" y Respetan la Identidad (G3, G4, G5) ⚠️

Las jerarquías (especialización/generalización) son una herramienta poderosa, pero su uso está estrictamente reservado para modelar relaciones semánticas de tipo **"ES UN"**. Además, no deben entrar en conflicto con otros mecanismos de identificación, como la dependencia de las entidades débiles. Violar estas reglas se considera un **error conceptual grave** que demuestra una incomprensión fundamental del modelo EER y conlleva **penalizaciones significativas**.

1. **El Subtipo DEBE Ser un Tipo del Supertipo (G3)**: La regla "ES UN" debe cumplirse estrictamente. Crear una jerarquía donde el subtipo no es una versión especializada del supertipo rompe la lógica fundamental del modelo.
    
    - ❌ **Incorrecto y Penalizable (Fallo G3)**: Modelar `Motor` como subtipo de `Vehiculo`. Un motor **no "ES UN"** vehículo; es una _parte de_ él (relación de composición). Usar una jerarquía aquí es un error semántico grave.
        
    - ✅ **Correcto**: `COCHE` **"ES UN"** `Vehiculo`. Esta sí es una jerarquía válida.
        
2. **Usa la Notación Correcta para Jerarquías (G4)**: Si existe una relación "ES UN", **debes** usar la notación específica de jerarquía y no una relación binaria normal.
    
    - ❌ **Incorrecto y Penalizable (Fallo G4)**: Si `Coche` **"ES UN"** `Vehiculo`, representarlo con dos entidades y una relación entre ellas llamada "ES". Esto ignora la herramienta de modelado diseñada específicamente para esta semántica.
        
    - ✅ **Correcto**: Usar la notación de especialización/generalización para conectar `COCHE` (subtipo) extiende (`extends`) `VEHÍCULO` (supertipo).
        
3. **Un Subtipo NO Puede Ser una Entidad Débil de Otra Entidad (G5)**: Una entidad **no puede ser a la vez una subclase y una entidad débil** dependiente de una tercera entidad distinta. La razón es un **conflicto irresoluble de identificación**:

	- Una **subclase** (`Ingeniero`) **hereda** su identidad (su clave primaria) directamente de su **supertipo** (`Empleado`). Su identidad _es_ la del supertipo.
    
	- Una **entidad débil** (`Familiar`) **construye** su identidad combinando la clave de su **propietario** (`Empleado`) con su propia **clave parcial** (`nombre`). Su identidad _depende_ del propietario.
    
	- Si una entidad `E` fuera subclase de `F` _y_ entidad débil de `G`, ¿cómo se identifica `E`? ¿Hereda la clave de `F` o construye su clave a partir de `G`? Son dos mecanismos incompatibles. El modelo EER prohíbe esta ambigüedad.
    
	- ❌ **Incorrecto y Penalizable (Fallo G5)**: Imagina `Trabajado_Temporal` como subclase de `Empleado`. Ahora imagina que `Trabajador_Temporal` es también una entidad débil que depende de `Empresa_ETT` (Empresa de Trabajo Temporal) para identificarse (quizás porque su id solo es único dentro de la ETT). Esta estructura es inválida. Un `Trabajador_Temporal` debe heredar su dni de `Empleado` y no puede tener una identidad dependiente de `Empresa_ETT` al mismo tiempo.

---
### 2 Principios de Diseño Importantes (Basado en Errores Medios)

Estos principios cubren aspectos cruciales del modelado E/R y EER. Ignorarlos, aunque quizás no invalide _todo_ el modelo como los errores graves, demuestra una **falta de comprensión significativa** en áreas clave. Cometer estos errores se considera **medio (M)** y resultará en **penalizaciones medias** en la evaluación, reflejando deficiencias importantes en el diseño.

#### 2.1 Principios Sobre Entidades Fuertes (no débiles)

Estos principios se centran en la correcta identificación y definición de las entidades **fuertes** (aquellas con identidad propia) en tu modelo. 
##### Principio 4: Toda Entidad Debe Describir Algo Relevante (M1)

Una entidad representa un concepto del minimundo sobre el que **necesitamos guardar información**. Como mínimo absoluto, debe poseer los atributos que la identifican (su clave primaria) y, generalmente, otros atributos descriptivos. Dejar una entidad sin ningún atributo más allá de su clave (o incluso sin clave) **(Fallo M1)** es un error medio. Indica que, o bien el concepto no era lo suficientemente importante como para ser una entidad, o el análisis de sus propiedades ha sido incompleto. Una entidad "vacía" no aporta valor.

- ❌ **Incorrecto (M1)**: Crear una entidad `Color` sin atributos, solo para conectarla a `Producto`. Si solo necesitas el nombre del color, debería ser un atributo (`color`) en `Producto`. Si necesitas guardar más datos sobre el color (ej: `codigo_pantone`, `es_metalizado`), entonces sí justifica ser una entidad con atributos.
    
- ✅ **Correcto**: Asegurarse de que cada entidad (`Cliente`, `Producto`, `Pedido`) tenga, además de su clave, los atributos necesarios para describirla según los requisitos (`nombre`, `direccion`, `precio`, `stock`, `fecha`, etc.).

##### Principio 5: El Modelo Debe Ser Preciso y Conciso en Entidades (M2, M3)

El diagrama E/R debe ser un reflejo **exacto** y **completo** de las entidades descritas o implícitas en los requisitos del minimundo, ni más ni menos.

- **No inventes entidades (M2)**: Crear entidades que **no** se derivan de los requisitos **(Fallo M2)** es un error medio. Añade complejidad innecesaria y demuestra que no te has ceñido a la descripción del problema. Por ejemplo, si los requisitos hablan de `Empleados` y `Departamentos`, no inventes una entidad `Planta_Edificio` si no se menciona ni se necesita para guardar información relevante.
    
- **No omitas entidades (M3)**: Olvidar incluir entidades que son **claramente necesarias** según los requisitos **(Fallo M3)** es también un error medio. Hace que el modelo sea incompleto y no pueda representar toda la información requerida. Si los requisitos describen `Clientes` que realizan `Pedidos` de `Productos`, omitir cualquiera de estas tres entidades invalida el modelo.
    
**En resumen**: Basa tu modelo estrictamente en los requisitos. Incluye todas las entidades necesarias y solo las necesarias.

#### 2.2 Principios sobre Entidades Débiles (Errores Medios) ⚖️

Las entidades débiles son una herramienta clave para modelar dependencias, pero su correcta definición requiere precisión. Los errores en su identificación o en la especificación de su relación con la entidad fuerte indican una comprensión incompleta de este concepto fundamental.

##### Principio 6: Define Correctamente la Clave Parcial (M4) 🔑

La **clave parcial** (o discriminante) es el conjunto de atributos _dentro_ de la entidad débil que la distingue de otras entidades débiles que dependen **del mismo propietario**. Es una parte esencial de su identidad. Indicar incorrectamente la clave parcial (por ejemplo, omitiéndola o eligiendo atributos que no discriminan). Demuestra que no se comprende cómo se construye la identidad única de una entidad débil (salvo casos específicos como L15).

- ✅ **Correcto**: En `Familiar` (débil de `Empleado`), la clave parcial es `nombre`, asumiendo que un empleado no tiene dos familiares directos con el mismo nombre exacto. La clave completa heredada sería `{dni, nombre}`.
    
- ❌ **Incorrecto (M4)**: No indicar como clave parcial `nombre` en `Familiar`. El `dni` no es parte de la clave _parcial_; es parte de la clave _heredada_ de la entidad fuerte de la que depende.
    
##### Principio 7: La Dependencia de la Entidad Débil Hacia su Propietario es Total y Única (M5) 🔗

Por definición, una entidad débil tiene **dependencia de existencia e identificación** con su entidad fuerte propietaria. Esto significa que una entidad débil **no puede existir sin su propietario** y siempre está asociada a **exactamente uno**. Esta regla semántica se traduce en una cardinalidad **fija e inmutable** en la relación de identificación: vista _desde_ la entidad fuerte _hacia_ la débil, la participación es siempre **(1,1)** (uno y solo uno). Indicar cualquier otra cardinalidad (como (0,1), (0,N) o (1,N)) en el lado de la entidad fuerte es un error conceptual medio, porque contradice la naturaleza misma de la dependencia que define a la entidad débil.

- ✅ **Correcto**: En la relación `tiene` entre `Empleado` (fuerte) y `Familiar` (débil), la línea que conecta `tiene` con `Empleado` debe indicar (1,1) (o dos barras paralelas en Crow's Foot).
    
- ❌ **Incorrecto (M5)**: Indicar (0,1) en el lado de `Empleado`. Esto implicaría que un `Familiar` podría existir sin un `Empleado` asociado, lo cual es imposible por definición.
    
##### Principio 8: Define Correctamente Cuántos Dependientes Puede Tener un Propietario (M6) 👨‍👩‍👧‍👦

La relación de identificación, vista desde la entidad débil hacia la fuerte, indica cuántas instancias débiles puede tener asociadas **una** instancia de la entidad fuerte. Para que la estructura se ajuste al patrón estándar de entidad débil, la entidad fuerte **debe ser capaz de relacionarse con múltiples instancias** de la entidad débil. Por lo tanto, la cardinalidad **máxima** en el lado de la entidad débil **debe ser 'N'** (muchos).

Esto significa que las cardinalidades correctas y esperadas en el lado de la entidad débil son:
- **(0,N)**: Un propietario puede tener cero o muchos dependientes (ej: un `Empleado` puede estar relacionada con cero o varias instancias de  `Familiar`).
    
- **(1,N)**: Un propietario debe tener al menos un dependiente y puede tener muchos (ej: una `Factura` (fuerte) debe tener al menos una `Linea_Factura` (débil)).

Especificar una cardinalidad **máxima de '1'** (es decir, (0,1) o (1,1)) en el lado de la entidad débil se considera un **error medio**. Esto contradice la expectativa de la relación de dependencia (donde la clave parcial sirve para distinguir entre _varios_ dependientes del mismo propietario) e indica una posible **incomprensión del concepto** o un **error en el modelado**. Si un propietario solo puede tener, como máximo, un dependiente, raramente se justifica modelarlo como una entidad débil separada; a menudo podría integrarse como atributos en la entidad fuerte o modelarse de otra forma.

- ✅ **Correcto**: Si un `Empleado` puede estar relacionado con cero o varias instancias de  `Familiar`, indicar (0,N) o (1,N) (según si es obligatorio tener al menos uno) en la línea que conecta la relación con `Familiar`.
    
- ❌ **Incorrecto y Penalizable (M6)**: Si los requisitos _permitieran_ varios familiares, pero indicas (0,1) o (1,1) en el lado de `Familiar`. Esto restringe incorrectamente el modelo. Incluso si los requisitos _dijeran_ que un empleado solo puede tener _un_ familiar, modelarlo como entidad débil con cardinalidad máxima '1' viola el espíritu del criterio M6 y sugiere que quizás `Familiar` no debería ser una entidad débil en primer lugar.

#### 2.3 Decisiones de Diseño Importantes: Principios sobre Relaciones (Errores Medios) ⚖️

##### Principio 9: Modela Todas las Conexiones Requeridas (M7) 🔗

El modelo E/R debe capturar **todas** las interacciones y asociaciones significativas entre las entidades que se describen en los requisitos. Si el enunciado indica explícitamente que dos conceptos (representados como entidades) están relacionados de alguna manera, esa conexión **debe** modelarse como una **relación** explícita en el diagrama. Omitir una relación que es claramente requerida por la descripción del minimundo **(Fallo M7)** es un **error medio**. Indica que el modelo está incompleto, ya que no representa una parte fundamental de la estructura lógica de los datos descrita. La única excepción leve (L4) es cuando una relación _actual_ puede inferirse lógicamente a partir de una entidad histórica ya modelada.

- ✅ **Correcto**: Si los requisitos dicen "Un `Empleado` trabaja en `Proyecto`", debes incluir una relación `trabaja_en` (o similar) conectando las entidades `Empleado` y `Proyecto`.
    
- ❌ **Incorrecto y Penalizable (M7)**: Si los requisitos describen la relación anterior, pero en tu diagrama las entidades `Empleado` y `Proyecto` aparecen desconectadas. El modelo no refleja cómo interactúan estas entidades, lo cual es una omisión significativa.

#### 2.4 Decisiones de Diseño Importantes: Principios sobre Jerarquías (Errores Medios) ⚖️

Las jerarquías (especialización/generalización) son una herramienta poderosa del modelo EER, pero su correcta implementación requiere respetar los principios de herencia de identidad y evitar la redundancia. 

##### Principio 10: La Identidad es Única y Heredada en Toda la Jerarquía (M8) 🆔

En una jerarquía EER, la **identidad** (la clave primaria) se define **una sola vez** en la entidad **supertipo** (la raíz de la jerarquía o subjerarquía). Todos los **subtipos** que heredan de ella **deben usar exactamente la misma clave primaria**. La esencia de la relación "ES UN" es que un subtipo _es_ una versión especializada del supertipo; representan la misma entidad del mundo real y, por lo tanto, comparten la misma identidad fundamental. Definir claves primarias diferentes para los subtipos **(Fallo M8)** es un **error medio** que viola este principio básico de herencia.

- ✅ **Correcto**: Si `Vehiculo` tiene como clave primaria `id_vehiculo`, entonces sus subtipos `Coche` y `Camion` **también** deben tener `id_vehiculo` como su clave primaria (heredada).
    
- ❌ **Incorrecto y Penalizable (M8)**: Si `Vehiculo` usa `id_vehiculo` como clave, pero defines `matricula` como clave primaria de `Coche` y `numero_bastidor` como clave primaria de `Camion`. Esto rompe la conexión de identidad fundamental de la jerarquía.
    
#### Principio 11: Evita la Redundancia: Los Atributos Comunes Van Arriba (M9) ⬆️

Uno de los propósitos principales de crear una jerarquía es **factorizar las características comunes** en el nivel más general posible (el supertipo) para evitar repetirlas. Los atributos (que **no** sean clave) que son compartidos por varios o todos los subtipos **deben** definirse **una única vez** en el supertipo. Gracias a la herencia, estos atributos estarán disponibles automáticamente para todas las entidades subtipo. Repetir atributos comunes en los subtipos **(Fallo M9)** es un **error medio**. Crea redundancia innecesaria, aumenta el riesgo de inconsistencias (tener el mismo dato con valores diferentes en distintos sitios) y dificulta el mantenimiento del modelo.

- ✅ **Correcto**: Definir `nombre`, `fecha_nacimiento`, `direccion` una sola vez en la entidad `Empleado`. Los subtipos como `Ingeniero`, `Administrativo`, `Tecnico` heredarán estos atributos.
    
- ❌ **Incorrecto y Penalizable (M9)**: Definir `nombre` y `fecha_nacimiento` en `Empleado`, y luego volver a definir `nombre` y `fecha_nacimiento` dentro de `Ingeniero`, `Administrativo`, etc. Esta repetición es incorrecta y viola el principio de herencia eficiente.
    
---
### 3 Refinando el Detalle: Principios de Pulido del Diseño (Errores Leves) ✨

Estos principios finales se centran en la **precisión y completitud** de los detalles del modelo E/R y EER. Su correcta aplicación distingue un diseño funcional de uno **pulido y profesional**. Aunque no invalidan la estructura fundamental del modelo, ignorar estos detalles demuestra una falta de atención y rigor. Estos descuidos se consideran **fallos leves (L)** y, aunque conllevan **penalizaciones menores**, reflejan un dominio incompleto de las convenciones y mejores prácticas del modelado.

#### 3.1 Principios sobre Entidades Fuertes (No débiles) ✨

Estos principios se centran en la correcta y completa especificación de las entidades fuertes. Su aplicación demuestra un dominio completo del modelo E/R.

##### Principio 12: Señala Claramente la Clave Primaria (L1) 🔑

Toda entidad fuerte **debe** tener una clave primaria que la identifique de forma única. Es **esencial** indicar cuál es este atributo (o conjunto de atributos) utilizando la notación estándar (`key` en BigER, subrayado en notación gráfica). No marcar explícitamente la clave primaria **(Fallo L1)** es un error leve. Aunque a veces se pueda deducir, omitir esta marca dificulta la lectura y comprensión inmediata del identificador principal de la entidad.

- ✅ **Correcto**: En la entidad `Empleado`, subrayar el atributo `dni`.
    
- ❌ **Incorrecto (L1)**: Definir `dni` como un atributo más, sin subrayarlo ni marcarlo como clave.
    
##### Principio 13: Identifica Todas las Claves Candidatas (Alternativas) (L2) 🔑🔑

A veces, una entidad puede tener **más de un** atributo o conjunto de atributos que podrían servir como clave primaria (es decir, son únicos). Estos se denominan **claves candidatas**. Se debe elegir una como clave primaria, pero las **otras claves candidatas (claves alternativas)** también deben indicarse (`//UNIQUE` en BigER, subrayado discontinuo en notación gráfica). No indicar las claves alternativas **(Fallo L2)** es un error leve. Muestra que no se ha realizado un análisis completo de todos los identificadores únicos posibles para la entidad.

- ✅ **Correcto**: En la entidad `Coche`, si tenemos tanto `vin` (Número Identificación Vehículo) como `matricula`, elegir una como clave primaria y el otro como alternativa.
    
- ❌ **Incorrecto (L2)**: indicar solo `vin` como clave primaria y dejar `matricula` como un atributo normal, a pesar de que también es única según los requisitos.

#### 3.2 Principios sobre Relaciones  ✨

Estos principios aseguran que las relaciones en tu modelo sean precisas, completas y no redundantes. Su correcta aplicación demuestra un entendimiento detallado de cómo representar las interacciones entre entidades.

##### Principio 14: Define las Cardinalidades Exactamente Como se Requieren (L3) 🎯

Las restricciones de cardinalidad (mínima y máxima) son cruciales porque definen las **reglas de negocio** que gobiernan cómo se relacionan las entidades. Debes leer atentamente los requisitos y asegurarte de que las cardinalidades que indicas en el diagrama (ej: (0,N), (1,1), (1,N)) reflejan **exactamente** lo que se pide. Indicar una cardinalidad distinta a la especificada explícitamente en el enunciado **(Fallo L3)** es un error leve. Aunque no rompe la estructura general, demuestra una falta de precisión al traducir las reglas de negocio al modelo (salvo errores de cardinalidad graves ya cubiertos).

- ✅ **Correcto**: Si el enunciado dice "un empleado debe pertenecer a _un_ departamento, y un departamento puede tener _muchos_ empleados (o ninguno si es nuevo)", las cardinalidades serían (1,1) en el lado del Departamento y (0,N) en el lado del Empleado para la relación `trabaja_para`.
    
- ❌ **Incorrecto (L3)**: Poner (0,1) en el lado del `Departamento`, permitiendo que un `Empleado` no tenga `Departamento`, si los requisitos lo exigían.
    
##### Principio 15: Decide Cómo Modelar el Estado Actual vs. el Histórico (L4) ⏳

Cuando una relación entre entidades cambia a lo largo del tiempo (ej: un empleado cambia de puesto, un vehículo cambia de propietario), a menudo modelamos este **historial** usando una entidad débil (ej: `Asignacion_Historica`). Esto nos lleva a una decisión de diseño crucial: ¿cómo representamos el **estado actual** de esa relación?

Existen dos enfoques principales, y la elección tiene implicaciones importantes:

1. **Enfoque 1: Inferir el Estado Actual del Histórico**
    
    - **Cómo**: Se modela **únicamente** la entidad histórica. El estado actual se deduce buscando el registro más reciente o aquel que no tiene una fecha de fin (ej: `fecha_fin IS NULL`).
        
    - **Ventajas**: Modelo conceptual más simple, evita redundancia y el riesgo de inconsistencias entre el "estado actual" y el "último estado histórico".
        
    - **Desventajas**: Consultar el estado actual puede ser **menos eficiente**, especialmente con grandes volúmenes de datos históricos (requiere buscar y filtrar en la tabla histórica).
        
    - **Relación con L4**: El criterio L4 indica que **omitir** una relación explícita para el estado actual cuando este se puede inferir del histórico **no** es un error grave ni medio. Se considerará, un **fallo leve (L4)**, si por los requisitos del diseño en concreto son mayores las desventajas que las ventajas (por ejemplo, si la empresa explícitamente dice que tiene 50.000 empleados, esta opción no es la mejor posible para saber el departamento al que está asignado actualmente un empleado).
        
2. **Enfoque 2: Modelar Explícitamente el Estado Actual**
    
    - **Cómo**: Se modelan **ambas cosas**: la entidad histórica _y_ una **relación binaria separada** que representa únicamente el estado actual (ej: una relación `tiene_asignado_actualmente` (1:1 o 1:N) entre `Empleado` y `Puesto`).
        
    - **Ventajas**: Consultar el estado actual es **muy eficiente** (acceso directo a través de la relación). Puede reflejar más claramente la importancia del estado presente en el negocio.
        
    - **Desventajas**: Modelo conceptual más complejo. Introduce **redundancia controlada** y exige mecanismos (a nivel de aplicación o base de datos, como triggers) para **garantizar la consistencia** entre la relación actual y los registros históricos. Si no se gestiona bien, pueden surgir inconsistencias (ej: que la relación actual apunte a un puesto distinto al último registro del histórico).
        

**Principio de Diseño (Guía basada en L4 y buenas prácticas):**

- **Claridad vs. Eficiencia**: Debes sopesar la simplicidad conceptual y la garantía de consistencia (Enfoque 1) frente a la eficiencia en las consultas del estado actual (Enfoque 2).
      
- **Recomendación Práctica**: En muchos sistemas con grandes históricos, especialmente si las consultas sobre el estado actual son muy frecuentes (como saber el departamento actual de 50,000 empleados), el **Enfoque 2 (modelar ambos)** suele ser preferible por rendimiento, _siempre y cuando_ se implementen los mecanismos necesarios para mantener la coherencia. Si optas por el Enfoque 1, debes ser consciente de las posibles implicaciones en el rendimiento de las consultas.
    
**En resumen**: L4 solo se aplica si la decisión final entre inferir o modelar explícitamente la relación actual no se basa en un análisis de los requisitos específicos del contexto del problema, especialmente en términos de volumen de datos y patrones de consulta.

##### Principio 16: Especifica Siempre los Roles en Relaciones Recursivas (L5) 🎭

Cuando una entidad se relaciona consigo misma (una relación recursiva, como `EMPLEADO` supervisa `EMPLEADO`), es **absolutamente esencial** indicar los **roles** que juega cada participante en la relación. Esto clarifica el significado de cada "extremo" de la conexión. No indicar los roles en una relación recursiva **(Fallo L5)** es un error leve que genera ambigüedad.

- ✅ **Correcto**: En la relación `supervisa` entre `Empleado` y `Empleado`, indicar los roles "Supervisor" y "Supervisado" en cada extremo de la línea.
    
- ❌ **Incorrecto (L5)**: Dibujar la relación `supervisa` sin especificar qué extremo representa al que supervisa y cuál al supervisado.
    
##### Principio 17: No Dupliques Información de Relaciones con Atributos (L6) 🚫

Si ya has modelado una relación explícita entre dos entidades (ej: `trabaja_para` entre `Empleado` y `Departamento`), **no debes** incluir además un atributo en una de las entidades para almacenar información que ya está implícita en la relación (ej: añadir un atributo `nombre_departamento` a `Empleado`). Esto es redundante. Incluir un atributo que representa información ya capturada por una relación existente **(Fallo L6)** es un error leve. (Nota: esto es diferente del error grave G2, donde la _propia relación_ se omitía y se intentaba reemplazar por un atributo).

- ✅ **Correcto**: La relación `trabaja_para` conecta `Empleado` (con `dni`, `nombre`) y `Departamento` (con `numero_departamento`, `nombre`).
    
- ❌ **Incorrecto (L6)**: Además de la relación `trabaja_para`, añadir un atributo `nombre_departamento_empleado` dentro de la entidad `Empleado`.
    
##### Principio 18: Incluye Todos los Atributos Propios de una Relación (L7) 📝

Algunas relaciones, especialmente las M:N, tienen atributos que describen la **interacción** en sí misma, no a las entidades participantes. Es crucial identificar e incluir estos atributos asociados directamente a la relación (o a la entidad asociativa que la represente). No incluir los atributos que pertenecen a una relación **(Fallo L7)** es un error leve que hace el modelo incompleto.

- ✅ **Correcto**: En la relación M:N `trabaja_en` entre `Empleado` y `Proyecto`, incluir el atributo `horas` asociado a la relación .
    
- ❌ **Incorrecto (L7)**: Modelar la relación `trabaja_en` sin incluir el atributo `horas`, a pesar de que los requisitos indiquen que se debe registrar.
    
##### Principio 19: Asigna los Atributos al Lugar Correcto (Entidad vs. Relación) (L8)📍

Un atributo debe colocarse en el elemento que describe directamente. Incluir atributos en una relación cuando en realidad describen a una de las entidades participantes **(Fallo L8)** es un error leve de modelado. La regla general es: si el valor del atributo depende _solo_ de una de las entidades, va en esa entidad; si depende de la _combinación_ de las entidades participantes, va en la relación.

- ✅ **Correcto**: `NombreEmpleado` va en `Empleado`. `nombre` va en `Proyecto`. `horas` (trabajadas por un empleado en un proyecto) va en la relación `trabaja_en`.
    
- ❌ **Incorrecto (L8)**: Poner el atributo `salario` del empleado en la relación `trabaja_en`. El salario depende del empleado, no de en qué proyecto trabaje (generalmente).
    
##### Principio 20: Modela Solo Relaciones Necesarias y Significativas (L9) ✨

El diagrama debe ser claro y conciso. Incluir relaciones que no se derivan de los requisitos o que son redundantes (se pueden deducir a través de otras relaciones ya existentes) **(Fallo L9)** es un error leve. Complica innecesariamente el modelo y dificulta su comprensión. Céntrate en representar las asociaciones directas y fundamentales descritas en el minimundo.

- ✅ **Correcto**: Si `Empleado` trabaja en `Departamento`, y `Departamento` se localiza en `Ubicacion`, modelar esas dos relaciones.
    
- ❌ **Incorrecto (L9)**: Además de las dos relaciones anteriores, añadir una tercera relación directa `Empleado` se localiza en `Ubicacion`. Esta relación es deducible de las otras dos y probablemente innecesaria.

#### 3.3 Principios sobre Entidades débiles

Estos principios aseguran que uses el concepto de entidad débil de manera precisa y solo cuando sea verdaderamente necesario, respetando sus condiciones definitorias.

##### Principio 21: Una Entidad Débil DEBE Tener Dependencia de Identificación (L10) 🆔

La característica **definitoria** de una entidad débil es que **no puede identificarse por sí misma** y necesita "tomar prestada" la clave de su entidad fuerte propietaria para formar su propia identidad única. Si modelas una entidad como débil, pero en realidad _sí_ tiene una clave primaria propia o podría identificarse sin depender de la supuesta entidad fuerte, estás usando incorrectamente el concepto **(Fallo L10)**. Es un error leve porque, aunque estructuralmente podrías forzarlo, semánticamente es incorrecto.

- ✅ **Correcto**: `Familiar` es débil porque su `nombre` solo es único _dentro_ del contexto de un `Empleado`. Necesita el `dni`.
    
- ❌ **Incorrecto (L10)**: Modelar `Permiso_Conducir` como débil de `Persona`. Aunque depende de `Persona` para existir, `Permiso_Conducir` tiene su propia clave única (`numero_permiso`) y, por tanto, _no_ tiene dependencia de identificación. Debería ser una entidad fuerte.
    
##### Principio 22: Una Entidad Débil DEBE Tener Dependencia de Existencia (L11) ⏳

Además de la dependencia de identificación, una entidad débil también **debe** tener dependencia de existencia con su entidad fuerte. Esto significa que no puede existir si su propietario no existe. Modelar una entidad como débil si conceptualmente _podría_ existir independientemente de la entidad fuerte **(Fallo L11)** es un error leve. Muestra una inconsistencia entre la dependencia estructural que impones y la lógica del negocio.

- ✅ **Correcto**: Una `Linea_Factura` (débil) no puede existir si no existe la `Factura` (fuerte) a la que pertenece.
    
- ❌ **Incorrecto (L11)**: Imagina modelar `Libro` como débil de `Biblioteca`. Aunque un libro está _en_ una biblioteca, el concepto de `Libro` (con su isbn) existe independientemente de si pertenece a una biblioteca específica o no. Tiene dependencia de existencia con la biblioteca en el contexto de _esa_ base de datos, pero no es una dependencia inherente a su identidad, por lo que no debería ser débil por esta razón.
    
##### Principio 23: Si Depende en Existencia e Identificación, ES Débil (L12) ✅

Este es el reverso de los dos principios anteriores. Si una entidad cumple **ambas** condiciones (dependencia de existencia **y** dependencia de identificación) con respecto a una entidad fuerte, **debes** modelarla como una **entidad débil**. Indicarla como una entidad fuerte **(Fallo L12)** es un error leve. Aunque funcionalmente podría representarse con claves externas, ignora la semántica específica de dependencia que el modelo EER permite (y requiere) capturar explícitamente.

- ✅ **Correcto**: Modelar `Familiar` como entidad débil dependiente de `Empleado`.
    
- ❌ **Incorrecto (L12)**: Modelar `Familiar` como una entidad fuerte con una clave primaria artificial propia (ej: `id_familiar`) y una relación normal con `Empleado`. Esto oculta la dependencia inherente de su identidad.
    
##### Principio 24: Usa Entidades Débiles Históricas **Solo** si Hay Repeticiones Reales (L13, L14) 🔄

Cuando convertimos una relación M:N en una entidad para guardar su **historial** (como `Docencia` entre `Profesor` y `Asignatura` a lo largo de los años), la entidad resultante (`Docencia`) es débil y típicamente incluye un atributo de tiempo (como `año_academico`) en su clave parcial para distinguir las ocurrencias. Sin embargo, este patrón **solo** debe aplicarse si la misma pareja de entidades **puede relacionarse legítimamente varias veces** a lo largo del tiempo según las reglas de negocio.

- **No** convertir en entidad débil histórica una relación M:N donde **SÍ** hay repeticiones y necesitas distinguirlas (normalmente con un atributo de tiempo en la clave parcial) **(Fallo L14)** es un error leve. El modelo sería incapaz de almacenar correctamente el historial.
    
    - ✅ **Correcto (Necesario si hay repetición)**: Crear `Docencia` como débil de `Profesor` y `Asignatura` con clave parcial `año_academico`, si un profesor puede impartir la misma asignatura varios años.
        
- Convertir una relación en entidad débil histórica (añadiendo un componente de tiempo a la clave) cuando **NO** se pueden dar repeticiones **(Fallo L13)** es un error leve. Complica innecesariamente el modelo e introduce una clave primaria no mínima. En estos casos, si la relación M:N necesita ser una entidad (porque tiene atributos propios o participa en otras relaciones), debe ser una **entidad asociativa**, cuya clave se forma _únicamente_ por las claves de las entidades propietarias. Si no se da ninguno de estos casos se quedará como relación M:N
    
    - ❌ **Incorrecto (Fallo L13)**: Modelar `Linea_de_Pedido` como entidad débil de `Pedido` y `Producto` con una clave parcial `fecha_inclusion_en_pedido` (formando la clave `{id_pedido, id_producto, fecha_inclusion_en_pedido}`). Esto es incorrecto porque un producto solo puede estar _una vez_ en un pedido dado. La `fecha_inclusion_en_pedido` no es necesaria para la unicidad; la clave correcta y mínima es simplemente `{id_pedido, id_producto}`. Debería ser una entidad asociativa simple, no una histórica.


#### Principio 25: La Dependencia Histórica Debe Ser Mínima y Correcta (L15) ⚖️

Cuando creas una entidad débil histórica a partir de una relación que se repite, esta debe depender **única y exclusivamente** de las entidades necesarias para identificarla, siguiendo las reglas de los casos que vimos en un instante de tiempo. Hacerla depender de más entidades de las necesarias **(Fallo L15)** es un error leve. Introduce redundancia en la clave primaria y complica el modelo innecesariamente.

- ✅ **Correcto**: En el historial de `Asignacion_Departamento` (sección 5.4.2.3.1 en T05), la entidad histórica depende **solo** de `Empleado` (y la fecha), porque la relación en un instante es 1:N.
    
- ❌ **Incorrecto (L15)**: Hacer que `Asignacion_Departamento` dependa de `Empleado` **y** de `Departamento` (y la fecha). La dependencia de `Departamento` es innecesaria para la identificación única, dado que un empleado solo está en un departamento en una fecha dada.

#### 3.4 Principio sobre Relaciones Débiles ✨

Este principio aseguran que se indican correctamente las relaciones débiles cuando hay entidades débiles.

##### Principio 26: Marca Explícitamente la Relación de Identificación (L16) ♦️♦️

La relación que conecta una entidad débil con su entidad fuerte propietaria no es una relación cualquiera; es la **relación de identificación**. Es a través de esta relación que la entidad débil hereda parte de su clave y establece su dependencia. Para destacar esta naturaleza especial, el modelo EER utiliza una notación específica: un **rombo doble** en la notación gráfica (o la palabra clave `weak relationship` en BigER). No indicar explícitamente que esta relación es débil (identificadora) usando la notación correcta **(Fallo L16)** es un **error leve**. Aunque la estructura general pueda entenderse por el contexto (al ver la entidad débil con rectángulo doble), omitir la marca en la relación reduce la claridad y no sigue la convención estándar para resaltar las dependencias de identificación.

- ✅ **Correcto**: En el diagrama , la relación `familiar_de` entre `Empleado` (fuerte) y `Familiar` (débil) debe modelarse como `weak relationship` en BigER y dibujarse con un rombo doble.
    
- ❌ **Incorrecto (L16)**: Dejar la relación `familiar_de` como una relación normal.


#### 3.5 Principios sobre Jerarquías  ✨

Estos principios aseguran que tus jerarquías (especialización/generalización) sean semánticamente correctas, precisas y eficientes, evitando redundancias o conexiones incorrectas. Su correcta aplicación demuestra un dominio fino del modelo EER.

#### Principio 27: Crea Subtipos Solo si Aportan Valor (L17, L18) ➕➖

Las jerarquías sirven para modelar **diferencias significativas** dentro de un tipo de entidad general.

- **Crea un subtipo solo si es necesario (Evita L17)**: Solo debes definir una subclase si esta tiene **atributos propios** o participa en **relaciones propias** que no se aplican a toda la superclase. Crear un subtipo que no añade ninguna característica o relación nueva **(Fallo L17)** es un error leve. Es redundante, ya que la clasificación probablemente ya se puede representar con un atributo en el supertipo, y complica el diagrama innecesariamente.
    
    - ❌ **Ejemplo Detallado: Vehículos y su Combustible**

		1. **El Escenario Inicial:** Imagina que estás modelando la entidad `Vehiculo`. Defines sus atributos básicos como `matricula` (la clave), `marca`, `modelo`, y un atributo crucial llamado `tipo_combustible`. Este último atributo puede almacenar valores como 'gasolina', 'diesel', o 'eléctrico', clasificando así cada vehículo.
    
		2. **El Error L17 (Crear Subtipos Vacíos):** Basándote en el atributo `tipo_combustible`, decides crear una especialización. Dibujas nuevas entidades (rectángulos) para `Vehiculo_Gasolina`, `Vehiculo_Diesel`, y `Vehiculo_Electrico`, conectándolas a `Vehiculo` como subclases. **Sin embargo**, no añades ningún atributo _nuevo_ ni ninguna relación _nueva_ específica a estas subclases. Visualmente, tendrías las cajas de las subclases conectadas a la superclase, pero sin contenido propio (más allá de heredar los atributos de `Vehiculo`).
    
		3. **¿Por Qué es un Error (Leve)?**
    
		    - **Redundancia:** La información clave que distingue a estos "tipos" de vehículos (`tipo_combustible`) ya está perfectamente capturada y disponible en el atributo de la superclase `Vehiculo`. Las subclases vacías no añaden ninguna capacidad descriptiva adicional.
        
		    - **Complejidad Innecesaria:** El diagrama se vuelve visualmente más cargado (más cajas y líneas) sin que esto represente una mayor riqueza semántica o informativa. Complica la lectura y el mantenimiento del modelo sin justificación.
        
		4. **La Solución Correcta (Si no hay atributos/relaciones específicas):** Simplemente **no crear las subclases**. Mantener el atributo `tipo_combustible` dentro de la entidad `Vehiculo` es la forma correcta y suficiente de clasificar las instancias. El modelo es más limpio, más conciso y sigue conteniendo toda la información necesaria.
    
	**En resumen:** Solo justifica la creación de una subclase si esta va a tener "contenido propio" (atributos específicos o relaciones específicas) que la diferencie funcionalmente del resto de la superclase. Si la única diferencia es el valor de un atributo que ya existe en el supertipo, la jerarquía es innecesaria y su creación constituye el error L17.
        
- **No omitas subtipos necesarios (Evita L18)**: A la inversa, si un subconjunto claro de entidades de un supertipo _sí_ tiene atributos o relaciones específicas significativas, **debes** modelarlo como un subtipo. No hacerlo **(Fallo L18)** es un error leve que resulta en un modelo menos preciso y puede obligar a usar valores nulos o lógica compleja más adelante.
    
    - ✅ **Correcto (Necesario si hay diferencias)**: Crear las subclases `Ingeniero` (con atributo `tipo_ingeniero`) y `Administrativo` (con atributo `categoria`), ya que tienen propiedades distintas.
        

#### Principio 28: Conecta las Relaciones al Nivel Correcto de la Jerarquía (L19, L20) 🎯

Cuando una entidad externa se relaciona con una entidad que forma parte de una jerarquía, es crucial conectar la relación al nivel **semánticamente correcto**:

- **Conecta al subtipo si es específica (Evita L19)**: Si la relación solo aplica a un **subconjunto** específico (un subtipo), debe conectarse directamente a ese **subtipo**. Conectarla erróneamente al supertipo **(Fallo L19)** es un error leve que generaliza incorrectamente la relación.
    
    - ✅ **Correcto**: La relación `afiliado_a_` (con `Sindicato`) se conecta al subtipo `Empleado_Tiempo_Parcial`, si solo ellos pueden afiliarse.
        
    - ❌ **Incorrecto (L19)**: Conectar `afiliado_a` al supertipo `Empleado` si la regla solo aplica a los de tiempo parcial.
    -
- **Conecta al supertipo si aplica a todos (Evita L20)**: Si la relación se aplica a **todas** las instancias de la jerarquía (independientemente del subtipo), debe conectarse al **supertipo**. Conectarla erróneamente a un subtipo **(Fallo L20)** es un error leve que restringe incorrectamente el alcance de la relación.
    
    - ✅ **Correcto**: La relación `trabaja_para` (entre `Empleado` y `Departamento`) se conecta a `Empleado` (supertipo), porque _todos_ los empleados, sean ingenieros o administrativos, trabajan para un departamento.
        
    - ❌ **Incorrecto (L20)**: Conectar `trabaja_para` solo al subtipo `Ingeniero`.
        
        
#### Principio 29: Especifica Correctamente las Restricciones de la Jerarquía (L21) 🚦

Las restricciones de **totalidad/parcialidad** y **disyunción/solapamiento** definen las reglas fundamentales de la jerarquía. Debes analizar los requisitos y aplicar la combinación correcta (t,d / t,s / p,d / p,s). Indicar incorrectamente estas restricciones **(Fallo L21)** es un error leve. Demuestra una falta de precisión al capturar las reglas de negocio sobre cómo se clasifican las entidades dentro de la jerarquía.

- ✅ **Correcto**: Marcar la especialización `{Tiempo_Completo, Tiempo_Parcial}` como (t,d) si todo empleado debe tener una y solo una de esas jornadas.
    
- ❌ **Incorrecto (L21)**: Marcarla la especialización como ( t,s / p,d / p,s) si todo empleado debe tener una y solo una de esas jornadas.

---

![Resumen Criterios de Evaluación - Examen Entidad Relación](Resumen%20Criterios%20de%20Evaluación%20-%20Examen%20Entidad%20Relación.md)