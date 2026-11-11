# Del Concepto a la Realidad - Mapeo E/R a Relacional

---

tags: #database, #lecture, #entity-relationship #relational
author: aeprieto
origin: [“Fundamentos de Sistemas de Bases de Datos”](https://explora.unex.es/discovery/fulldisplay?docid=alma991004714442207611&context=L&vid=34UEX_INST:34UEX&lang=es&search_scope=MyInst_and_CI&adaptor=Local%20Search%20Engine&tab=Everything&query=any,contains,elmasri&sortby=date_d&facet=frbrgroupid,include,9013732198023457128&offset=0). R. Elmasri, R. y S. B. Navathe.  Addison-Wesley, 2008 (5ª edición) + contenido generado por Gemini 2.5 Pro, Gemini 3.1 Pro y Claude Opus 5.5. Refinado y editado por aeprieto
date: 2025-09-22
last-update: 2026-11-10

---
## Contenidos
1. Introducción: Del Plano Conceptual al Plano Lógico
2. El Algoritmo de Mapeo (Pasos E/R)
	1. Paso 1: Mapear Tipos de Entidad Fuertes (Regulares)
	2. Paso 2: Mapear Tipos de Entidad Débiles
	3. Paso 3: Mapear Relaciones Binarias 1:1 (Cardinalidad Máxima 1 en ambos lados)
	4. Paso 4: Mapear Relaciones Binarias 1:N (Cardinalidad Máxima 1 en un lado y N en el otro)
	5. Paso 5: Mapear Relaciones Binarias M:N (Cardinalidad Máxima N en ambos lados)
		1. Paso 5b: Mapear Entidades Asociativas
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

### 1.2 Convenciones en este tema sobre la notación del esquema relacional

- **Clave primaria**: subrayada.

- **Clave alternativa**: con subrayado discontinuo.

- **Clave externa**: en negrita y cursiva, y debajo de su relación se indica a qué relación hace referencia:
	- ***atributo*** -> RELACION(clave) [B:x,M:y]
	- **opciones de borrado (B) y modificación (M)** de la clave referenciada:
		- **R**: restringido o *no action*. No se permite mientras existan referencias.
		- **C**: en cascada. Se borra o modifica también en las filas que la referencian.
		- **N**: puesta a nulos. La clave externa pasa a `NULL`.
		- **D**: puesta a valor por defecto.
	- **`NOT NULL`** indica que la clave externa no admite nulos.
	- **`UNIQUE`** indica que la clave externa no admite repetidos.

---
## 2 El Algoritmo de Mapeo (Pasos E/R)

Para ver la aplicabilidad del algoritmo, usaremos el diagrama de la base de datos [EMPRESA](anexos/BD%20Empresa%20versión%20final%20-%20BigER.md) como ejemplo a desarrollar cuando corresponda en el paso correspondiente.

---
### Paso 1: Mapear Tipos de Entidad Fuertes (Regulares)

**Regla:** Por cada entidad fuerte `E`, se crea una nueva **Relación** `R`.

- Se incluyen todos los atributos de `E` como atributos de `R`.
    
- Si tuviéramos atributos **compuestos** sobre los que se necesitará operar, se "descomponen", si no se ha hecho ya, en sus atributos simples (ej: `nombre_completo` → `nombre`, `apellido_1`, `apellido_2`).
    
- Se <u>subraya</u> la **Clave Primaria** de `E` como **Clave Primaria (PK)** de `R`.
    
- Se usa <span style="text-decoration: underline dashed; text-underline-offset: 3px;">subrayado discontinuo</span> si hubiera **claves alternativas**.
    
- A esta relación se podrán añadir más atributos para representar asociaciones 1:N o 1:1 en las que intervenga.

![](../imgs/BD%20-%20MapeoER01_EntidadRegular.png)

---
#### Aplicación al ejemplo `EMPRESA`
**Relaciones a incluir en el Esquema Relacional:**
- EMPLEADO(<u>dni</u>, nombre, apellido_1, apellido_2, fecha_nacimiento, direccion, sexo, sueldo)
- DEPARTAMENTO(<u>numero_departamento</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>)
- PROYECTO(<u>numero_proyecto</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ubicacion)

---

### Paso 2: Mapear Tipos de Entidad Débiles

**Regla:** Por cada entidad débil `D` que depende de una entidad fuerte propietaria `P`, se crea una nueva **Relación** `D`.

- Se incluyen todos los atributos simples de `D`.
    
- Se añade la **Clave Primaria (PK)** de la propietaria `P` a `D`. Esta será una **Clave Externa (FK)** que referencia a `P`.
	- Si la entidad débil `D` depende de más de una entidad propietaria, se añaden las **Claves Primarias** de cada una de ellas como **Clave Externa (FK)**.
    
- La **Clave Primaria (PK)** de `D` es una clave **compuesta** por:
	- La **Clave Primaria** de la propietaria `P` (la **Clave Externa (FK)** que acabamos de añadir).
		- Si la entidad débil `D` depende de más de una entidad propietaria, cada una de las **Claves Primarias** de cada una de ellas formará parte de la **Clave Primaria**.
	- La **clave parcial** de `D` .
        

![](../imgs/BD%20-%20MapeoER02_EntidadDebil.png)

---
#### Aplicación al ejemplo `EMPRESA`:
**Relaciones a incluir en el Esquema Relacional:**
- FAMILIAR(<u>***empleado***</u>, <u>nombre</u>, sexo, fecha_nacimiento, relacion) 
	- ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
- UBICACION_DEPARTAMENTO(<u>***dpto***</u>, <u>nombre</u>) 
	- ***dpto*** -> DEPARTAMENTO(numero_departamento) [B:C,M:C]
   
---
### Paso 3: Mapear Relaciones Binarias 1:1 (Cardinalidad Máxima 1 en ambos lados)

Suponiendo que:
- existe la entidad `E_1` en el modelo ER (con clave `id_e_1` y `resto_atributos_e_1`), que ya ha pasado a ser la relación `E_1` en el Paso 1.
- existe la entidad `E_2` en el modelo ER (con clave `id_e_2` y `resto_atributos_e_2`), que ya ha pasado a ser la relación `E_2` en el Paso 1.
- existe en el modelo ER la relación binaria `r_e_1_e_2` entre `E_1[X..1] ↔ E_2[X..1]` (donde X puede ser 0 o 1), con atributos de relación (`atributos_r_e_1_e_2`).

La estrategia de mapeo depende **enteramente de las cardinalidades mínimas** (la participación).

---

#### Caso A: Participación Total en un lado y Opcional en el otro

**(Cardinalidades: `E_1[1..1]` ↔ `E_2[0..1]`)**

Este es el caso más común para una relación 1:1.

- **Regla**: Se usa el ***Enfoque de Clave Externa***. 
	- Se añade la Clave Primaria de la entidad `E_1` que no está obligada a relacionarse con la entidad `E_2` como Clave Externa (FK) a la relación `E_2` dado que sí está obligada a tener esa relación con `E_1` para evitar `NULL`.

- **Restricción extra**: Esta nueva Clave Externa (FK) debe tener una restricción `UNIQUE` para garantizar que la cardinalidad máxima sea 1 y no N.
		
- **Atributos de la relación**: Si la relación `r_e_1_e_2` tenía atributos, migran junto con la Clave Externa (FK) a `E_2`.

- **Esquema final**:
	- E_1(<u>id_e_1</u>, resto_atributos_e_1)
	- E_2(<u>id_e_2</u>, resto_atributos_e_2, ***id_e_1***, atributos_r_e_1_e_2)
		- ***id_e_1*** `NOT NULL`, `UNIQUE`->  E_1(id_e_1) [B:R,M:C]

![](../imgs/BD%20-%20MapeoER03_01a11.png)

- **Ejemplo**:
	 ![200](../imgs/BD%20-%20BigER%20Relacion%2001%2011.png)
	- Una `PROVINCIA` debe tener de capital **un** `MUNICIPIO` (`[1..1]`).
	- Un `MUNICIPIO` puede ser capital o no de **una** `PROVINCIA` (`[0..1]`).
	- MUNICIPIO(<u>id_municipio</u>, resto_atributos_municipio)
	- PROVINCIA(<u>id_provincia</u>, resto_atributos_provincia, ***capital***)
		- ***capital*** `NOT NULL`, `UNIQUE`->  MUNICIPIO(id_municipio) [B:R,M:C]


>[!tip] Importante
> No es obligatorio que el atributo que actúa como clave externa comparta el nombre con la clave primaria de la tabla origen. En este ejemplo, usar ***capital*** en lugar de ***id_municipio*** deja mucho más claro el propósito de la referencia. Además, hay que tener en cuenta que esto va más allá de una simple preferencia de diseño y a menudo es una necesidad técnica estricta. Si en la relación `PROVINCIA` necesitaras incluir otra clave externa apuntando a `MUNICIPIO` (por ejemplo, ***municipio_mas_poblado***), asignarles nombres distintos basados en su rol es la única forma de evitar una colisión de atributos.

---

#### Caso B: Participación Opcional en ambos lados

**(Cardinalidades: `E_1[0..1]` ↔ `E_2[0..1]`)**

- **Regla**: Se usa el ==**Enfoque de relación adicional**==. 
	- Si usáramos el ***Enfoque de Clave Externa*** (Caso A), la Clave Externa FK tendría que admitir `NULL`s, lo cual es menos "limpio" y eficiente. Para evitarlo:
		- se crea una nueva relación `r_e_1_e_2` que representa la relación `r_e_1_e_2` del modelo ER:
			- `r_e_1_e_2` contiene las PKs de `E_1` y `E_2` como FKs.
			- Se elige una de las FKs (ej: `id_e_1`) como PK de `r_e_1_e_2`. La otra (`id_e_2`) será Clave Alternativa (`UNIQUE`).

- **Restricción extra**: La FK no elegida como PK en `r_e_1_e_2` debe ser `NOT NULL` y `UNIQUE` para garantizar que la cardinalidad máxima sea 1 y no N.
    
- **Atributos de la relación**: Si la relación `r_e_1_e_2` tenía atributos, estos formarán parte de `r_e_1_e_2`.

- **Esquema final**:
	- E_1(<u>id_e_1</u>, resto_atributos_e_1)
	- E_2(<u>id_e_2</u>, resto_atributos_e_2)
	- r_e_1_e_2(<u>***id_e_1***</u>, ***id_e_2***, atributos_r_e_1_e_2)
		- ***id_e_1*** ->  E_1(id_e_1) [B:C,M:C]
		- ***id_e_2*** `NOT NULL`, `UNIQUE`->  E_2(id_e_2) [B:C,M:C]

![](../imgs/BD%20-%20MapeoER04_01a01.png)

- **Ejemplo**:
	 ![200](../imgs/BD%20-%20BigER%20Relacion%2001%2001.png)
	- Un `TRABAJADOR` puede tener asignada (o no) **una** `PLAZA_APARCAMIENTO` (`[0..1]`).
	- Una `PLAZA_APARCAMIENTO` puede estar asignada (o no) a **un** `TRABAJADOR` (`[0..1]`).
	- TRABAJADOR(<u>id_trabajador</u>, resto_atributos_trabajador)
	- PLAZA_APARCAMIENTO(<u>id_plaza</u>, resto_atributos_plaza)
	- TIENE_ASIGNADA(<u>***trabajador***</u>, <span style="font-weight: bold; font-style: italic; text-decoration: underline dashed; text-underline-offset: 3px;">plaza</span>, atributos_relacion)
		- ***trabajador*** ->  TRABAJADOR(id_trabajador) [B:C,M:C]
		- ***plaza*** `NOT NULL`,`UNIQUE` ->  PLAZA_APARCAMIENTO(id_plaza) [B:C,M:C]

---
#### Caso C: Participación Total en ambos lados

**(Cardinalidades: `E_1[1..1]` ↔ `E_2[1..1]`)**

No se da muy a menudo. Implica que las dos entidades deben existir siempre juntas y en la misma cantidad.

- **Regla**: tienes dos opciones válidas:
    1. **Opción habitual**: ***Enfoque de Clave Externa***. Similar al Caso A, con la diferencia de que se puede elegir entre que la PK de `E_1` migre como FK a `E_2` o, al revés, que la PK de `E_2` migre como FK a `E_1`: da igual. Al igual que en el Caso A, la FK debe ser `NOT NULL` y `UNIQUE` para mantener la cardinalidad máxima en 1, y la relación que recibe la FK recibe también los atributos de la relación `r_e_1_e_2`.
    ![](../imgs/BD%20-%20MapeoER05_11a11.png)
        - **Por ejemplo**:
            ![200](../imgs/BD%20-%20BigER%20Relacion%2011%2011.png)
            - Un `AUTOMOVIL` debe tener **un** `SEGURO` (`[1..1]`).
            - Un `SEGURO` solo pertenece a **un** `AUTOMOVIL` (`[1..1]`).
            - SEGURO(<u>id_seguro</u>, resto_atributos_seguro)
            - AUTOMOVIL(<u>id_automovil</u>, resto_atributos_automovil, ***seguro***)
                - ***seguro*** `NOT NULL`, `UNIQUE` -> SEGURO(id_seguro) [B:R,M:C]
    2. **Opción menos habitual: ==Enfoque de Fusión en una relación==**. Solo es adecuado si las dos entidades son conceptualmente muy cercanas. Se crea una sola relación que contiene todos los atributos de `E_1`, de `E_2` y de la relación `r_e_1_e_2`. Las claves de `E_1` y `E_2` serán `NOT NULL` y `UNIQUE` en la nueva relación, y se elige cualquiera de ellas como su Clave Primaria. En resumen:
        - r_e_1_e_2(<u>id_e_1</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">id_e_2</span>, resto_atributos_e_1, resto_atributos_e_2, atributos_r_e_1_e_2)
            - <span style="text-decoration: underline dashed; text-underline-offset: 3px;">id_e_2</span> `NOT NULL`, `UNIQUE`
        ![](../imgs/BD%20-%20MapeoER05_11a11%20excepcional.png)
        - **Por ejemplo**:
            ![200](../imgs/BD%20-%20BigER%20Relacion%2011%2011.png)
            - Un `AUTOMOVIL` debe tener **un** `SEGURO` (`[1..1]`).
            - Un `SEGURO` solo pertenece a **un** `AUTOMOVIL` (`[1..1]`).
            - AUTOMOVIL_CON_SEGURO(<u>id_automovil</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">id_seguro</span>, resto_atributos_automovil, resto_atributos_seguro)
                - <span style="text-decoration: underline dashed; text-underline-offset: 3px;">id_seguro</span> `NOT NULL`, `UNIQUE`

---

#### Aplicación al ejemplo `EMPRESA`:
**Relaciones a incluir en el Esquema Relacional:**

- La relación en ER `Empleado[1..1]` `dirige` `Departamento [0..1]` es el **Caso A** y se aplica el ***Enfoque de Clave Externa***. Como ya teníamos creadas las relaciones para `EMPLEADO` y `DEPARTAMENTO` hay que modificar `DEPARTAMENTO`:
	- DEPARTAMENTO(<u>numero_departamento</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ***director***, fecha_ingreso_director)
		- ***director*** `NOT NULL`, `UNIQUE`->  EMPLEADO(dni) [B:R,M:C]

---
### Paso 4: Mapear Relaciones Binarias 1:N (Cardinalidad Máxima 1 en un lado y N en el otro)

Suponiendo que:
- existe la entidad `E_1` en el modelo ER (con clave `id_e_1` y `resto_atributos_e_1`), que ya ha pasado a ser la relación `E_1` en el Paso 1.
- existe la entidad `E_2` en el modelo ER (con clave `id_e_2` y `resto_atributos_e_2`), que ya ha pasado a ser la relación `E_2` en el Paso 1.
- existe en el modelo ER la relación binaria `r_e_1_e_2` entre `E_1[X..1] ↔ E_2[X..N]` (donde X puede ser 0 o 1), con atributos de relación (`atributos_r_e_1_e_2`).

Recuerda cómo se lee: el ==1== máximo escrito junto a `E_1` indica que cada instancia de `E_2` se relaciona como mucho con **una** de `E_1`. Por eso, en el lado ==N==, la información de la relación cabe en la propia `E_2`.

---
#### Caso General: Participación total u opcional con pocos nulos
>[!question] ¿Cuándo usar esta alternativa?
>1. ==**Participación total u opcionalidad con pocos NULLs**==: si la participación de `E_2` (lado N) es total o, siendo opcional, se espera que la mayoría de instancias de `E_2` estén relacionadas con instancias de `E_1`.
>2. ==**No se espera evolución futura a M:N**==: si no se prevé que la relación 1:N pueda convertirse en M:N en el futuro.
>3. ==**Pocos atributos en la relación**==: si la relación `r_e_1_e_2` tiene pocos o ningún atributo propio.

- **Regla**: se aplica el ***Enfoque de Clave Externa***.
	- Se añade la PK de `E_1` (`id_e_1`) a `E_2` como **Clave Externa (FK)**.
- **Atributos de la relación**: si la relación `r_e_1_e_2` tenía atributos, migran junto con la Clave Externa (FK) a `E_2`.
- **Restricción de nulidad**: si la FK admite **NULL**s depende de la **cardinalidad mínima escrita junto a** `E_1`, que es la que indica si cada instancia de `E_2` está obligada a relacionarse con una de `E_1`:

	**Cardinalidades `E_1[1..1] ↔ E_2[X..N]`**
	Toda instancia de `E_2` tiene que estar relacionada con una de `E_1` (mínimo 1), así que la FK (`id_e_1`) en `E_2` **DEBE ser `NOT NULL`**.

	- **Esquema final**:
	    - E_1(<u>id_e_1</u>, resto_atributos_e_1)
	    - E_2(<u>id_e_2</u>, resto_atributos_e_2, ***id_e_1***, atributos_r_e_1_e_2)
	        - ***id_e_1*** `NOT NULL` -> E_1(id_e_1) [B:R,M:C]



	![](../imgs/BD%20-%20MapeoER06_11a0NyER07_11a1N.png)


	- **Ejemplo**:
		![200](../imgs/BD%20-%20BigER%20Relacion%2011%201N.png)

	- Una `PROVINCIA` **debe** tener al menos **un** `MUNICIPIO`, y puede tener **muchos** (`[1..N]`).
	- Un `MUNICIPIO` **debe** pertenecer a **una** `PROVINCIA` (`[1..1]`).
	    - PROVINCIA(<u>id_provincia</u>, resto_atributos_provincia)
	    - MUNICIPIO(<u>id_municipio</u>, resto_atributos_municipio, ***provincia***)
	        - ***provincia*** `NOT NULL` -> PROVINCIA(id_provincia) [B:R,M:C]


	**Cardinalidades `E_1[0..1] ↔ E_2[X..N]`**
	Una instancia de `E_2` puede no estar relacionada con ninguna de `E_1` (mínimo 0), así que la FK (`id_e_1`) en `E_2` **DEBE admitir `NULL`**.

	- **Esquema final**:
	    - E_1(<u>id_e_1</u>, resto_atributos_e_1)
	    - E_2(<u>id_e_2</u>, resto_atributos_e_2, ***id_e_1***, atributos_r_e_1_e_2)
	        - ***id_e_1*** -> E_1(id_e_1) [B:N,M:C]


	![](../imgs/BD%20-%20MapeoER08_01a0NyER09_01a1N_NORMAL.png)

	- **Ejemplo**:

	![200](../imgs/BD%20-%20BigER%20Relacion%2001%200N.png)

	- Un `SOCIO` puede tener (o no) **muchos** ejemplares en préstamo (`[0..N]`).
	- Un `EJEMPLAR` puede estar (o no) prestado a **un** `SOCIO` (`[0..1]`).
	- Se espera que la mayoría de los ejemplares estén prestados.
	- Solo se guarda el préstamo actual, y no se prevé guardar el histórico.
	- La relación solo tiene un atributo: la fecha del préstamo.
	    - SOCIO_BIBLIOTECA(<u>id_socio</u>, resto_atributos_socio)
	    - EJEMPLAR_DE_LIBRO(<u>id_ejemplar</u>, resto_atributos_ejemplar, ***socio***, fecha_prestamo)
	        - ***socio*** -> SOCIO_BIBLIOTECA(id_socio) [B:N,M:C]
	
---
#### Caso Excepcional: Participación opcional con muchos nulos
>[!question] ¿Cuándo usar esta alternativa?
>1. ==**Opcionalidad alta con muchos NULL**s**==: si la participación de `E_2` (lado N) se espera **muy opcional**, es decir, que la mayoría de instancias de `E_2` no estén relacionadas con instancias de `E_1` y, por tanto, la mayoría de las filas de `E_2` tendrían `NULL` en la FK.
>2. ==**Evolución futura a M:N**==: si se prevé que la relación 1:N pueda convertirse en M:N en el futuro, para facilitar la migración.
>3. ==**Muchos atributos en la relación**==: si la relación `r_e_1_e_2` tiene muchos atributos propios, para evitar «ensuciar» demasiado la relación `E_2`.

- **Regla**: se aplica el ==Enfoque de relación adicional==.
    - Se crea una nueva relación `r_e_1_e_2` que representa la relación del modelo ER, y que contendrá:
        - las PKs de `E_1` y `E_2` como FKs;
        - los atributos de la relación.
- **Clave Primaria de `r_e_1_e_2`**: la FK del lado N (`id_e_2`), porque cada instancia de `E_2` se relaciona como mucho con una de `E_1`.
    - Además, la FK `id_e_1` será `NOT NULL`, porque una fila de `r_e_1_e_2` solo existe si relaciona instancias de las dos entidades.
- **Esquema final**:
    - E_1(<u>id_e_1</u>, resto_atributos_e_1)
    - E_2(<u>id_e_2</u>, resto_atributos_e_2)
    - r_e_1_e_2(***id_e_1***, <u>***id_e_2***</u>, atributos_r_e_1_e_2)
        - ***id_e_1*** `NOT NULL` -> E_1(id_e_1) [B:C,M:C]
        - ***id_e_2*** -> E_2(id_e_2) [B:C,M:C]

	![](../imgs/BD%20-%20MapeoER08_01a0NyER09_01a1N_EXCEPCIONAL.png)

	- **Ejemplo**:
		![200](../imgs/BD%20-%20BigER%20Relacion%2001%200N.png)

	- Un `SOCIO` puede tener (o no) **muchos** ejemplares en préstamo (`[0..N]`).
	- Un `EJEMPLAR` puede estar (o no) prestado a **un** `SOCIO` (`[0..1]`).
	- Se espera que la mayoría de los ejemplares **no** estén prestados.
	- Por ahora solo se guarda el préstamo actual, pero se prevé que **en el futuro** se quiera guardar el histórico de préstamos (pasaría a ser M:N).
	- La relación tiene varios atributos.
	    - SOCIO_BIBLIOTECA(<u>id_socio</u>, resto_atributos_socio)
	    - EJEMPLAR_DE_LIBRO(<u>id_ejemplar</u>, resto_atributos_ejemplar)
	    - PRESTAMO(***socio***, <u>***ejemplar***</u>, fecha_prestamo, fecha_devolucion_prevista, observaciones)
	        - ***socio*** `NOT NULL` -> SOCIO_BIBLIOTECA(id_socio) [B:C,M:C]
	        - ***ejemplar*** -> EJEMPLAR_DE_LIBRO(id_ejemplar) [B:C,M:C]

---
#### Aplicación al ejemplo `EMPRESA`:

**Relaciones a incluir en el Esquema Relacional:**

- La relación `trabaja_para` (`Empleado[1..N] ↔ Departamento[1..1]`) es el **Caso General** y se aplica el ***Enfoque de Clave Externa***. Como ya teníamos creadas las relaciones `EMPLEADO` y `DEPARTAMENTO`, hay que modificar `EMPLEADO`:
    - EMPLEADO(<u>dni</u>, nombre, apellido_1, apellido_2, fecha_nacimiento, direccion, sexo, sueldo, ***dpto***)
        - ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
- La relación `controla` (`Departamento[1..1] ↔ Proyecto[0..N]`) es el **Caso General** y se aplica el ==Enfoque de Clave Externa==. Como ya teníamos creadas las relaciones `DEPARTAMENTO` y `PROYECTO`, hay que modificar `PROYECTO`:
    - PROYECTO(<u>numero_proyecto</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ubicacion, ***dpto***)
        - ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]

---
### Paso 5: Mapear Relaciones Binarias M:N (Cardinalidad Máxima N en ambos lados)

Suponiendo que: 
- existe la entidad `E_1` en el modelo ER (con clave `id_e_1` y `resto_atributos_e_1`), que ya ha pasado a ser la relación `E_1` en el Paso 1.
- existe la entidad `E_2` en el modelo ER (con clave `id_e_2` y `resto_atributos_e_2`), que ya ha pasado a ser la relación `E_2` en el Paso 1.
- existe en el modelo ER la relación binaria `r_e_1_e_2` entre `E_1[X..N] ↔ E_2[X..N]` (donde X puede ser 0 o 1), con atributos de relación (`atributos_r_e_1_e_2`).

Como cada instancia de `E_1` puede relacionarse con muchas de `E_2` y al revés, la relación no cabe en ninguna de las dos entidades: necesita una relación propia.

- **Regla**: se aplica el ==Enfoque de relación adicional==.
    - Se crea una nueva relación `r_e_1_e_2`, que se suele denominar **relación de referencia cruzada**, y que contendrá:
        - la **Clave Primaria** de `E_1` (`id_e_1`) como **Clave Externa (FK)**;
        - la **Clave Primaria** de `E_2` (`id_e_2`) como **Clave Externa (FK)**;
        - cualquier atributo propio de la relación `r_e_1_e_2`.
- **Clave Primaria de `r_e_1_e_2`**: la clave compuesta formada por las dos claves externas (`id_e_1`, `id_e_2`).
- **Esquema final**:
    - E_1(<u>id_e_1</u>, resto_atributos_e_1)
    - E_2(<u>id_e_2</u>, resto_atributos_e_2)
    - r_e_1_e_2(<u>***id_e_1***, ***id_e_2***</u>, atributos_r_e_1_e_2)
        - ***id_e_1*** -> E_1(id_e_1) [B:C,M:C]
        - ***id_e_2*** -> E_2(id_e_2) [B:C,M:C]

	![](../imgs/BD%20-%20MapeoER10_XMaXN.png)

- **Ejemplo**:

	![200](../imgs/BD%20-%20BigER%20Relacion%200N%200N.png)

- Un `TURISTA` puede visitar (o no) **muchos** monumentos (`[0..N]`).
- Un `MONUMENTO` puede ser visitado (o no) por **muchos** turistas (`[0..N]`).
    - TURISTA(<u>id_turista</u>, resto_atributos_turista)
    - MONUMENTO(<u>id_monumento</u>, resto_atributos_monumento)
    - VISITA(<u>***turista***, ***monumento***</u>, atributos_visita)
        - ***turista*** -> TURISTA(id_turista) [B:C,M:C]
        - ***monumento*** -> MONUMENTO(id_monumento) [B:C,M:C]

---
#### Aplicación al ejemplo `EMPRESA`:

**Relaciones a incluir en el Esquema Relacional:**
- La relación `trabaja_en` (`Empleado[1..N] ↔ Proyecto[0..N]`) es M:N. Como ya teníamos creadas las relaciones `EMPLEADO` y `PROYECTO`, solo hay que crear la nueva relación `TRABAJA_EN`:
    - TRABAJA_EN(<u>***empleado***, ***proyecto***</u>, horas)
        - ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
        - ***proyecto*** -> PROYECTO(numero_proyecto) [B:C,M:C]

---
#### Paso 5b: Mapear Entidades Asociativas 

Este paso se aplica cuando hemos modelado una relación M:N como una **entidad asociativa**, para que pueda participar en otras relaciones.

Una entidad asociativa se mapea **exactamente igual que la relación `r_e_1_e_2` del Paso 5 (Relaciones M:N)**. La diferencia está en lo que viene después: como ahora es una relación más, puede recibir las claves externas de las relaciones en las que participa con otras entidades.

Usaremos el ejemplo de la sección 5.5 del Tema 5, donde la relación M:N entre `Puesto_de_Trabajo` y `Competencia` se convirtió en la entidad asociativa `Requisito_Puesto`, que a su vez se relaciona con `Validador`.

![](../imgs/BD%20-%20BigER%20Entidad%20Asociativa%20Ejemplo.png)

**Regla:** El mapeo se hace en dos etapas:

1. **Mapear la entidad asociativa (igual que el Paso 5)**:
    - La entidad asociativa (`Requisito_Puesto`) se convierte en una nueva relación, exactamente como si fuera una relación M:N.
    - Sus dos **relaciones asociativas** (`requiere` y `es_requerida_por`) no generan relaciones propias y se convierten en las dos claves externas, que juntas forman su **Clave Primaria** (`puesto` + `competencia`).
    - Los atributos propios de la entidad asociativa, si los tuviera, se añaden como atributos.
2. **Mapear sus relaciones con otras entidades (Paso 3 o 4)**:
    - Una vez que `REQUISITO_PUESTO` es una relación, la tratamos como cualquier otra.
    - Mapeamos la relación `validado_por` (`Requisito_Puesto[0..N] ↔ Validador[1..1]`) con las reglas del Paso 4: es el **Caso General** con participación total, así que la FK hacia `VALIDADOR` va en `REQUISITO_PUESTO` y es `NOT NULL`.

- **Esquema resultante**:
    - PUESTO_DE_TRABAJO(<u>id_puesto</u>, nombre_puesto)
    - COMPETENCIA(<u>id_competencia</u>, nombre_competencia)
    - VALIDADOR(<u>id_validador</u>, rol_empresa)
    - REQUISITO_PUESTO(<u>***puesto***, ***competencia***</u>, ***validador***)
        - ***puesto*** -> PUESTO_DE_TRABAJO(id_puesto) [B:C,M:C]
        - ***competencia*** -> COMPETENCIA(id_competencia) [B:C,M:C]
        - ***validador*** `NOT NULL` -> VALIDADOR(id_validador) [B:R,M:C]

---
### Paso 6: Mapear Relaciones Unarias

Las relaciones unarias (o recursivas), donde una entidad se relaciona consigo misma, no necesitan un conjunto distinto de reglas. Simplemente **aplicamos los mismos Pasos 3, 4 o 5** (para 1:1, 1:N o M:N), teniendo en cuenta una particularidad: las claves externas apuntan a la propia relación, así que hay que **renombrarlas** para que se entiendan.

- Si se aplica el ***Enfoque de Clave Externa***:
    - Se renombra la **Clave Externa (FK)** que apunta a la **Clave Primaria (PK) de su propia relación**, usando el rol del modelo ER del lado que pasa a ser Clave Externa.
- Si se aplica el ==**Enfoque de Relación Adicional**==:
    - Se renombran **las dos claves de la nueva relación**, usando los roles del modelo ER de cada lado.

---
#### Aplicación al ejemplo `EMPRESA`:
**Relaciones a incluir en el Esquema Relacional:**

- La relación `supervisa` (`Empleado[0..1 | "Supervisor"] ↔ Empleado[0..N | "Supervisado"]`) es 1:N, así que se aplica el **Caso General del Paso 4**, es decir, el ***Enfoque de Clave Externa***:
    - Como ya teníamos creada la relación `EMPLEADO`, se le añade su propia Clave Primaria como Clave Externa, renombrándola con el rol del lado `[0..1]`: `supervisor`.
    - EMPLEADO(<u>dni</u>, nombre, apellido_1, apellido_2, fecha_nacimiento, direccion, sexo, sueldo, ***dpto***, ***supervisor***)
        - ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
        - ***supervisor*** -> EMPLEADO(dni) [B:N,M:C]


---
#### Ejemplo con Enfoque de Relación Adicional

En el Enunciado ER 04 de The Expanse, los minerales se relacionan con otros minerales que suelen encontrarse cerca:
(`Mineral[0..N | "Mineral_A"] ↔ Mineral[0..N | "Mineral_B"]`). 
Es una relación M:N, así que se aplica el Paso 5, renombrando las dos claves de la nueva relación con los roles:
- MINERAL(<u>codigo</u>, nombre, descripcion)
- MINERAL_RELACIONADO(<u>***mineral_a***, ***mineral_b***</u>)
    - ***mineral_a*** -> MINERAL(codigo) [B:C,M:C]
    - ***mineral_b*** -> MINERAL(codigo) [B:C,M:C]

---
### Paso 7: Mapear relaciones de grado n>2

Suponiendo que:
- existen las entidades `E_1`, `E_2` y `E_3` en el modelo ER (con claves `id_e_1`, `id_e_2` e `id_e_3`), que ya han pasado a ser relaciones en el Paso 1.
- existe en el modelo ER la relación **ternaria** `r_e_1_e_2_e_3` entre `E_1[X..Y] ↔ E_2[X..Y] ↔ E_3[X..Y]` (donde X puede ser 0 o 1 e Y puede ser 1 o N), con atributos de relación (`atributos_r_e_1_e_2_e_3`).

Este escenario es muy similar al de las relaciones M:N, y ==se aplica el Enfoque de Relación Adicional==, con las particularidades de tener un grado mayor que 2.

- **Regla**: se aplica el ==Enfoque de relación adicional==.
    - Se crea una nueva relación `r_e_1_e_2_e_3` (relación de referencia cruzada), que contendrá:
        - la **Clave Primaria** de `E_1` (`id_e_1`) como **Clave Externa (FK)**;
        - la **Clave Primaria** de `E_2` (`id_e_2`) como **Clave Externa (FK)**;
        - la **Clave Primaria** de `E_3` (`id_e_3`) como **Clave Externa (FK)**;
        - cualquier atributo propio de la relación `r_e_1_e_2_e_3`.
- **Clave Primaria de `r_e_1_e_2_e_3`**: una clave compuesta por claves externas, que depende de las **cardinalidades máximas** de cada lado:

	**Cardinalidad máxima N en todos los lados**

	Hacen falta todas las claves externas: 
	(`id_e_1`, `id_e_2`, `id_e_3`).

	![](../imgs/BD%20-%20MapeoER11_XMaXN_XP.png)

	**Cardinalidad máxima 1 en un lado y N en el resto**

	Basta con las claves de los lados con cardinalidad máxima N. Así, si la cardinalidad máxima 1 está en el lado de `E_3`: 
	(`id_e_1`, `id_e_2`).

	![](../imgs/BD%20-%20MapeoER12_XMaXN_X1.png)

	**Cardinalidad máxima 1 en dos lados y N en el otro**

	Hay más de una posibilidad: vale la clave del lado N combinada con cualquiera de las otras dos. Así, si la cardinalidad máxima N está en el lado de `E_1`: 
	(`id_e_1`, `id_e_2`) o 
	(`id_e_1`, `id_e_3`).

	![](../imgs/BD%20-%20MapeoER13_XNaX1_X1.png)

	**Cardinalidad máxima 1 en todos los lados**

	Hay más de una posibilidad: vale cualquier combinación de dos de las tres claves:
	(`id_e_1`, `id_e_2`) o
	(`id_e_1`, `id_e_3`) o 
	(`id_e_2`, `id_e_3`).

	![](../imgs/BD%20-%20MapeoER14_X1aX1_X1.png)

- **Ejemplo `suministra`**:

	![](../imgs/BD%20-%20BigER%20Ternaria%20Proveedor%20Proyecto%20Suministro.png)

- Un proveedor puede suministrar muchos repuestos a muchos proyectos (`Repuesto[1..N]`, `Proyecto[1..N]`).
- A un proyecto le suministran muchos proveedores muchos repuestos (`Proveedor[1..N]`, `Repuesto[1..N]`).
- Un repuesto es suministrado a muchos proyectos por muchos proveedores (`Proyecto[1..N]`, `Proveedor[1..N]`).
- Como la cardinalidad máxima es N en todos los lados, la clave primaria está compuesta por las claves de las tres entidades:
    - PROVEEDOR(<u>id_proveedor</u>, resto_atributos_proveedor)
    - PROYECTO(<u>id_proyecto</u>, resto_atributos_proyecto)
    - REPUESTO(<u>id_repuesto</u>, resto_atributos_repuesto)
    - SUMINISTRA(<u>***proveedor***, ***proyecto***, ***repuesto***</u>, cantidad)
        - ***proveedor*** -> PROVEEDOR(id_proveedor) [B:C,M:C]
        - ***proyecto*** -> PROYECTO(id_proyecto) [B:C,M:C]
        - ***repuesto*** -> REPUESTO(id_repuesto) [B:C,M:C]

---
### Paso 8: Mapear Jerarquías (Especialización/Generalización)

Este es el paso más flexible del mapeo, porque ofrece varias estrategias. La elección depende de dos factores:

1. **Las restricciones de la jerarquía**: ¿es total o parcial? ¿disjunta o solapada?
2. **Los objetivos de rendimiento**: ¿preferimos minimizar `NULL`s (lo que puede requerir más relaciones y `JOIN`s) o minimizar `JOIN`s (a costa de aceptar `NULL`s)?

En función de las respuestas, tenemos 4 opciones (8A, 8B, 8C y 8D):

| Opción | Relaciones                                      | Sirve para                                 |
| ------ | ----------------------------------------------- | ------------------------------------------ |
| 8A     | Superclase + una por subclase                   | Cualquier jerarquía                        |
| 8B     | Solo una por subclase                           | Solo `total disjoint`                      |
| 8C     | Una sola, con un atributo de tipo               | Solo `disjoint` (total o parcial)          |
| 8D     | Una sola, con un atributo booleano por subclase | Cualquier jerarquía, también `overlapping` |

---
#### Opción 8A: Múltiples relaciones (Superclase + Subclases)

Es la estrategia **más limpia, flexible y recomendada en general**.

- **Regla**: se crea **una relación para la superclase** (con su PK y sus atributos comunes) y **una relación por cada subclase** (con la misma PK y solo sus atributos específicos).
- **Esquema lógico**:
    - La clave primaria de la superclase es la **Clave Primaria (PK) de TODAS** las relaciones.
    - En cada relación de subclase, esa clave es también **Clave Externa (FK)** hacia la relación de la superclase.

![](../imgs/BD%20-%20MapeoER15_Jerarquia%208A.png)

- **Ventajas**:
    - **Funciona SIEMPRE**: es la única opción de varias relaciones válida para cualquier tipo de jerarquía.
    - **Sin redundancia**: los atributos comunes se almacenan una sola vez.
    - **Sin `NULL`s**: no hay columnas para atributos que no aplican.
- **Desventajas**:
    - **Requiere `JOIN`s**: para obtener toda la información de una entidad (por ejemplo, un ingeniero), hace falta un `JOIN` entre `EMPLEADO` e `INGENIERO`.
- **Ejemplo `por_puesto`**:

	![](../imgs/BD%20-%20BigER%20Jerarquia%20Empleado%20por%20puesto.png)

- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono)
- ADMINISTRATIVO(<u>***dni***</u>, categoria)
    - ***dni*** -> EMPLEADO(dni) [B:C,M:C]
- TECNICO(<u>***dni***</u>, nivel)
    - ***dni*** -> EMPLEADO(dni) [B:C,M:C]
- INGENIERO(<u>***dni***</u>, tipo_ingeniero)
    - ***dni*** -> EMPLEADO(dni) [B:C,M:C]

---
#### Opción 8B: Múltiples relaciones (Solo Subclases)

- **Regla**: se crea una relación **por cada subclase**. **No se crea relación para la superclase**.
- **Esquema lógico**: cada relación de subclase contiene sus atributos específicos y todos los atributos comunes heredados de la superclase.

![](../imgs/BD%20-%20MapeoER15_Jerarquia%208B.png)

- **Ventajas**:
    - Ningún `NULL`.
    - Las consultas sobre una subclase concreta son rápidas, sin `JOIN`s.
- **Desventajas**:
    - **Mucha redundancia en el esquema**: los atributos comunes se repiten en cada relación.
    - **Difícil de consultar la superclase**: consultar todos los vehículos, por ejemplo, requiere unir todas las relaciones de subclase con `UNION`.
- **Cuándo usarla**: **SOLO** funciona si la jerarquía es **total** y **disjunta**. Si fuera parcial, las instancias que no están en ninguna subclase no tendrían dónde guardarse; si fuera solapada, una instancia de varias subclases se guardaría repetida.
- **Ejemplo Vehículo**:

	![400](../imgs/BD%20-%20BigER%20Jerarquia%20Vehiculo.png)

- COCHE(<u>id_vehiculo</u>, matricula, precio, max_velocidad, numero_pasajeros)
- CAMION(<u>id_vehiculo</u>, matricula, precio, tonelaje, numero_ejes)

---
#### Opción 8C: Relación Única (con Atributo de Tipo)

- **Regla**: se crea **UNA SOLA RELACIÓN** para la superclase.
- **Esquema lógico**:
    - Contiene los atributos comunes **MÁS** todos los atributos específicos de **TODAS** las subclases.
    - Se añade un **atributo discriminante** (o «de tipo») que indica a qué subclase pertenece cada fila (por ejemplo, `puesto = 'Ingeniero'`).

![](../imgs/BD%20-%20MapeoER15_Jerarquia%208C.png)

- **Ventajas**:
    - **Muy eficiente**: no hacen falta `JOIN`s ni `UNION`s.
- **Desventajas**:
    - **Muchísimos valores `NULL`**: un ingeniero tendrá `NULL` en `categoria` (de `ADMINISTRATIVO`) y en `nivel` (de `TECNICO`).
- **Cuándo usarla**: **SOLO** funciona para jerarquías **disjuntas**, sean totales o parciales. Con una parcial, el atributo de tipo queda a `NULL` en las filas que no están en ninguna subclase.
- **Ejemplo `por_puesto`**:

	![](../imgs/BD%20-%20BigER%20Jerarquia%20Empleado%20por%20puesto.png)

- EMPLEADO(<u>dni</u>, nombre, fecha_nacimiento, direccion, telefono, **puesto**, categoria, nivel, tipo_ingeniero)

---
#### Opción 8D: Relación Única (con Atributos Booleanos)

- **Regla**: se crea **UNA SOLA RELACIÓN**, igual que en 8C.
- **Esquema lógico**:
    - Contiene los atributos comunes **MÁS** todos los atributos específicos de **TODAS** las subclases.
    - En lugar de _un_ atributo de tipo, se usa un **atributo booleano por cada subclase**, que indica si la fila pertenece a ella.

![](../imgs/BD%20-%20MapeoER15_Jerarquia%208D.png)

- **Ventajas**:
    - **Muy eficiente**: no hacen falta `JOIN`s ni `UNION`s.
- **Desventajas**:
    - **Muchísimos valores `NULL`**: un estudiante que no sea empleado tendrá `NULL` en `puesto` y `salario`.
- **Cuándo usarla**: es la **única opción de relación única** para jerarquías **solapadas**, y también sirve para las disjuntas.
- **Ejemplo Persona**:

	![350](../imgs/BD%20-%20BigER%20Jerarquia%20Universidad%20Sencilla.png)

- PERSONA_UEX(<u>dni</u>, nombre, apellido_1, apellido_2, **es_estudiante**, **es_empleado**, nota_selectividad, puesto, salario)


---
 
## 3 Ejemplo EMPRESA Completo

Nuestro ejemplo EMPRESA, al pasarlo al modelo relacional, quedaría así:

- EMPLEADO(<u>dni</u>, nombre, apellido_1, apellido_2, fecha_nacimiento, direccion, sexo, sueldo, ***dpto***, ***supervisor***)
    - ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
    - ***supervisor*** -> EMPLEADO(dni) [B:N,M:C]
- DEPARTAMENTO(<u>numero_departamento</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ***director***, fecha_ingreso_director)
    - ***director*** `NOT NULL`, `UNIQUE` -> EMPLEADO(dni) [B:R,M:C]
- PROYECTO(<u>numero_proyecto</u>, <span style="text-decoration: underline dashed; text-underline-offset: 3px;">nombre</span>, ubicacion, ***dpto***)
    - ***dpto*** `NOT NULL` -> DEPARTAMENTO(numero_departamento) [B:R,M:C]
- FAMILIAR(<u>***empleado***, nombre</u>, sexo, fecha_nacimiento, relacion)
    - ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
- UBICACION_DEPARTAMENTO(<u>***dpto***, nombre</u>)
    - ***dpto*** -> DEPARTAMENTO(numero_departamento) [B:C,M:C]
- TRABAJA_EN(<u>***empleado***, ***proyecto***</u>, horas)
    - ***empleado*** -> EMPLEADO(dni) [B:C,M:C]
    - ***proyecto*** -> PROYECTO(numero_proyecto) [B:C,M:C]

## 4 Grafo relacional de EMPRESA 

La transformación del esquema E/R al esquema relacional se puede representar mediante un **grafo relacional**, donde se muestran las relaciones con:

- **Clave primaria**: subrayado.
- **Clave alternativa**: subrayado discontinuo.
- **Claves externas**: flecha hacia la relación a cuya clave primaria hacen referencia, con sus opciones de borrado y modificación.
- **Atributos que admiten nulos**: asterisco.

El grafo relacional de nuestro ejemplo EMPRESA quedaría así:

![](../imgs/BD%20-%20Grafo%20Relacional%20EMPRESA.png)



