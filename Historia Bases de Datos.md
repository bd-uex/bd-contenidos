# Historia de la Tecnología de Bases de Datos

Este vídeo de 5 minutos del [Computer History Museum](https://computerhistory.org/) proporciona una revisión rápida (en inglés) de la historia de las bases de datos desde 1960 a 1990.
![History of Databases](https://youtu.be/KG-mqHoXOXY?si=9FO22pMOblmx4LX5)
(parcialmente generada por Gemini 2.5 Pro)
## 1. Era Pre-Bases de Datos (1950s - principios 1960s)

Antes de los sistemas de bases de datos propiamente dichos, las organizaciones utilizaban **sistemas de archivos**, que proporcionan: almacenamiento de datos durante largos periodos de tiempo y permiten el almacenamiento de grandes cantidades de datos. Sin embargo, los sistemas de archivos **no** suelen **garantizar la imposibilidad de perder datos** si no se realiza una copia de seguridad de estos, **ni permiten un acceso eficiente** a elementos de datos cuya ubicación en un archivo específico se desconoce.

Además, los sistemas de archivos **no admiten un lenguaje de consulta** de alto nivel para los datos de los archivos. A nivel de definición de esquema de datos, se limita a la creación de estructuras de directorios para los archivos. Si bien permiten el acceso simultáneo a los archivos por parte de varios usuarios o procesos, un sistema de archivos generalmente **no evitará situaciones como que dos usuarios modifiquen el mismo archivo al mismo tiempo**, por lo que los cambios realizados por uno de los usuarios no aparecerán en el archivo.

## 2. Primeras Bases de Datos (1960s-1970s)

Los primeros sistemas comerciales de gestión de bases de datos aparecieron a finales de la década de 1960.
### Modelo Jerárquico

- **[IMS](https://www.ibm.com/products/ims) (Information Management System) de IBM** se lanzó en 1966 para el programa Apollo de la NASA
- Organiza los datos en estructuras de árbol con relaciones padre-hijo uno-a-muchos
- Aunque considerado obsoleto en muchos contextos, IMS sigue siendo utilizado extensivamente en sistemas bancarios y de aerolíneas, procesando billones de transacciones anuales
- Limitación principal: dificultad para representar relaciones muchos-a-muchos. Para más información se recomienda revisar [el problema de la rigidez](Problema%20Modelo%20Jerárquico.md) de estos modelos.

![IMS](https://www.ibmmainframer.com/static/images/imsdb/ims-database.jpg)
### Modelo de Red (o grafo)

- **CODASYL DBTG** (Conference on Data Systems Languages - Data Base Task Group) propuso el estándar en 1971
- Productos notables: IDMS (Integrated Database Management System) de Cullinet
- Permitía relaciones más complejas que el modelo jerárquico mediante punteros
- Desventaja: Las bases de datos CODASYL eran complejas y requerían de una capacitación y un esfuerzo importantes para producir aplicaciones útiles. Para más información se recomienda revisar [el problema de la complejidad](Problema%20Modelo%20Red.md) de estos modelos.

![CODASYL](https://upload.wikimedia.org/wikipedia/commons/d/d6/CodasylB.png)

## 3. Era del Modelo Relacional (1970-presente)

### Fundamentos Teóricos (1970-1979)

- **Edgar F. Codd** publicó "A Relational Model of Data for Large Shared Data Banks" en 1970 mientras trabajaba en IBM
	- Introdujo conceptos fundamentales: tablas, tuplas, claves primarias y foráneas, normalización
	- Al contrario que en los sistemas anteriores, el programador no necesitaba conocer la estructura de almacenamiento. 
	- Las consultas se podían expresar en lenguajes de alto nivel, incrementando enormemente la eficiencia de los programadores de bases de datos.
- **System R** de IBM (1974-1977) fue el primer prototipo de SGBD relacional
- **Ingres** en UC Berkeley (1974-1977) desarrollado por Michael Stonebraker y Eugene Wong
- Desarrollo de **SQL** (Structured Query Language) como SEQUEL en System R

### Comercialización (1980s)

- **Oracle** (originalmente Relational Software Inc.) lanzó el primer SGBD relacional comercial en 1979
- **IBM DB2** apareció en 1983
- **Informix** (1981) y **Sybase** (1984) entraron al mercado
- **dBase** popularizó las bases de datos en computadoras personales
- Estándar SQL-86 (SQL-87) establecido por ANSI

### Maduración y Dominancia (1990s)

- **Microsoft SQL Server** (1989) originalmente basado en código de Sybase
- **PostgreSQL** (1996) evolucionó de Ingres como proyecto open source
- **MySQL** (1995) democratizó el acceso a bases de datos relacionales
- Estándares SQL-92 y SQL:1999 añadieron características avanzadas

## 4. Bases de Datos Orientadas a Objetos (1985-2000)

### OODBMS Puros

- Productos como **ObjectStore**, **Versant**, **GemStone** y **O2** surgieron entre 1986-1991
- Diseñados para aplicaciones CAD/CAM, CASE, y sistemas multimedia
- Prometían eliminar el "impedance mismatch" entre lenguajes OO y bases de datos
- Adopción limitada debido a falta de estándares, herramientas maduras y la inercia del modelo relacional

### 5. Bases de Datos Objeto-Relacionales (ORDBMS)

- **Postgres** (precursor de PostgreSQL) pionero en características objeto-relacionales (1986)
- Oracle 8 (1997) incorporó tipos de datos definidos por usuario y herencia
- SQL:1999 formalizó extensiones orientadas a objetos
- Representan un compromiso exitoso entre los modelos relacional y orientado a objetos

## 6. Era de Internet y Datos Distribuidos (1995-2010)

### Bases de Datos y la Web

- Aparición de aplicaciones web dinámicas con CGI, PHP (1995), ASP (1996)
- **XML** (1998) como estándar para intercambio de datos semi-estructurados
- Bases de datos XML nativas: **BaseX**, **eXist-db**, **MarkLogic**
- **XQuery** (2007) como lenguaje de consulta para XML
- Frameworks ORM (Object-Relational Mapping) como **Hibernate** (2001) simplificaron el desarrollo

### Almacenes de Datos y OLAP

- Concepto de **Data Warehouse** popularizado por Bill Inmon y Ralph Kimball en los 90s
- Herramientas ETL (Extract, Transform, Load) para integración de datos
- Bases de datos columnares como **Vertica** y **Sybase IQ** para análisis

## 7. Era del Big Data y NoSQL (2000-presente)

### Movimiento NoSQL (2009 como término, 2000s como concepto)

- **Bigtable** de Google (2006) y **Dynamo** de Amazon (2007) como inspiración
- **Bases de datos de documentos**: MongoDB (2009), CouchDB (2005)
- **Bases de datos clave-valor**: Redis (2009), Memcached (1998 como cache)
- **Bases de datos de grafos**: Neo4j (2007), Amazon Neptune
- **Bases de datos de familias de columnas**: Cassandra (2008), HBase (2008)
- Teorema CAP (Consistency, Availability, Partition tolerance) guiando decisiones de diseño

### Procesamiento Distribuido

- **MapReduce** de Google (2004) y **Hadoop** (2006) para procesamiento masivo
- **Apache Spark** (2014) para procesamiento en memoria
- Arquitecturas Lambda y Kappa para procesamiento de streams

## 8. Tendencias Actuales y Futuras (2015-presente)

### NewSQL y Bases de Datos Híbridas

- **Google Spanner** (2012/2017 público), **CockroachDB** (2015): consistencia global distribuida
- **Amazon Aurora** (2014): MySQL/PostgreSQL compatible con arquitectura cloud-native
- Convergencia de capacidades transaccionales y analíticas (HTAP - Hybrid Transactional/Analytical Processing)

### Cloud y Database-as-a-Service

- **Amazon RDS** (2009), **Azure SQL Database** (2010)
- Arquitecturas serverless: **Amazon DynamoDB** (2012), **Azure Cosmos DB** (2017)
- Separación de cómputo y almacenamiento para elasticidad

### Inteligencia Artificial y Machine Learning

- **Bases de datos vectoriales**: Pinecone, Weaviate, Qdrant para embeddings y búsqueda semántica
- Integración de ML directamente en bases de datos (in-database ML)
- Optimización de consultas mediante IA

### Blockchain y Bases de Datos Descentralizadas

- Bases de datos inmutables y verificables
- **BigchainDB**, **Amazon QLDB** (Quantum Ledger Database)

### Multi-modelo 

- Bases de datos que soportan múltiples modelos de datos simultáneamente
- **ArangoDB**, **OrientDB**, **Azure Cosmos DB**
- Reconocimiento de que diferentes tipos de datos requieren diferentes modelos de almacenamiento

## 9. Reflexión Final

La evolución de las bases de datos refleja las cambiantes necesidades del procesamiento de información: desde la estructura rígida pero eficiente de los modelos jerárquicos, pasando por la flexibilidad y rigor matemático del modelo relacional, hasta la diversidad actual de soluciones especializadas para big data, tiempo real, y aplicaciones distribuidas globalmente. El futuro apunta hacia sistemas cada vez más inteligentes, autónomos y adaptables a las necesidades específicas de cada aplicación.