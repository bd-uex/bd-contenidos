### Fallos Graves (G) 🚨 (-6 puntos / fallo)

#### a) Requisitos del diseño:

- **G1**: El diseño global está muy lejos de la solución correcta, independientemente de los fallos concretos cometidos.
    

#### b) Relaciones:

- **G2**: **¡Crítico!** Incluir un atributo en una entidad (o relación) para representar lo que en realidad es una relación con otra, _cuando no se ha representado dicha relación_ en el diagrama (no es el caso L6).
    

#### c) Jerarquías:

- **G3**: Definir una jerarquía donde el subtipo **no "ES UN"** tipo del supertipo (relación semántica incorrecta).
    
- **G4**: Usar una relación normal (ej: llamada "ES") cuando lo correcto es usar una jerarquía.
    
- **G5**: Crear un subtipo que a la vez es una entidad débil de _otra_ entidad distinta (conflicto de identidad).
    

---

### Fallos Medios (M) (-2 puntos / fallo)

#### a) Entidades fuertes (no débiles):

- **M1**: Dejar entidades sin ningún atributo que la describa(más allá de la clave o incluso sin ella).
    
- **M2**: Crear entidades que no son necesarias según los requisitos.
    
- **M3**: No incluir entidades que sí son necesarias según los requisitos.
    

#### b) Entidades débiles:

- **M4**: Indicar incorrectamente la clave parcial (discriminante) salvo que se dé el caso **L15**.
    
- **M5**: Indicar una cardinalidad distinta a `(1,1)` en el lado de la **entidad fuerte** en la relación de identificación.
    
- **M6**: Indicar una cardinalidad distinta a `(0,N)` o `(1,N)` en el lado de la **entidad débil** en la relación de identificación.
    

#### c) Relaciones:

- **M7**: No incluir una relación requerida en el enunciado (salvo L4).
    

#### d) Jerarquías:

- **M8**: Indicar claves primarias distintas en entidades de la misma jerarquía.
    
- **M9**: Repetir atributos comunes (no clave) del supertipo en los subtipos.
    

---

### Fallos Leves (L) (-1 punto / fallo)

#### a) Entidades fuertes (no débiles):

- **L1**: No indicar correctamente la clave primaria.
    
- **L2**: No indicar las claves alternativas (candidatas).
    

#### b) Relaciones:

- **L3**: Indicar cardinalidades (mínima o máxima) distintas a las explícitamente requeridas salvo en los casos recogidos como fallo medio (M5 o M6).
    
- **L4**: Omitir la relación "actual" cuando es inferible de un histórico ya modelado porque habrá una fecha fin sin valor para mostrar dicha relación actual (no estrictamente un error, pero puede ser leve si no se basa en un análisis de los requisitos específicos del contexto del problema, especialmente en términos de volumen de datos y patrones de consulta).
    
- **L5**: No indicar los roles en una relación recursiva.
    
- **L6**: Incluir un atributo redundante que representa información ya capturada por una relación existente.
    
- **L7**: No incluir los atributos que pertenecen a una relación.
    
- **L8**: Incluir atributos en una relación que en realidad pertenecen a una entidad participante.
    
- **L9**: Incluir relaciones que no son necesarias o son redundantes.
    

#### c) Entidades débiles:

- **L10**: Indicar como débil una entidad que _no_ tiene dependencia de identificación.
    
- **L11**: Indicar como débil una entidad que _no_ tiene dependencia de existencia.
    
- **L12**: Indicar como fuerte una entidad que _sí_ cumple las condiciones para ser débil (dependencia de existencia e identificación).
    
- **L13**: Convertir en débil histórica una relación que _no_ se repite en el tiempo (debería ser una relación normal o una entidad asociativa).
    
- **L14**: No convertir en débil histórica una relación que _sí_ se repite y necesita distinguirse en el tiempo.
    
- **L15**: Hacer depender una entidad débil histórica de más entidades de las estrictamente necesarias para su identificación.
    

#### d) Relaciones débiles:

- **L16**: No indicar una relación débil entre una entidad débil y la entidad fuerte de la que depende.
    

#### e) Jerarquías:

- **L17**: Hacer un subtipo "vacío" (sin atributos ni relaciones distintas a las del supertipo).
    
- **L18**: No hacer un subtipo cuando sí hay atributos/relaciones específicas para un subconjunto de las instancias del supertipo.
    
- **L19**: Relacionar una entidad externa con un subtipo cuando la relación era con el supertipo.
    
- **L20**: Relacionar una entidad externa con el supertipo cuando la relación era específica de un subtipo.
    
- **L21**: Indicar incorrectamente las restricciones de la jerarquía (total/parcial, disjunta/solapada).