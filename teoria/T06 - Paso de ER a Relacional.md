# Del Concepto a la Realidad - Mapeo E/R a Relacional

---

tags: #database, #lecture, #entity-relationship #relational
author: aeprieto
origin: [“Fundamentos de Sistemas de Bases de Datos”](https://explora.unex.es/discovery/fulldisplay?docid=alma991004714442207611&context=L&vid=34UEX_INST:34UEX&lang=es&search_scope=MyInst_and_CI&adaptor=Local%20Search%20Engine&tab=Everything&query=any,contains,elmasri&sortby=date_d&facet=frbrgroupid,include,9013732198023457128&offset=0). R. Elmasri, R. y S. B. Navathe.  Addison-Wesley, 2008 (5ª edición) + contenido generado por Gemini 2.5 Pro refinado y editado por aeprieto
date: 2025-11-01

---
## Contenidos
1. Introducción: Del Plano Conceptual al Plano Lógico
2. El Algoritmo de Mapeo (Pasos E/R)
	1. Paso 1: Mapear Tipos de Entidades Fuertes (Regulares)
	2. Paso 2: Mapear Tipo de Entidades Débiles
	3. Paso 3: Mapear Relaciones Binarias 1:1 (Cardinalidad Máxima 1 en ambos lados)
	4. Paso 4: Mapear Relaciones Binarias 1:N (Cardinalidad Máxima 1 en un lado y N en el otro)
	5. Paso 5: Mapear Relaciones Binarias M:N (Cardinalidad Máxima N en ambos lados)
	6. Paso 6: Mapear Relaciones Unarias
	7. Paso 7: Mapear Relaciones de grado n>2
	8. Paso 8: Mapear Jerarquías (Especialización/Generalización)
3. Ejemplo EMPRESA Completo
4. Grafo Relacional de EMPRESA

---
## 1 Introducción: Del Plano Conceptual al Plano Lógico

En el tema anterior, hemos aprendido a construir el **Modelo Entidad-Relación (E/R)**. Este es el "plano del arquitecto" ✍️: un diagrama de alto nivel, fácil de entender y perfecto para comunicarnos con los expertos del negocio y validar que hemos capturado correctamente la realidad.

Ahora, debemos traducir ese plano conceptual en un **Modelo Relacional**: el "plano de construcción lógico" 📐. Este modelo no es código SQL, sino un **esquema formal** compuesto por **Relaciones** (el término lógico para las tablas), **Atributos** (columnas), **Claves Primarias (PK)** y **Claves Externas o Foráneas (FK)**.

Este proceso no es mágico ni subjetivo; es un **algoritmo sistemático** basado en un conjunto de reglas.
### 1.1 Objetivos del Mapeo

El objetivo de este algoritmo no es solo traducir, sino hacerlo bien. Nuestros objetivos clave son:

- **Conservar toda la información**: Todos los atributos y relaciones deben tener su lugar en el nuevo esquema.
    
- **Mantener las restricciones**: Debemos preservar las reglas de negocio (como claves y cardinalidades) en la medida de lo posible. 
    
- **Minimizar valores nulos**: Un buen diseño evita tener relaciones con muchas columnas vacías (`NULL`), lo que ahorra espacio y reduce la ambigüedad.

## 2 El Algoritmo de Mapeo (Pasos E/R)

Para ver la aplicabilidad del algoritmo, usaremos el diagrama de la base de datos [EMPRESA](BD%20Empresa%20versión%20final%20-%20BigER.md) como ejemplo a desarrollar cuando corresponda en el paso correspondiente.

---
### Paso 1: Mapear Tipos de Entidad Fuertes (Regulares)

**Regla:** Por cada entidad fuerte `E`, se crea una nueva **Relación** `R`.

- Se incluyen todos los atributos de `E` como atributos de `R`.
    
- Si tuviéramos atributos **compuestos** sobre los que se necesitará operar, se "descomponen", si no se ha hecho ya, en sus atributos simples (ej: `NombreCompleto` → `Nombre`, `Apellido1`, `Apellido2`).
    
- Se <u>subraya</u> la **Clave Primaria** `E` como **Clave Primaria (PK)** de `R`.
    
- Si hubiera **claves alternativas**, las ==resaltaremos==, como vimos en el Tema 2, si estamos usando markdown, y las subrayaremos discontinuas si estamos usando bolígrafo y papel.
    
- A esta relación se podrán añadir más atributos para representar asociaciones 1:N o 1:1 en las que intervenga.

![600](../imgs/BD%20-%20MapeoER01_EntidadRegular.png)

---
#### Aplicación al ejemplo `EMPRESA`
**Relaciones a incluir en el Esquema Relacional:**
- EMPLEADO(<u>dni</u>, nombre, apellido1, apellido2, fechaNac, direccion, sexo, sueldo)
- DEPARTAMENTO(<u>numero_departamento</u>, ==nombre==)
- PROYECTO(<u>numero_proyecto</u>, ==nombre==, ubicacion)

---

### Paso 2: Mapear Tipos de Entidad Débiles

**Regla:** Por cada entidad débil `D` que depende de una propietaria `P`, se crea una nueva **Relación** `R`.

- Se incluyen todos los atributos simples de `D`.
    
- Se añade la **Clave Primaria (PK)** de la propietaria `P` a `D`. Esta será una **Clave Externa (FK)** que referencia a `P`.
	- Si la entidad débil `D` depende de más de una entidad propietaria, se añaden las **Claves Primarias** de cada una de ellas como **Clave Externa (FK)**.
    
- La **Clave Primaria (PK)** de `R` es una clave **compuesta** por:
	- La **Clave Primaria** de la propietaria `P` (la **Clave Externa (FK)** que acabamos de añadir).
		- Si la entidad débil `D` depende de más de una entidad propietaria, cada una de las **Claves Primarias** de cada una de ellas formará parte de la  **Clave Primaria**.
	- La **clave parcial** de `D` de la entidad débil.
        

![600](../imgs/BD%20-%20MapeoER02_EntidadDebil.png)

---
#### Aplicación al ejemplo `EMPRESA`:
**Relaciones a incluir en el Esquema Relacional:**
- FAMILIAR(<u>empleado</u>, <u>nombre</u>, sexo, fechaNac, relacion) 
	- empleado ->  EMPLEADO(dni) [B:C,M:C]
- UBICACION_DPTO(<u>dpto</u>, <u>nombre</u>) 
	- dpto->  DEPARTAMENTO(numero_departamento) [B:C,M:C]
   
---
### Paso 3: Mapear Relaciones Binarias 1:1 (Cardinalidad Máxima 1 en ambos lados)

Suponiendo que:
	- existe entidad `E1` en ER (con clave id_E1 y resto_atributos_E1)
	- existe entidad `E2` en ER (con clave id_E2 y resto_atributos_E2)
	- existe en ER relación binaria `RE1E2` entre `E1[X..1]` ↔ `E2[X..1]` (donde X puede ser 0 o 1) y con atributos de relación (atributos_RE1E2)

La estrategia de mapeo depende **enteramente de las cardinalidades mínimas** (la participación).

---

#### Caso A: Participación Total en un lado y Opcional en el otro

**(Cardinalidades: `E1[1..1]` ↔ `E2[0..1]`)**

Este es el caso más común para una relación 1:1.

- **Regla**: Se usa el ==**Enfoque  de Clave Externa**==. 
	- Se crean las relaciones `E1` y `E2` a partir de las entidades `E1` y `E2
	- Se añade la Clave Primaria de la entidad `E1` que no está obligada a relacionarse con la entidad 'E2' como Clave Externa (FK) a la relación `E2` dado  que sí está obligada a tener esa relación con `E1` para evitar `NULL`.

- **Restricción extra**: Esta nueva Clave Externa (FK) debe tener una restricción `UNIQUE`para garantizar que la cardinalidad máxima sea 1 y no N:
		
- **Atributos de la relación**: Si la relación `RE1E2` tenía atributos, migran junto con la Clave Externa (FK) a `E2`.

- **Esquema final**:
	- E1(<u>id_E1</u>, resto_atributos_E1)
	- E2(<u>id_E2</u>, resto_atributos_E2, id_E1, atributos_RE1E2)
		- id_E1 `NOT NULL`,`UNIQUE`->  E1(id_E1) [B:R,M:C]

![600](../imgs/BD%20-%20MapeoER03_01a11.png)

- **Ejemplo**:
	- ![200](../imgs/BD%20-%20BigER%20Relación%2001%2011%20Crows%20Foot.png)
	- Una `PROVINCIA` debe tener de capital _un_ `MUNICIPIO` (`[1..1]`).
	- Un `MUNICIPIO` puede ser capital o no de _una_ `PROVINCIA` (`[0..1]`).
	- MUNICIPIO(<u>id_municipio</u>, resto_atributos_municipio)
	- PROVINCIA(<u>id_provincia</u>, resto_atributos_provincia, id_municipio}
		- id_municipio `NOT NULL`,`UNIQUE`->  MUNICIPIO(id_municipio) [B:R,M:C]
   
---

#### Caso B: Participación Opcional en ambos lados

**(Cardinalidades: `E1[0..1]` ↔ `E2[0..1]`)**

- **Regla**: Se usa el ==**Enfoque de relación adicional**==. 
	- Se crean las relaciones `E1` y `E2` a partir de `E1` y `E2
	- Si usáramos el ==**Enfoque de Clave Externa**== (Caso A), la Clave Externa FK tendría que admitir `NULL`s, lo cual es menos "limpio" y eficiente. Para evitarlo:
		- se crea la relación `RE1E2` para la relación `RE1E2`.
			- `RE1E2` contiene las PKs de `E1` y `E2` como FKs.
			- Se elige una de las FKs (ej: `id_E1`) como PK de `RE1E2`. La otra (`id_E2`) será Clave Alternativa (`UNIQUE`).

- **Restricción extra**: La FK no elegida como PK en `RE1E2` deben ser `UNIQUE` para garantizar que la cardinalidad máxima sea 1 y no N
    
- **Atributos de la relación**: Si la relación `RE1E2` tenía atributos, estos formarán parte de `RE1E2`.

- **Esquema final**:
	- E1(<u>id_E1</u>, resto_atributos_E1)
	- E2(<u>id_E2</u>, resto_atributos_E2)
	- RE1E2(<u>id_E1</u>, ==id_E2==, atributos_RE1E2)
		- id_E1 ->  E1(id_E1) [B:C,M:C]
		- id_E2 `NOT NULL`,`UNIQUE`->  E2(id_E2) [B:C,M:C]

![600](../imgs/BD%20-%20MapeoER04_01a01.png)

- **Ejemplo**:
	- ![200](../imgs/BD%20-%20BigER%20Relación%2001%2001%20Crows%20Foot.png)
	- Un `TRABAJADOR` puede tener asignada(o no) _una_ `PLAZA_APARCAMIENTO` (`[0..1]`).
	- Una `PLAZA_APARCAMIENTO` puede estar asignada (o no) a _un_ `TRABAJADOR` (`[0..1]`).
	- TRABAJADOR(<u>id_trabajador</u>, resto_atributos_trabajador)
	- PLAZA_APARCAMIENTO(<u>id_plaza</u>, resto_atributos_plaza)
	- TIENE_ASIGNADA(<u>id_trabajador</u>, ==id_plaza==, atributos_relación)
		- id_trabajador ->  TRABAJADOR(id_trabajador) [B:C,M:C]
		- id_plaza `NOT NULL`,`UNIQUE` ->  PLAZA_APARCAMIENTO(id_plaza) [B:C,M:C]

---
#### Caso C: Participación Total en ambos lados

**(Cardinalidades: `E1[1..1]` ↔ `E2[1..1]`)**

No se da muy a menudo. Implica que las dos entidades deben existir siempre juntas y en la misma cantidad.

- **Regla**: Tienes dos opciones válidas:
    
    1. Opción habitual: ==**Enfoque de Clave Externa**==. Similar al Caso A con la diferencia de que se puede elegir entre que la PK de `E1` migre como FK a `E2` o viceversa, que la PK de `E2` migre como FK a `E1` , da igual. Al igual que en caso A la FK debe ser `NOT NULL` y `UNIQUE` para mantener la cardinalidad máxima en 1 y la relación que reciba la FK también recibe los atributos de la relación `RE1E2`. 
    ![600](../imgs/BD%20-%20MapeoER05_11a11.png)
		- Por ejemplo:
		- ![200](../imgs/BD%20-%20BigER%20Relación%2011%2011%20Crows%20Foot.png)
			- Un `AUTOMOVIL` debe tener _un_ `SEGURO` (`[1..1]`).
			- Un `SEGURO` solo pertenece a _un_ `AUTOMOVIL` (`[1..1]`).
			- SEGURO(<u>id_seguro</u>, resto_atributos_seguro)
			- AUTOMOVIL(<u>id_automovil</u>, resto_atributos_automovil, id_seguro}
				- id_seguro `NOT NULL`,`UNIQUE`->  SEGURO(id_seguro) [B:R,M:C]
        
    2. Opción menos habitual: ==**Enfoque de Fusión en una relación**==. Solo es adecuado si ambas entidades conceptualmente son muy cercanas. El resultado es que se crea **una sola relación** que contiene todos los atributos de `E1`, `E2` y de la relación `RE1E2`. Las claves de E1 y E2 serán `NOT NULL`y `UNIQUE` en la nueva relación y se elige una cualquiera de ellas como Clave Primaria de la nueva relación.
		- En resumen:
			- RE1E2(<u>id_E1</u>, ==id_E2==, resto_atributos_E1, resto_atributos_E2, atributos_RE1E2)
				- id_E2 `NOT NULL`,`UNIQUE`
		![600](../imgs/BD%20-%20MapeoER05_11a11%20excepcional.png)
		- Por ejemplo:
		- ![200](../imgs/BD%20-%20BigER%20Relación%2011%2011%20Crows%20Foot.png)
			- Un `AUTOMOVIL` debe tener _un_ `SEGURO` (`[1..1]`).
			- Un `SEGURO` solo pertenece a _un_ `AUTOMOVIL` (`[1..1]`).
			- AUTOMOVIL_CON_SEGURO(<u>id_automovil</u>, ==id_seguro==, resto_atributos_automovil, resto_atributos_seguro}
				- id_seguro `NOT NULL`,`UNIQUE`
        
---
#### Aplicación al ejemplo `EMPRESA` :
**Relaciones a incluir en el Esquema Relacional:**

- La relación en ER `EMPLEADO[1..1]` `DIRIGE` `DEPARTAMENTO [0..1]` es el **Caso A** y se aplica el ==**Enfoque de Clave Externa**==. Como ya teníamos creadas las relaciones para `EMPLEADO`y `DEPARTAMENTO` hay que modificar `DEPARTAMENTO`:
	- DEPARTAMENTO(<u>numero_departamento</u>, ==nombre==, **director**, fechaIngresoDirector)
		- director `NOT NULL`,`UNIQUE`->  EMPLEADO(dni) [B:R,M:C]

---
### Paso 4: Mapear Relaciones Binarias 1:N (Cardinalidad Máxima 1 en un lado y N en el otro)

Suponiendo que:
	- existe entidad `E1` en ER (con clave id_E1 y resto_atributos_E1) que ha pasado a ser relación `E1` (con clave `id_E1`).
	- existe entidad `E2` en ER (con clave id_E2 y resto_atributos_E2) que ha pasado a ser relación `E2` (con clave `id_E2`).
	- existe en ER relación binaria `RE1E2` entre `E1[X..1]` ↔ `E2[X..N]` (donde X puede ser 0 o 1) y con atributos de relación (atributos_RE1E2)

---
#### Caso General: Participación total u opcional con pocos nulos
- **¿Cuándo usar esta alternativa?**
       1. **Participación total u opcionalidad con pocos `NULL`s**: Si la participación de `E2` (lado N) es total o, siendo opcional, se espera que la mayoría de instancias de E2 estén relacionadas con instancias de E1.
       2. **No se espera evolución Futura a M:N**: Si no se prevé que la relación 1:N pueda convertirse en M:N en el futuro.
       3. **Pocos Atributos en la Relación**: Si la relación `RE1E2` tiene pocos o ningún  atributo propio.

- **Regla**: Se aplica el ==**Enfoque de Clave Externa**==.
	- Se crean las relaciones `E1` y `E2` a partir de `E1` y `E2
	- Se añade la PK de `E1` (`id_E1`) a `E2`  como Clave Externa (FK).
	
- **Atributos de la relación**: Si la relación `RE1E2` tenía atributos, migran junto con la Clave Externa (FK) a `E2`.

- **Restricción de nulidad en función de participación mínima**: La decisión sobre si esta FK admite `NULL`s depende de la **cardinalidad mínima** de la entidad `E2`  en la relación. Opciones:
	- **(Cardinalidades: `E1[1..1]` ↔ `E2[X..N]`)**
		- Dado que la participación de `E2` en la relación es **total** (mínimo 1, es obligatorio estar relacionado), el atributo FK (`id_E1`) en `E2` **DEBE ser `NOT NULL`**.
		- **Esquema final**:
			- E1(<u>id_E1</u>, resto_atributos_E1)
			- E2(<u>id_E2</u>, resto_atributos_E2, id_E1, atributos_RE1E2)
				- id_E1 `NOT NULL`->  E1(id_E1) [B:R,M:C]

		![600](../imgs/BD%20-%20MapeoER06_11a0NyER07_11a1N.png)
		
		- **Ejemplo**:
			- ![200](../imgs/BD%20-%20BigER%20Relación%2011%201N%20Crows%20Foot.png)
			- Una `PROVINCIA` puede tener _muchos_ `MUNICIPIO` (`[1..N]`).
			- Un `MUNICIPIO` debe pertenecer a _una_ `PROVINCIA` (`[1..1]`).
			- PROVINCIA(<u>id_provincia</u>,resto_atributos_provincia)
			- MUNICIPIO(<u>id_municipio</u>,resto_atributos_municipio,id_provincia}
				- id_provincia `NOT NULL`->  PROVINCIA(id_provincia) [B:R,M:C]
				
	- **(Cardinalidades: `E1[0..1]` ↔ `E2[X..N]`)**
		- Dado que la participación de `E2` en la relación es **opcional** (mínimo 0, no es obligatorio estar relacionado), el atributo FK (`id_E1`) en `E2` **DEBE admitir `NULL`**.
		- **Esquema final**:
			- E1(<u>id_E1</u>, resto_atributos_E1)
			- E2(<u>id_E2</u>, resto_atributos_E2, id_E1, atributos_RE1E2)
				- id_E1 ->  E1(id_E1) [B:N,M:C]
		![](../imgs/BD%20-%20MapeoER08_01a0NyER09_01a1N_NORMAL.png)
		
		- **Ejemplo**:
			- ![200](../imgs/BD%20-%20BigER%20Relación%2001%200N%20Crows%20Foot.png)
			- Un `SOCIO` puede tener (o no) _muchos_ `EJEMPLAR` en préstamo (`[0..N]`). 
			- Un `EJEMPLAR` puede estar (o no) prestado a _un_ `SOCIO` (`[0..1]`). 
			- Se esperan mayoría de ejemplares prestados
			- Solo se guarda el préstamo actual (no va a ser M:N)
			- Solo se guarda la fecha del prestamo en la relación.
			- SOCIO_BIBLIOTECA(<u>id_socio</u>, resto_atributos_socio)
			- EJEMPLAR_LIBRO(<u>id_ejemplar</u>, resto_atributos_ejemplar,id_socio,atributos_relación)
				- id_socio ->  SOCIO_BIBLIOTECA(id_socio) [B:N,M:C]
---
#### Caso Excepcional: Participación opcional con muchos nulos
- **¿Cuándo usar esta alternativa?**
    1. **Opcionalidad Alta con muchos `NULL`s**: Si la participación de `E2` (lado N) se espera _muy opcional_, es decir, que la mayoría de instancias de E2 no estén relacionadas con instancias de E1 y ,por tanto, la mayoría de las filas en `E2` tendrían `NULL` en la FK.
    2. **Evolución Futura a M:N**: Si se prevé que la relación 1:N pueda convertirse en M:N en el futuro para facilitar la migración.
    3. **Muchos Atributos en la Relación**: Si la relación `RE1E2` tiene muchos atributos propios, para evitar "ensuciar" demasiado la relación `E2`.

- **Regla**: Se aplica el ==**Enfoque de relación adicional**==
	- Se crean `E1` (para `E1`), `E2` (para `E2`) y `RE1E2` para la relación `RE1E2`. 
	- `RE1E2` contendrá:
		- las PKs de `E1` y `E2` como FKs
		- los atributos de la relación.
    
- **Clave Primaria de `RE1E2`**: La PK de `RE1E2` será la FK del lado "N" (`id_E2`). 
	- Además la PK de E1 será `NOT NULL` dado que no habrá instancias de esta relación si no relaciona instancias de ambas entidades

- **Esquema final**:
	- E1(<u>id_E1</u>, resto_atributos_E1)
	- E2(<u>id_E2</u>, resto_atributos_E2)
	- RE1E2(id_E1, <u>id_E2</u>, atributos_RE1E2)
		- id_E1 `NOT NULL`-> E1(id_E1) [B:C,M:C]
		- id_E2 -> E2(id_E2) [B:C,M:C]
	![600](../imgs/BD%20-%20MapeoER08_01a0NyER09_01a1N_EXCEPCIONAL.png)
	- **Ejemplo**:
		- ![200](../imgs/BD%20-%20BigER%20Relación%2001%200N%20Crows%20Foot.png)
		- Un `SOCIO` puede tener (o no) _muchos_ `EJEMPLAR` en préstamo (`[0..N]`). 
		- Un `EJEMPLAR` puede estar (o no) prestado a _un_ `SOCIO` (`[0..1]`).
		- Se esperan muchos ejemplares sin prestar
		- Se espera guardar histórico de préstamos (va a ser M:N)
		- Se guardan varios atributos en la relación.
		- SOCIO_BIBLIOTECA(<u>id_socio</u>, resto_atributos_socio)
		- EJEMPLAR_LIBRO(<u>id_ejemplar</u>, resto_atributos_ejemplar)
		- PRESTAMOS(id_socio,<u>id_ejemplar</u>, atributos_relación)
			- id_socio `NOT NULL` -> SOCIO_BIBLIOTECA(id_socio) [B:C,M:C]
			- id_ejemplar -> EJEMPLAR_LIBRO(id_ejemplar) [B:C,M:C]
---
#### Aplicación al ejemplo `EMPRESA` :
**Relaciones a incluir en el Esquema Relacional:**

- La relación en ER `EMPLEADO[1..N]` `TRABAJA_PARA_` `DEPARTAMENTO [1..1]` es el **Caso General** y se aplica el ==**Enfoque de Clave Externa**==. Como ya teníamos creadas las relaciones para `EMPLEADO`y `DEPARTAMENTO` hay que modificar la relación `EMPLEADO`:
	- EMPLEADO(<u>dni</u>, nombre, apellido1, apellido2, fechaNac, direccion, sexo, sueldo, **dpto**)
		- dpto `NOT NULL` ->  DEPARTAMENTO(numero_departamento) [B:R,M:C]
- La relación en ER `DEPARTAMENTO[1..1]` `CONTROLA` `PROYECTO [1..N]` es el **Caso General** y se aplica el ==**Enfoque de Clave Externa**==. Como ya teníamos creadas las relaciones para `DEPARTAMENTO` y `PROYECTO` hay que modificar la relación `PROYECTO`:
	- PROYECTO(<u>numero_proyecto</u>, ==nombre==, ubicacion, **dpto**)
		- dpto `NOT NULL` ->  DEPARTAMENTO(numero_departamento) [B:R,M:C]
---
### Paso 5: Mapear Relaciones Binarias M:N (Cardinalidad Máxima N en ambos lados)

Suponiendo que:
	- existe entidad `E1` en ER (con clave id_E1 y resto_atributos_E1) que ha pasado a ser relación `E1` (con clave `id_E1`).
	- existe entidad `E2` en ER (con clave id_E2 y resto_atributos_E2) que ha pasado a ser relación `E2` (con clave `id_E2`).
	- existe en ER relación binaria `RE1E2` entre `E1[X..N]` ↔ `E2[X..N]` (donde X puede ser 0 o 1) y con atributos de relación (atributos_RE1E2)

**Regla**: Se aplica el ==**Enfoque de relación adicional**==
	- Se crea `RE1E2` para la relación `RE1E2` que se suele denominar **relación de referencia cruzada**. 
	- `RE1E2` contendrá:
		-  La **Clave Primaria** de `E1` (`id_E1`) como **Clave Externa (FK)**.
		-  La **Clave Primaria** de `E2` (`id_E2`) como **Clave Externa (FK)**.
		- Cualquier atributo propio de la relación `RE1E2`.
    
- **Clave Primaria de `RE1E2`**: es la clave compuesta formada por la combinación de las claves externas (id_E1, id_E2).

- **Esquema final**:
	- E1(<u>id_E1</u>, resto_atributos_E1)
	- E2(<u>id_E2</u>, resto_atributos_E2)
	- RE1E2(<u>id_E1</u>, <u>id_E2</u>, atributos_RE1E2)
		- id_E1 -> E1(id_E1) [B:C,M:C]
		- id_E2 -> E2(id_E2) [B:C,M:C]
	![600](../imgs/BD%20-%20MapeoER10_XMaXN.png)

	- **Ejemplo**:
		- ![200](../imgs/BD%20-%20BigER%20Relación%200N%200N%20Crows%20Foot.png)
		- Un `TURISTA` puede visitar (o no) _muchos_ `MONUMENTO` (`[0..N]`). 
		- Un `MONUMENTO` puede ser visitado (o no) por _muchos_ `TURISTA` (`[0..N]`).
		- TURISTA(id_turista, resto_atributos_turista)
		- MONUMENTO(id_monumento, resto_atributos_monumento)
		- VISITAS(<u>id_turista</u>,<u>id_monumento</u>, atributos_relación)
			- id_turista -> TURISTA(id_turista) [B:C,M:C]
			- id_monumento -> MONUMENTO(id_monumento) [B:C,M:C]
---
#### Aplicación al ejemplo `EMPRESA` :
**Relaciones a incluir en el Esquema Relacional:**

- La relación en ER `EMPLEADO[1..N]` `TRABAJA_EN_` `PROYECTO [0..N]`, como ya teníamos creadas las relaciones para `EMPLEADO` y `DEPARTAMENTO`, solo hay que crear la nueva relación  `TRABAJA_EN_`:
	- TRABAJA_EN(<u>empleado</u>, <u>proyecto</u>, horas)
		- empleado ->  EMPLEADO(dni) [B:C,M:C]
		- proyecto ->  PROYECTO(numero_proyecto) [B:C,M:C]

---
#### Paso 5b: Mapear Entidades Asociativas (Relaciones M:N "Puente")

Este paso se aplica cuando hemos modelado una relación M:N como una **entidad asociativa**, con el fin de que esta pueda participar en _otras_ relaciones.

Una entidad asociativa se mapea **inicialmente de forma idéntica a la relación `RE1E2` del Paso 5 (Relaciones M:N)**. La gran diferencia no está en cómo se crea su relación, sino en **cómo se mapean las relaciones en las que participa con otras entidades**.

Usaremos el ejemplo de la sección 5.5 del Tema 5 donde teníamos `PUESTO_DE_TRABAJO` ↔ `ASOC_REQUISITO_DE_PUESTO` ↔ `COMPETENCIA`, donde la entidad asociativa `ASOC_REQUISITO_DE_PUESTO` se relaciona a su vez con `VALIDADOR`.

![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo%20Rombo%20Crows%20Foot.png)

**Regla:** El mapeo se hace en dos etapas:

1. **Mapear la Entidad Asociativa (Igual que Paso 5):**
    
    - Primero, la entidad asociativa (ej: `ASOC_REQUISITO_DE_PUESTO`) se convierte en una **nueva relación**, exactamente como si fuera una relación M:N del Paso 5.
        
    - La PK de esta nueva relación es la **clave compuesta** por las FKs de las entidades que conecta (`id_puesto` + `id_competencia`).
        
    - Los atributos propios de la entidad asociativa (si los tuviera) se añaden como atributos.
        
2. **Mapear las Relaciones "Puente" (Usando Paso 3 o 4):**
    
    - Una vez que `ASOC_REQUISITO_DE_PUESTO` es una relación, la tratamos como cualquier otra relación normal.
        
    - Ahora, mapeamos la relación "puente" (ej: `VALIDADA_POR` entre `ASOC_REQUISITO_DE_PUESTO` y `VALIDADOR`) siguiendo las reglas estándar del **Paso 3 (1:1)** o **Paso 4 (1:N)** según corresponda.
	
	    - Dado que `VALIDADA_POR` relaciona `ASOC_REQUISITO_DE_PUESTO[0..N]` ↔ `VALIDADOR[1..1]` se aplicaría el Caso General del Paso 4 con restricción de nulidad
	    - Esquema resultante:
		    - PUESTO_DE_TRABAJO (<u>id_puesto</u>, nombre_puesto)
		    - COMPETENCIA (<u>id_competencia</u>, nombre_competencia)`
		    - VALIDADOR (<u>id_validador</u>, nombre, apellido)
		    - ASOC_REQUISITO_DE_PUESTO(<u>id_puesto</u>, <u>id_competencia</u>, id_validador)
			    - id_puesto -> PUESTO_DE_TRABAJO(id_puesto) [B:C,M:C]
			    - id_competencia -> COMPETENCIA(id_competencia) [B:C,M:C]
			    - id_validador `NOT NULL` -> VALIDADOR(id_validador) [B:R,M:C]


---
### Paso 6: Mapear Relaciones Unarias

Las relaciones unarias (o recursivas), donde una entidad se relaciona consigo misma, no necesitan un conjunto distinto de reglas. Simplemente **aplicamos los mismos Pasos 3, 4 o 5** (para 1:1, 1:N o M:N) que ya hemos visto pero teniendo en cuenta alguna particularidad debida a las especiales características de las relaciones unarias. Estas particularidades son:

- En caso de aplicar **Enfoque de Clave Externa:
	- Renombrar la **Clave Externa (FK) que apuntará a la Clave Primaria (PK) de su _propia_ relación** para que sea comprensible. Para ello, podemos usar el rol dado en el modelo ER al lado de la relación que pasa a ser Clave Externa.
- En caso de aplicar **Enfoque de Relación Adicional**:
	- Renombrar **ambas Claves de la nueva relación** para que sea comprensible. Para ello, podemos usar los roles dados en el modelo ER a cada lado de la relación.

---
#### Aplicación al ejemplo `EMPRESA` :
**Relaciones a incluir en el Esquema Relacional:**

- La relación en ER `EMPLEADO[0..1] (Supervisor)` `SUPERVISA`  `EMPLEADO[0..N] (Supervisado)`. Dada la cardinalidad de la relación se aplica el Caso General del Paso 4: Mapear Relaciones Binarias 1:N, es decir, ==**Enfoque de Clave Externa**==:
	- Como ya teníamos creada la relación para `EMPLEADO`, se añade la Clave Primaria (PK) como Clave Externa (FK) pero renombrandola con el nombre del rol de la relación que pasa a ser Clave Externa, en este caso Supervisor (lado `[0..1]).
	- EMPLEADO(dni, nombre, apellido1, apellido2, fechaNac, direccion, sexo, sueldo, dpto, **supervisor**)
		- supervisor ->  EMPLEADO(dni) [B:N,M:C]

---
### Paso 7: Mapear relaciones de grado n>2

Suponiendo que:
	- existe entidad `E1` en ER (con clave id_E1 y resto_atributos_E1) que ha pasado a ser relación `E1` (con clave `id_E1`).
	- existe entidad `E2` en ER (con clave id_E2 y resto_atributos_E2) que ha pasado a ser relación `E2` (con clave `id_E2`).
	- existe entidad `E3` en ER (con clave id_E3 y resto_atributos_E3) que ha pasado a ser relación `E3` (con clave `id_E3`).
	- existe en ER relación binaria `RE1E2E3` entre `E1[X..Y]` ↔ `E2[X..Y]` ↔ `E3[X..Y]` (donde X puede ser 0 o 1 e Y puede ser 1 o N) y con atributos de relación (atributos_RE1E2E3)

Este escenario es muy similar al mapeo de relaciones M:N y ==**se aplica el Enfoque de Relación Adicional**== teniendo en cuenta las particularidades inherentes a tener un grado de relación mayor que 2.

**Regla**: Se aplica el ==**Enfoque de relación adicional**==
	- Se crea `RE1E2E3` para la relación `RE1E2E3` que se suele denominar **relación de referencia cruzada**. 
	- `RE1E2` contendrá:
		- La **Clave Primaria** de `E1` (`id_E1`) como **Clave Externa (FK)**.
		- La **Clave Primaria** de `E2` (`id_E2`) como **Clave Externa (FK)**.
		- La **Clave Primaria** de `E3` (`id_E3`) como **Clave Externa (FK)**.
		- Cualquier atributo propio de la relación `RE1E2E3`.
    
- **Clave Primaria de `RE1E2E3`**: será una clave compuesta formada por la combinación de las claves externas de acuerdo a las cardinalidades máximas de cada lado de la relación:
	- Cardinalidad máxima N en todos los lados:
		- Será necesario usar todas las Claves Externas como Clave Primaria:
			- (id_E1, id_E2, id_E3).
	- ![600](../imgs/BD%20-%20MapeoER11_XMaXN_XP.png)
		
	- Cardinalidad máxima 1 en un lado y N en el resto:
		- Bastará con usar las claves de los lados con cardinalidad máxima N como clave compuesta. Así, suponiendo que la cardinalidad máxima 1 se da en el lado de la entidad E3:
			- (id_E1, id_E2).
	- ![600](../imgs/BD%20-%20MapeoER12_XMaXN_X1.png)
	- Cardinalidad máxima 1 en dos lados y N en el otro:
		- En este caso hay más de una posibilidad, ya que serán validas las Claves Primarias formadas por la clave del lado con cardinalidad máxima N y cualquiera de las otras claves. Así, suponiendo que la cardinalidad máxima N se da en el lado de la entidad E1:
			- (id_E1, id_E2).
			- (id_E1, id_E3).
	- ![](../imgs/BD%20-%20MapeoER13_XNaX1_X1.png)
	- Cardinalidad máxima 1 en todos los lados:
		- En este caso hay más de una posibilidad, ya que serán validas las Claves Primarias formadas por cualquier combinación de 2 de las 3 claves disponibles:
			- (id_E1, id_E2).
			- (id_E1, id_E3).
			- (id_E2, id_E3).
	- ![600](../imgs/BD%20-%20MapeoER14_X1aX1_X1.png)

- **Ejemplo SUMINISTRA**:
	![600](../imgs/BD%20-%20BigER%20Ternaria%20Proveedor%20Proyecto%20Suministro%20Crows%20Foot.png)
	- Un `PROVEEDOR` puede suministrar _muchos_ `REPUESTO[1..N]` a _muchos_ `PROYECTO([1..N]`).
	- A `PROYECTO` le suministran _muchos_ `PROVEEDOR([1..N]`)  _muchos_ `REPUESTO[1..N]`.
	- Un `REPUESTO` es suministrado a _muchos_ `PROYECTO([1..N]`) por _muchos_ `PROVEEDOR[1..N]`.
	- PROVEEDOR(<u>id_proveedor</u>, resto_atributos_proveedor)
	- PROYECTO(<u>id_proyecto</u>, resto_atributos_proyecto)
	- REPUESTO(<u>id_repuesto</u>, resto_atributos_repuesto)
	- Dado que la cardinalidad máxima es N en todos los lados, la clave primaria estará compuesta por todas las claves de las entidades que asocia:
		- SUMINISTRA(<u>id_proveedor</u>, <u>id_proyecto</u>, <u>id_repuesto</u>, cantidad)
			- id_proveedor -> PROVEEDOR(id_proveedor) [B:C,M:C]
			- id_proyecto -> PROYECTO(id_proyecto) [B:C,M:C]
			- id_repuesto -> REPUESTO(id_repuesto) [B:C,M:C]

---
### Paso 8: Mapear Jerarquías (Especialización/Generalización)

Este es el paso más flexible del mapeo, ya que ofrece múltiples estrategias. La elección correcta depende de dos factores:

1. **Las restricciones de la jerarquía**: ¿Es total o parcial? ¿Disjunta o solapada?
    
2. **Los objetivos de rendimiento**: ¿Preferimos minimizar `NULL`s (lo que puede requerir más tablas y `JOIN`s) o preferimos minimizar `JOIN`s (a costa de aceptar `NULL`s)?
    
En función de las respuestas a estas preguntas tenemos 4 opciones (8A, 8B, 8C, 8D)En total, tenemos 4 opciones que se exponen a continuación.

---
#### Opción 8A: Múltiples relaciones (Superclase + Subclases)

Esta es la **estrategia más limpia, flexible y recomendada en general**.

- **Regla**: Se crea **una relación para la superclase** `C` (con su PK y sus atributos comunes) Y **una relación separada por cada subclase** `S1`, `S2`, etc. (con la misma PK pero solo sus atributos específicos).
    
- **Esquema Lógico**:
    - La clave primaria `k` de la superclase es la **Clave Primaria (PK)** de **TODAS** las relaciones.
    - En cada relación de subclase, esta `k` también actúa como **Clave Foránea (FK)** que referencia a la relación de la superclase.
    
    ![600](../imgs/BD%20-%20MapeoER15_Jerarquia%208A.png)
- **Ventajas**:
    - **Funciona SIEMPRE**: Es la única opción válida para _cualquier_ tipo de especialización (total, parcial, disjunta o solapada).
    - **Sin redundancia**: Los atributos comunes se almacenan una sola vez.
    - **Sin `NULL`s**: No se rellenan columnas con valores nulos para atributos que no aplican.
        
- **Desventajas**:
    - Requiere `JOIN`s: Para obtener la información completa de una entidad (ej: un Ingeniero), se necesita una operación `JOIN` entre `EMPLEADO` e `INGENIERO`.
- **Ejemplo TipoTrabajo**:
	![400](../imgs/BD%20-%20BigER%20Ejemplo%20Tipo%20Trabajo.png)
	
	- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono)
	- ADMINISTRATIVO(<u>dni</u>, categoria)
		- dni -> EMPLEADO(dni) [B:C,M:C]
	- TECNICO(<u>dni</u>, nivel)
		- dni -> EMPLEADO(dni) [B:C,M:C]
	- INGENIERO(<u>dni</u>, tipoIng)
		- dni -> EMPLEADO(dni) [B:C,M:C]

---
#### Opción 8B: Múltiples relaciones (Solo Subclases)

- **Regla**: Se crea una relación **para cada subclase** `S1`, `S2`, etc.. **No se crea una relación para la superclase** `C`.
    
- **Esquema Lógico**:
    - Cada relación de subclase contiene:
	    - sus atributos específicos
	    - todos los atributos comunes heredados de la superclase.
    ![600](../imgs/BD%20-%20MapeoER15_Jerarquia%208B.png)
- **Ventajas**:
    - Cero `NULL`s.
    - Consultas a una subclase específica son rápidas (no hay `JOIN`s).
        
- **Desventajas**:
    - **Mucha redundancia**: Los atributos comunes de la superclase `C` se repiten en cada relación.
    - **Difícil de consultar**: Consultar sobre _toda_ la superclase (ej: "todos los VEHICULOS") es muy complejo (requiere `UNION` o `FULL JOIN` de todas las tablas de subclase).
        
- **Cuándo usarla**: **SOLO** funciona si la especialización es **TOTAL y DISJUNTA**

- **Ejemplo Vehículo**:
	![400](../imgs/BD%20-%20Big%20ER%20Jerarquia%20Vehiculo.png)
	- COCHE(<u>id_vehiculo</u>, matricula, precio, max_velocidad, num_pasajeros)
	- CAMION(<u>id_vehiculo</u>, matricula, precio, tonelaje, numero_ejes)
      

---
#### Opción  8C: Relación Única (con Atributo de Tipo)

- **Regla**: Se crea **UNA SOLA RELACIÓN** para la superclase `C`.
    
- **Esquema Lógico**:
    - Esta relación contiene los atributos comunes **MÁS** todos los atributos específicos de **TODAS** las subclases.
    - Se añade un atributo especial **discriminante** (o "de tipo", `t`) que indica a qué subclase pertenece cada fila (ej: `TipoTrabajo = 'Ingeniero'`) .
	![600](../imgs/BD%20-%20MapeoER15_Jerarquia%208C.png)
- **Ventajas**:
    - **Muy eficiente**: No se necesitan `JOIN`s ni `UNION`s.
        
- **Desventajas**:
    - **Muchísimos valores `NULL`**. Un `INGENIERO` tendrá `NULL` en las columnas `categoria` (de `ADMINISTRATIVO`) y `nivel` (de `TECNICO`).
        
- **Cuándo usarla**: **SOLO** funciona para especializaciones **DISJUNTAS** (sean totales o parciales).

- **Ejemplo TipoTrabajo**:
	![400](../imgs/BD%20-%20BigER%20Ejemplo%20Tipo%20Trabajo.png)
	- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono, **tipo_trabajo**, categoria, nivel, tipoIng)

---
#### Opción 8D: Relación Única (con Atributos Booleanos)

- **Regla**: Se crea **UNA SOLA TABLA** (igual que 8C).
    
- **Esquema Lógico**:
    - Esta relación contiene los atributos comunes **MÁS** todos los atributos específicos de **TODAS** las subclases.
    - En lugar de _un_ atributo de tipo, se usan **múltiples atributos booleanos (flags)** (`t1`, `t2`...), uno por cada subclase, para indicar pertenencia .
	![600](../imgs/BD%20-%20MapeoER15_Jerarquia%208D.png)
- **Ventajas**:
    - **Muy eficiente**: No se necesitan `JOIN`s ni `UNION`s.
        
- **Desventajas**:
    - **Muchísimos valores `NULL`**. Un `INGENIERO` tendrá `NULL` en las columnas `categoria` (de `ADMINISTRATIVO`) y `nivel` (de `TECNICO`).

- **Cuándo usarla**: Es la unica opción de relación única con especializaciones **SOLAPADAS** (también sirve para **DISJUNTAS**).
    
- **Ejemplo Persona**:
	![350](../imgs/BD%20-%20BigER%20Jerarquia%20Universidad%20Sencilla.png)
	- PERSONA(<u>dni</u>, nombre, apellido1,apellido2, **estudiante**, **empleado**, notaSelectividad, puesto, salario)

 ---
    
## 3: Ejemplo EMPRESA Completo

Nuestro ejemplo EMPRESA al pasarlo al modelo relacional quedaría así:

- EMPLEADO(<u>dni</u>, nombre, apellido1, apellido2, fechaNac, direccion, sexo, sueldo, dpto, supervisor)
	- dpto `NOT NULL` ->  DEPARTAMENTO(numero_departamento) [B:R,M:C]
	- supervisor ->  EMPLEADO(dni) [B:N,M:C]
- DEPARTAMENTO(<u>numero_departamento</u>, ==nombre==, director, fechaIngresoDirector)
	- director`NOT NULL`,`UNIQUE`->  EMPLEADO(dni) [B:R,M:C]
- PROYECTO(<u>numero_proyecto</u>, ==nombre==, ubicacion, dpto)
	- dpto `NOT NULL` ->  DEPARTAMENTO(numero_departamento) [B:R,M:C]
- FAMILIAR(<u>empleado</u>, <u>nombre</u>, sexo, fechaNac, relacion) 
	- empleado ->  EMPLEADO(dni) [B:C,M:C]
- UBICACION_DPTO(<u>dpto</u>, <u>nombre</u>) 
	- dpto->  DEPARTAMENTO(numero_departamento) [B:C,M:C]
- TRABAJA_EN(<u>empleado</u>, <u>proyecto</u>, horas)
	- empleado ->  EMPLEADO(dni) [B:C,M:C]
	- proyecto ->  PROYECTO(numero_proyecto) [B:C,M:C]

## 4: Grafo relacional de EMPRESA 

La transformación del esquema E/R al esquema relacional se puede representar mediante un grafo relacional donde se muestran las relaciones con:
 - Clave primaria: subrayado
 - Clave alternativa: subrayado discontinuo
 - Claves externas: flecha hacia la relación a cuya clave primaria hace referencia
	 - Opciones de borrado y modificación: 
		 - restringido o no action (R), 
		 - en cascada (C), 
		 - puesta a nulos (N), 
		 - puesta a valor por defecto (D)
 - Atributos que admiten nulos: asterisco
		 
El grafo relacional de nuestro ejemplo EMPRESA quedaría así:
![](../imgs/BD%20-%20Grafo%20Relacional%20EMPRESA.png)