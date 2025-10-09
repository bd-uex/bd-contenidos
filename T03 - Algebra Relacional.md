# Álgebra relacional

## Contenidos

- Conceptos fundamentales
- Operaciones relacionales unarias
- Operaciones de álgebra relacional a partir de la teoría de conjuntos
- Operaciones relacionales binarias
- Operaciones relacionales adicionales
- Expresando restricciones en álgebra relacional

---

## 1. Conceptos fundamentales

### 1.1. Álgebra relacional

#### Un lenguaje de consulta algebraico

En este tema, se presenta el aspecto de manipulación de datos del modelo relacional. Un modelo de datos no es solo una estructura; necesita una forma de consultar y modificar los datos. Para comenzar nuestro estudio de las operaciones con relaciones, aprenderemos sobre un álgebra especial, llamada álgebra relacional, que consiste en **métodos simples pero eficaces para construir nuevas relaciones a partir de relaciones dadas**.

El álgebra relacional no se utiliza actualmente como lenguaje de consulta en los SGBD comerciales, aunque algunos de los primeros prototipos sí la utilizaban directamente. En cambio, **SQL incorpora el álgebra relacional en su núcleo**. Además, cuando un SGBD procesa consultas, lo primero que ocurre con una consulta SQL es que se traduce a álgebra relacional o a una representación interna muy similar. Por lo tanto, existen varias buenas razones para comenzar a aprender esta álgebra.

#### Álgebra

Un álgebra, en general, consta de **operadores y operandos atómicos.** Por ejemplo, en el álgebra aritmética, los operandos atómicos son variables como $x$ y constantes como `15`. Los operadores son los habituales en la aritmética: suma, resta, multiplicación y división. 

Cualquier álgebra permite **construir expresiones aplicando operadores a operandos atómicos y/u otras expresiones algebraicas**. Normalmente, se necesitan paréntesis para agrupar operadores y sus operandos. Por ejemplo, en aritmética tenemos expresiones como $(x + y) * z$ o $((x + 7)/(2 / -3)) + x.$

El álgebra relacional es otro ejemplo de álgebra. Sus operandos atómicos son:

1. Variables, que representan relaciones.
2. Constantes, que son relaciones finitas.

#### Conceptos y terminología

- El álgebra relacional es el **conjunto básico de operaciones** para el modelo relacional.
- Permiten al usuario **especificar** solicitudes de recuperación básicas (o **consultas** )
- El **resultado** de una operación es **una nueva relación**, que puede haberse formado a partir de una o más relaciones de entrada.
	- **Esta propiedad hace que el álgebra sea *cerrada***: todos los objetos en el álgebra relacional son relaciones.

- Las **operaciones algebraicas producen así nuevas relaciones**
	- Éstas se pueden manipular aún más utilizando operaciones del mismo álgebra.
- Una secuencia de operaciones de álgebra relacional forma una **expresión de álgebra relacional**
	- El resultado de una expresión de álgebra relacional también es una relación que representa el resultado de una consulta a la base de datos (o solicitud de recuperación).

>[!note] Conjuntos vs Multiconjuntos
> Una **relación** en el modelo relacional se corresponde con el **conjunto** matemático de conjunto (set) y, por tanto, no pueden contener tuplas repetidas. En este contenido, se presentan las operaciones de álgebra relacional siguiendo esta consideración.
> Por ejemplo, dada la siguiente relación
> 
> | A   | B   | C |
> |:---:|:---:|:---:|
> | 1 | 2 | 5 |
> | 3 | 4 | 6 |
> | 1 | 2 | 7 |
> | 1 | 2 | 8 |
> 
> si usamos conjuntos el resultado de una operación de proyección de los atributos A y B sería:
>   
> | A   | B   |
> |:---:|:---:|
> | 1 | 2 | 
> | 3 | 4 |
> 
> Sin embargo, desde un **punto de vista práctico**, por cuestiones fundamentalmente de **eficiencia**, se suele relajar esta condición y considerar que las **relaciones** son **multiconjuntos** (multiset o bag), es decir, permiten tuplas duplicadas. 
> En el ejemplo anterior, si usamos multiconjuntos, el resultado de la misma operación sería:
>  
> | A   | B   |
> |:---:|:---:|
> | 1 | 2 | 
> | 3 | 4 |
> | 1 | 2 | 
> | 1 | 2 | 
> 
> En este caso, es mucho más eficiente la operación porque no hay que comparar cada tupla con el resto de tuplas que hay en la relación para ver si ya existe en la relación. 
> 
> El lenguaje de consulta **SQL**, por cuestiones prácticas, **opera con multiconjuntos** (bags).

### 1.2. Operaciones

El álgebra relacional consta de varios grupos de operaciones
- Operaciones relacionales **unarias**
	- **SELECCIÓN** (símbolo:  $\sigma$ (sigma))
	- **PROYECCIÓN** (símbolo: $\pi$ (pi))
	- **RENOMBRADO** (símbolo: $\rho$ (rho))
- Operaciones binarias de la teoría de conjuntos
	- **UNIÓN** ( $\cup$ )
	- **INTERSECCIÓN** ( $\cap$ )
	- **DIFERENCIA** (o MENOS, - )
	- **PRODUCTO CARTESIANO** ( $\times$ )
- Operaciones relacionales binarias
	- **CONCATENACIÓN** (variaciones de JOIN)
	- **DIVISIÓN**
- Operaciones relacionales adicionales
	- **JOIN EXTERNO** (OUTER JOIN)
	- **Funciones de agregación**: SUMA, CONTAR, PROMEDIO, MÍNIMO, MÁXIMO

###  1.3. Base de datos de ejemplo: EMPRESA

En este tema seguiremos usando como ejemplo ilustrativo [[BD Empresa - Ejemplo completo| la base de datos de Empresa]]; por lo que, es conveniente repasar su esquema y estado.

>[!info]+ Calculadora de Álgebra Relacional
> Para poder practicar con las operaciones del álgebra relacional sobre la base de datos de empresa puedes usar este recurso web:
>  [cálculadora con esquema y estado de EMPRESA](https://dbis-uibk.github.io/relax/calc/gist/5e3b094713c94df48e314477b0b1945b)
>  

---
## 2. Operaciones relacionales unarias

Las operaciones relacionales unarias tienen como operando una sola relación. Y son:
	- SELECCIÓN (SELECT) cuyo símbolo es  $\sigma$ (sigma)
	- PROYECCIÓN (PROJECTION) cuyo símbolo es $\pi$ (pi)
	- RENOMBRADO (RENAME) cuyo símbolo es $\rho$ (rho))

### 2.1. Selección (SELECT)

La operación SELECT se utiliza para **seleccionar un subconjunto de las tuplas (filas) de una relación** según una condición de selección (**partición horizontal**).

> [!math]+ Expresión SELECT
>$$\sigma_{<condición>}(R)$$

Se denota por $\sigma_{<condición>}(R)$ donde
	- el símbolo $\sigma$ (sigma) se utiliza para denotar el operador de selección
	- la condición es una expresión booleana especificada sobre los atributos de la relación $R$
	- Se seleccionan las tuplas que hacen que la condición sea verdadera,
		- aparecen en el resultado de la operación
	- Las tuplas que hacen que la condición sea falsa se filtran,
		- descartadas del resultado de la operación

**Ejemplos:**
	1. Seleccione las tuplas $Empleado$ cuyo número de departamento sea 4:  
	$$\sigma_{Dno  = 4} (Empleado)$$
	2. Seleccione las tuplas de empleados cuyo salario sea mayor a 30,000: 
	$$\sigma_{Sueldo > 30.000} (Empleado)$$
>[!example] Ejemplo en RelaX
> Empleados con número de departamento 4:
> ```
> sigma Dno=4 (Empleado)
> ```
> Empleados con salario mayor a 30000:
> ```
> sigma Sueldo>30000 (Empleado)
> ```
> 
#### Propiedades de la operación SELECT

1. $\sigma_{<condición>}(R)$ **produce una relación $S$ que tiene el mismo esquema** (mismos atributos) que $R$

2. Es **conmutativa**:
$$\sigma_{<condición1>}(\sigma_{<condición2>}(R)) = \sigma_{<condición2>}(\sigma_{<condición1>}(R))$$

3. Una **secuencia** de operaciones SELECT puede ser reemplazada por **una única selección con una conjunción de todas las condiciones**:
$$\sigma_{<cond1>}(\sigma_{<cond2>}(\sigma_{<cond3>}(R))) = \sigma_{<cond1 \land cond2 \land cond3>}(R)$$

4. El **número de tuplas en el resultado** de una SELECT es **menor que (o igual** a) el número de tuplas en la relación de entrada $R$ 

>[!exercise]+ Ejercicio
> Usa una sola operación SELECT para obtener los empleados del departamento 4 que cobren más de 30000. 
> 
> Recuerda usar la  [cálculadora con esquema y estado de EMPRESA](https://dbis-uibk.github.io/relax/calc/gist/5e3b094713c94df48e314477b0b1945b)

#### Representación en SQL
```SQL
/* caso general: sigma<condicion>(R) */

SELECT *
FROM R
WHERE <condicion>;

/* ejemplo: sigma Dno = 4 (Empleado) */

SELECT *
FROM Empleado
WHERE Dno = 4;

```

---

### 2.2. Proyección (PROJECTION)

Esta operación **devuelve ciertas columnas** (atributos) de una relación y descarta las otras.
Crea una **partición vertical**:
	- La lista de columnas especificadas (atributos) se mantiene en cada tupla.
	- Los demás atributos de cada tupla se descartan.

La forma general de la proyección es:
$$\pi_{lista\ de\ atributos} (R)$$

- $\pi$ (pi) es el símbolo utilizado para representar el funcionamiento de la proyección.
- $<lista\ de\ atributos>$ es la lista deseada de atributos de la relación R.

Esta operación **elimina cualquier tupla duplicada**.
	- El **resultado** de la operación del proyección debe ser una **relación** (conjunto de tuplas).
	- Los conjuntos matemáticos no permiten elementos duplicados.

**Ejemplo**. Para enumerar el nombre, apellido y sueldo de cada empleado, se utiliza lo siguiente:
$$\pi_{Apellido1, Nombre, Sueldo} (EMPLEADO)$$

>[!example] Ejemplo en RelaX
> ```
> pi Apellido1,Nombre,Sueldo (Empleado)
> ```
> 
#### Propiedades de la operación PROJECTION

1. El **número de tuplas en el resultado** de la proyección  $\pi_{<lista>}(R)$ siempre es **menor o igual al número de tuplas en $R$**
	1. **Si** la lista de atributos incluye una **clave de $R$**, entonces el número de tuplas en el resultado de la proyección es **igual al número de tuplas en $R$**
2. La proyección no es conmutativa

$$\pi_{<lista1>} ( \pi_{<lista2>} (R) ) = \pi_{<lista1>}(R)$$ 
siempre que $lista2$ contenga los atributos de $lista1$.

#### Representación en SQL

```SQL
/* caso general: pi<atributos>(R) */

SELECT DISTINCT <atributos>
FROM R;

/* ejemplo: pi Apellido1, Nombre, Sueldo (Empleado) */

SELECT DISTINCT Apellido1, Nombre, Sueldo
FROM Empleado;

```

>[!info] Expresiones del álgebra relacional: resultados intermedios
>
> Es posible que queramos aplicar **varias operaciones de álgebra relacional** una tras otra.
>	- Podemos escribir las operaciones como **una única expresión** de álgebra relacional anidando las operaciones, o
>	- podemos aplicar una operación a la vez y **crear relaciones de resultados intermedios**.
>
> En el último caso, debemos **dar nombres** a las relaciones que contienen los resultados intermedios.
> 
> Ejemplo:  para recuperar el nombre, apellido y salario de todos los empleados que trabajan en el departamento número 5, debemos aplicar una operación de selección y una de proyección.
> 
> Opción 1: podemos escribir una única expresión de álgebra relacional de la siguiente manera:
> $$\pi_{Nombre, Apellido1, Sueldo} (\sigma_{DNO=5} (EMPLEADO))$$
> Opción 2: podemos mostrar explícitamente la secuencia de operaciones, dando un nombre a cada relación intermedia:
> 
> $$DEP5\_EMPS \gets \sigma_{DNO=5} (EMPLEADO)$$
> $$RESULTADO \gets \pi_{Nombre, Apellido1, Sueldo} (DEP5\_EMPS)$$
> 
> En la [calculadora de álgebra relacional](https://dbis-uibk.github.io/relax/calc/gist/5e3b094713c94df48e314477b0b1945b), para la asignación se usa el símbolo $=$
> ```
> Dep5_Emps = sigma Dno=5 (Empleado)
> Resultado = pi Nombre, Apellido1, Sueldo (Dep5_Emps)
> Resultado
> ```

>[!exercise]+ Ejercicio
> 1. Crea una expresión en álgebra relacional para obtener el nombre de los empleados del departamento 4 que cobre más de 25000 y los del departamento 5 que cobren más de 30000. 
> 2. Divide la expresión anterior en dos pasos usando resultados intermedios
> 
> Recuerda usar la  [cálculadora con esquema y estado de EMPRESA](https://dbis-uibk.github.io/relax/calc/gist/5e3b094713c94df48e314477b0b1945b)

### 2.3. Renombrado (RENAME)

En algunos casos, es posible que queramos **cambiar el nombre de los atributos de una relación o el nombre de la relación** o ambos.

El operador renombrado se denota por $\rho$ (rho).

| Caso                             | Sintaxis libro                | Sintaxis RelAx               |
| -------------------------------- | ----------------------------- | ---------------------------- |
| Cambiar el nombre de la relación | $\rho_S (R)$                  | rho S (R)                    |
| Cambiar nombre de atributos      | $\rho_{b1,b2, \dots, bn} (R)$ | rho b1 \<- a1, b2 \<- a2 (R) |

>[!example] Ejemplo en RelAx
> ```
> Dep5_emps = sigma Dno=5 (Empleado)
> rho NuevoNombre<-Nombre, NuevoApellido<-Apellido1, NuevoSueldo<-Sueldo (pi Nombre, Apellido1, Sueldo (Dep5_emps))
> ```

#### Representación en SQL

```SQL
/* caso general relación: rho S (R) */

SELECT *
FROM R S;

/* caso general atributos: rho b1<-a1, b2<-a2 ... bn<-an (R) */

SELECT a1 AS b1, a2 AS b2, ... , an AS bn
FROM R;

/* ejemplo: rho NuevoNombre<-Nombre, NuevoApellido<-Apellido1, 
NuevoSueldo<-Sueldo (pi Nombre, Apellido1, Sueldo (Empleado))
*/

SELECT Nombre AS NuevoNombre, Apellido1 AS NuevoApellido, Sueldo AS NuevoSueldo
FROM Empleado;

```

---

## 3. Operaciones de la teoría de conjuntos

### 3.1. Unión, intersección y diferencia

- Son operaciones binarias: dos relaciones como operandos.
- Operaciones binarias de conjuntos:

| Operación      | Sintaxis matemática | Sintaxis RelAx |
| -------------- |:-------------------:| -------------- |
| UNION          |       $\cup$        | union          |
| INTERSECTION   |       $\cap$        | intersect      |
| SET DIFFERENCE |         $–$         | except         |

**Requieren compatibilidad de tipos en los operandos**. $R_1(A_1, A_2, \dots, A_n)$ y $R_2(B_1, B_2, \dots, B_n)$ son compatibles en cuanto a tipos si:
	- tienen el **mismo número** de atributos y
	- los **dominios** de los atributos correspondientes son **compatibles** en cuanto a tipos, es decir, $dom(A_i)=dom(B_i)$ para $i=1, 2, \dots, n$.

Por convención, la relación resultante para $R_1 \cup R_2$ (también para $R_1 \cap R_2$, o $R_1 - R_2$) tiene los mismos nombres de atributos que la primera relación del operando $R_1$.

#### Unión
- Operación **binaria**, denotada por $\cup$
- El resultado de la operación $R \cup S$, es una relación que incluye todas las tuplas que están en $R$ o en $S$ o en $R$ y $S$
- Se **eliminan las tuplas duplicadas**
- Las dos **relaciones** operandos R y S deben ser **compatibles**

#### Intersección
- Operación **binaria**, denotada por $\cap$
- El resultado de la operación $R \cap S$, es una relación que incluye todas las tuplas que están tanto en $R$ como en $S$.
	- Los nombres de los atributos en el resultado serán los mismos que los nombres de los atributos en R
- Las dos **relaciones** operandos R y S deben ser **compatibles**

#### Diferencia

- Operación **binaria**, denotada por -
- El resultado de la operación $R - S$, es una relación que incluye todas las tuplas que están  en $R$ pero no en $S$.
	- Los nombres de los atributos en el resultado serán los mismos que los nombres de los atributos en R
- Las dos **relaciones** operandos R y S deben ser **compatibles**

#### Propiedades de Unión, Intersección y Diferencia

1. Tanto la **unión** como la **intersección** son operaciones **conmutativas**:
$$R \cup S = S \cup R$$  
$$R \cap S = S \cap R$$

2. Tanto la **unión** como la **intersección** son operaciones **asociativas**:
$$R \cup (S \cup T) = (R \cup S) \cup T$$
$$(R \cap S) \cap T = R \cap (S \cap T)$$

3. La operación **diferencia no es conmutativa**; es decir, en general
$$R-S \not= S-R$$

#### Representación en SQL

```sql
/* R union S */

SELECT * FROM R
UNION
SELECT * FROM S;

/* R intersect S */

SELECT * FROM R
INTERSECT
SELECT * FROM S;

/* R except S */

SELECT * FROM R
EXCEPT
SELECT * FROM S;

```

#### Ejemplos

>[!example]+ Ejemplo en RelaX
>Recuperar los DNI de todos los empleados que trabajan en el departamento 5 o supervisan directamente a un empleado que trabaja en el departamento 5
> 
> ```
> Dep5_Emps = sigma Dno=5 (Empleado)
> Resultado1 = pi Dni (Dep5_Emps)
> Resultado2 = pi SuperDni (Dep5_Emps)
> Resultado1 union Resultado2
> ```
> La operación de unión produce las tuplas que están en Resultado1 o Resultado2 o en ambos.

>[!exercise]+ Ejercicio
> 1. Devuelve los nombres y la edad de todas las personas almacenadas en la base de datos, sean empleados o familiares. 
> 2. Devuelve los empleados que son supervisores y tienen más de un familiar.
> 3. Devuelve los empleados que no dirigen un departamento.
> 4. Devuelve los empleados que trabajan en proyectos pero NO tienen familiares registrados y tampoco son supervisores.
> 
> Recuerda usar la  [cálculadora con esquema y estado de EMPRESA](https://dbis-uibk.github.io/relax/calc/gist/5e3b094713c94df48e314477b0b1945b)

### 3.2. Producto Cartesiano 

- Esta operación se utiliza para **combinar tuplas de dos relaciones de forma combinatoria**.
	- Denotado por $R(A_1, A_2, \dots, A_n) x S(B_1, B_2, \dots, B_m)$
- El **resultado** es una **relación Q con grado de atributos n + m**:
$$Q(A_1, A_2, \dots, A_n, B_1, B_2, \dots, B_m)$$, en ese orden.
- El estado de relación resultante tiene **una tupla para cada combinación de tuplas**: una de $R$ y otra de $S$.
	- Por lo tanto, si $R$ tiene $nR$ tuplas (denotadas como $|R| = nR$ ), y $S$ tiene $nS$ tuplas, entonces $R x S$ tendrá $nR * nS$ tuplas.
- **NO** requiere que las relaciones operando sean **compatibles** en cuanto a tipos.

Generalmente, el producto cartesiano **no es una operación significativa**, dado que combina todas las tuplas de ambas relaciones sin ningún criterio.

>[!example] Ejemplo en RelaX
>**Ejemplo no significativo**
>```
> Empleadas = sigma Sexo = ‘M' (Empleado)
> Nom_Empleadas = pi Nombre, Apellido1, Dni (Empleadas)
> Emp_Familiares = Nom_Empleadas x Familiar
> Resultado = pi Nombre, Apellido1, NombSubordinado (Fam_Reales)
>  ```
>  Resultado contendrá cada combinación de Nom_Empleadas y Familiar estén realmente
>  relacionados o no. 
>
> | Empleado.Nombre | Empleado.Apellido1 | Familiar.NombSubordinado |
> | --------------- | ------------------ | ------------------------ |
> | Alicia          | Jimenez            | Alicia                   |
> | Alicia          | Jimenez            | Teodoro                  |
> | Alicia          | Jimenez            | Luisa                    |
> | Alicia          | Jimenez            | Alfonso                  |
> | Alicia          | Jimenez            | Miguel                   |
> | Alicia          | Jimenez            | Elisa                    |
> | Juana           | Sainz              | Alicia                   |
> | Juana           | Sainz              | Teodoro                  |
> | Juana           | Sainz              | Luisa                    |
> | Juana           | Sainz              | Alfonso                  |
> | Juana           | Sainz              | Miguel                   |
> | Juana           | Sainz              | Elisa                    |
> | Aurora          | Oliva              | Alicia                   |
> | Aurora          | Oliva              | Teodoro                  |
> | Aurora          | Oliva              | Luisa                    |
> | Aurora          | Oliva              | Alfonso                  |
> | Aurora          | Oliva              | Miguel                   |
> | Aurora          | Oliva              | Elisa                    |
> 
> Para obtener solo las combinaciones donde el Familiar esté relacionado con el Empleado,
> agregamos una **operación de selección que filtre los no relacionados**
>
>  **Ejemplo significativo** 
>  ```
>  Empleadas = sigma Sexo = 'M' (Empleado)
>  Nom_Empleadas = pi Nombre, Apellido1, Dni (Empleadas)
>  Emp_Familiares = Nom_Empleadas x Familiar
>  Fam_Reales = sigma Dni=DniEmpleado (Emp_Familiares)      -- filtro
>  Resultado = pi Nombre, Apellido1, NombSubordinado (Fam_Reales)
>  ```
>
> El resultado ahora contendrá el nombre de las empleadas y sus familiares
>
> | Empleado.Nombre | Empleado.Apellido1 | Familiar.NombSubordinado |
> | --------------- | ------------------ | ------------------------ |
> | Juana           | Sainz              | Alfonso                  |

>[!note] Aviso
>Puedes revisar el estado de la [[BD Empresa - Estado]] para entender mejor el ejemplo.

#### Representación en SQL

```sql
/* R X S */

SELECT R.*, S.*
FROM R CROSS JOIN S;

```

---

## 4. Operaciones relacionales binarias

### 4.1. JOIN (concatenación)

- La secuencia PRODUCTO CARTESIANO seguida de SELECCIÓN se utiliza con frecuencia para identificar y seleccionar tuplas relacionadas de dos relaciones.
- Una operación especial, llamada JOIN, combina esta secuencia en una sola operación.
- Muy importante para cualquier base de datos relacional con más de una relación, porque nos permite combinar tuplas relacionadas de varias relaciones.
- La forma general de una operación de concatenación en dos relaciones $R(A_1, A_2, \dots, A_n)$ y $S(B_1, B_2, \dots, B_m)$ es:
$$R \Join_{<condición\ de\ unión>} S$$
donde $R$ y $S$ pueden ser cualquier relación que resulte de expresiones del álgebra relacional general.

>[!math]+ Ejemplo: Recuperar el nombre del director de cada departamento.
> Necesitamos combinar cada tupla en Departamento con la tupla de Empleado cuyo valor Dni coincida con el valor DniDirector en la tupla del departamento.
> Podemos hacerlo mediante la operación join.
>
> $$Departamento  \Join_{DniDirector=Dni} Empleado$$
>
> DniDirector=Dni es la condición de concatenación
> Combina cada registro del departamento con el empleado que administra el departamento.

>[!example] Ejemplo en RelaX
>Recuperar el nombre del director de cada departamento
>```
>Temp = Departamento join DniDirector=Dni Empleado
>pi Departamento.NombreDpto, Empleado.Nombre (Temp)
>```
>
> |Departamento|Empleado|
> |---|---|
> |Investigacion|Alberto|
> |Administracion|Juana|
> |Sede-central|Eduardo|
> 
> **Total de registros:** 3

#### Propiedades de JOIN
Dada la siguiente operación JOIN:
$$R(A_1, A_2, \dots, A_n) \Join_{R.A_i=S.B_j} S(B_1, B_2, \dots, B_m)$$
El resultado es una relación Q de grado n + m:
$$Q(A_1, A_2, \dots, A_n, B_1, B_2, \dots, B_m)$$, en ese orden.

- El **estado** de relación resultante tiene **una tupla para cada combinación de tuplas** ($r$ de $R$ y $s$ de $S$), pero solo **si satisfacen la condición de concatenación** $r[Ai]=s[Bj]$.
- Si $R$ tiene $nR$ tuplas y $S$ tiene $nS$ tuplas, generalmente el resultado de la unión tendrá menos de $nR * nS$ tuplas.
- Solo las tuplas relacionadas (según la condición de concatenación) aparecerán en el resultado.

#### Caso general

El caso general de la operación JOIN se denomina Theta-join (la condición de unión se llama $theta$): $$R \Join_{theta} S$$
$Theta$ puede ser cualquier expresión booleana general sobre los atributos de $R$ y $S$; por ejemplo:
$$R.A_i<S.B_j \land (R.A_k=S.B_i \lor R.A_p<S.B_q)$$
La mayoría de las condiciones de concatenación implican una o más condiciones de igualdad unidas mediante el método “AND”; por ejemplo:
$$R.A_i=S.B_j \land R.A_k=S.B_i \land R.A_p=S.B_q$$

#### Casos típicos de JOIN

**EQUIJOIN**
- El uso más común de concatenación implica condiciones de unión con **comparaciones de igualdad** únicamente.
- En el **resultado** de un EQUIJOIN siempre tenemos **uno o más pares de atributos** (cuyos nombres no necesitan ser idénticos) que tienen **valores idénticos en cada tupla**.

**NATURAL JOIN**
- Es una variación de JOIN, indicada por $R * R$, para **eliminar del resultado el segundo atributo (superfluo) en una condición EQUIJOIN**.
- En la calculadora se indica simplemente así: $R \Join R$ o $R\ natural\ join\ S$
- La definición estándar de natural join requiere que los dos **atributos de concatenación**, o cada par de atributos correspondientes, tengan el **mismo nombre en ambas relaciones**.

- Ejemplo general: $$Q \gets R(A,B,C,D) * S(C,D,E) $$
La condición de concatenación implícita incluye cada par de atributos con el mismo nombre, unidos mediante la operación “AND”:
$$R.C=S.C \land R.D=S.D$$
El resultado conserva solo un atributo de cada uno de estos pares:
$$Q(A,B,C,D,E)$$

>[!example] Ejemplo en RelaX
>
>Un natural JOIN sobre los atributos $NumeroDpto$ de $Departamento$ y $Localizaciones\_Dpto$ sería simplemente (Empresa): 
>```
>Departamento join Localizaciones_Dpto
>```
>El único atributo con el mismo nombre es $NumeroDpto$
>La condición de concatenación implícita basada en este atributo sería: 
>```
>Departamento.NumeroDpto = Localizaciones_Dpto.NumeroDpto
>```
>El estado de la relación resultado será:
>
>| Nombre Departamento | Número Dpto | DNI Director | Fecha Ingreso Director | Ubicación Dpto |
>| ------------------- | ----------- | ------------ | ---------------------- | -------------- |
>| Sede-central        | 1           | 888665555    | 19/06/1981             | Madrid         |
>| Administracion      | 4           | 987654321    | 01/01/1995             | Gijón          |
>| Investigacion       | 5           | 333445555    | 22/05/1988             | Valencia       |
>| Investigacion       | 5           | 333445555    | 22/05/1988             | Sevilla        |
>| Investigacion       | 5           | 333445555    | 22/05/1988             | Madrid         |

#### Representación en SQL

```sql
/* R join <condición> S */

SELECT R.*, S.* 
FROM R INNER JOIN S ON (<condición>);

/* R natural join S */

SELECT R.*, S.* 
FROM R NATURAL JOIN S;

SELECT R.*, S.* 
FROM R INNER JOIN S USING (<atributos comunes>);

```

Ejemplos

```sql
/* Equijoin. Departamento join DniDirector=Dni Empleado */

SELECT DEPARTAMENTO.*, EMPLEADO.*
FROM DEPARTAMENTO INNER JOIN EMPLEADO ON (DniDirector=Dni);

/* Natural Join. Proyecto natural join Departamento */

SELECT DEPARTAMENTO.*, LOCALIZACION_DPTO.* 
FROM DEPARTAMENTO NATURAL JOIN LOCALIZACION_DPTO;

SELECT DEPARTAMENTO.*, LOCALIZACION_DPTO.* 
FROM DEPARTAMENTO INNER JOIN LOCALIZACION_DPTO USING(NumeroDpto);

```

>[!note] Conjunto completo
>El conjunto de operaciones que incluye
>- SELECCIÓN $\sigma$ , 
>- PROJECCIÓN $\pi$ , 
>- UNION $\cup$ , 
>- DIFERENCIA $-$ , 
>- RENOMBRADO $\rho$ 
>- y PRODUCTO CARTESIANO $\times$ 
>
>se denomina conjunto completo porque **cualquier otra expresión de álgebra relacional puede expresarse mediante una combinación de estas cinco operaciones**.
>
>Ejemplos:
>
>$$R \cap S = (R \cup S ) – ((R - S) \cup (S - R))$$
>$$R \Join_{<condición\ de\ join>} S = \sigma_{<condición\ de\ join>} (R \times S)$$

### 4.2. Outer Join

En NATURAL JOIN y EQUIJOIN, **se eliminan del resultado las tuplas**:
- **sin una tupla coincidente** (o relacionada), 
- **con nulos en los atributos de concatenación**.

Esto equivale a una **pérdida de información**.

Se puede utilizar un **conjunto de operaciones**, llamadas **OUTER JOIN**, **cuando queremos mantener todas las tuplas** en $R$, o todas aquellas en $S$, o todas aquellas en ambas relaciones en el resultado de la concatenación, independientemente de si tienen o no tuplas coincidentes en la otra relación.

#### LEFT OUTER JOIN
La operación de LEFT OUTER JOIN mantiene cada tupla en la primera relación $R$ en $R\ \unicode{x27D5}\ S$.
- Si no se encuentra ninguna tupla coincidente en $S$, entonces los atributos de $S$ en el resultado de la concatenación se rellenan con valores nulos.
>[!example] Ejemplo en Relax
> Indica para cada empleado el nombre del departamento del que es director. 
> Deben aparecer todos los empleados tanto si son directores como si no lo son. 
>```
>Temp = Empleado left outer join Dni=DniDirector Departamento
>pi Nombre, Apellido1, Apellido2, NombreDpto (Temp)
>```
>
>| Nombre   | Primer Apellido | Segundo Apellido | Departamento   |
>| -------- | --------------- | ---------------- | -------------- |
>| Jose     | Perez           | Perez            | null           |
>| Alberto  | Campos          | Sastre           | Investigacion  |
>| Alicia   | Jimenez         | Celaya           | null           |
>| Juana    | Sainz           | Oreja            | Administracion |
>| Fernando | Ojeda           | Ordonez          | null           |
>| Aurora   | Oliva           | Avovzuela        | null           |
>| Luis     | Pajares         | Morera           | null           |
>| Eduardo  | Ochoa           | Paredes          | Sede-central   |
>

#### RIGHT OUTER JOIN
La operación de RIGHT OUTER JOIN mantiene cada tupla en la segunda relación $R$ en $R\ \unicode{x27D6}\ S$.
- Si no se encuentra ninguna tupla coincidente en $R$, entonces los atributos de $R$ en el resultado de la concatenación se rellenan con valores nulos.
>[!example] Ejemplo en Relax
> Indica para cada familiar el nombre del empleado del que depende. 
> Deben aparecer todos los empleados tanto si tienen familiares como si no. 
>```
>Temp = Familiar right outer join Dni=DniEmpleado  Empleado
pi NombSubordinado, Nombre, Apellido1 (Temp)
>```
>
>|Familiar/Subordinado|Empleado|Apellido|
>|---|---|---|
>|Miguel|Jose|Perez|
>|Alicia|Jose|Perez|
>|Elisa|Jose|Perez|
>|Alicia|Alberto|Campos|
>|Teodoro|Alberto|Campos|
>|Luisa|Alberto|Campos|
>|null|Alicia|Jimenez|
>|Alfonso|Juana|Sainz|
>|null|Fernando|Ojeda|
>|null|Aurora|Oliva|
>|null|Luis|Pajares|
>|null|Eduardo|Ochoa|
>

#### FULL OUTER JOIN
La operación de FULL OUTER JOIN mantiene todas las tuplas en ambas relaciones en $R\ \unicode{x27D7}\ S$.
- Si no se encuentra ninguna tupla coincidente en $R$ o en $S$, entonces los atributos de $R$ o de $S$ en el resultado de la concatenación se rellenan con valores nulos.

>[!error] BD Empresa no tiene datos suficientes
> La BD Enpresa no tiene datos para poder mostrar un ejemplo de este tipo.

#### Representación en SQL

```sql
/* R left outer join (condicion) S */

-- v1
SELECT R.*, S.*
FROM R LEFT OUTER JOIN S ON(CONDICION);

-- v2
SELECT R.*, S.*
FROM R LEFT JOIN S ON(CONDICION);

/* R right outer join (condicion) S */

-- v1
SELECT R.*, S.*
FROM R RIGHT OUTER JOIN S ON(CONDICION);

-- v2
SELECT R.*, S.*
FROM R RIGHT JOIN S ON(CONDICION);

/* R full outer join (condicion) S */

-- v1
SELECT R.*, S.*
FROM R FULL OUTER JOIN S ON(CONDICION);

-- v2
SELECT R.*, S.*
FROM R OUTER JOIN S ON(CONDICION);
```

### 4.3. División

$R(Z) \div S(X)$, donde $X$ es subconjunto de $Z$. 
Sea $Y = Z - X$ (y, por tanto, $Z = X \div Y$); es decir, sea $Y$ el conjunto de atributos de $R$ que no son atributos de $S$.

El resultado de la DIVISIÓN es una relación $T(Y)$ que incluye una tupla $t$ si las tuplas $t_R$ aparecen en $R$ con $t_R [Y] = t$, y con $t_R [X] = t_S$ para cada tupla $t_S$ en $S$.

Para que una tupla $t$ aparezca en el resultado $T$ de la DIVISIÓN, los valores en $t$ deben aparecer en $R$ en combinación con cada tupla en $S$.

>[!example] Ejemplo en RelaX
>```
>DNI_PNOS = pi DniEmpleado,NumProy (Trabaja_En)
>PEREZ_PNOS = pi NumProy (Trabaja_En join DniEmpleado = Dni (sigma Apellido1 = 'Perez' (Empleado)))
>DNI_PNOS division PEREZ_PNOS
>```
>![[BD - Algebra Relacional - Division.png]]

## 5. Operaciones relacionales adicionales
Hay algunos tipos de consulta que no se pueden expresar en el álgebra relacional básica:
- Funciones matemáticas de agregación sobre colecciones de valores
- La agrupación de valores para el cálculo de funciones agregadas
- El cierre recursivo
- Las operaciones Outer Join

### 5.1. Funciones de agregación
En este caso, vamos a considerar las siguientes funciones de agregación (soportadas por RelaX), que también aparecen en SQL:

| función/tipo datos | number | string | date |
| ------------------ | ------ | ------ | ---- |
| COUNT( * )         | yes    | yes    | yes  |
| COUNT( atributo )  | yes    | yes    | yes  |
| MIN( atributo )    | yes    | yes    | yes  |
| MAX( atributo )    | yes    | yes    | yes  |
| SUM( atributo )    | yes    | no     | no   |
| AVG( atributo )    | yes    | no     | no   |

La diferencia entre COUNT( * ) y COUNT(atributo) es que la primera devuelve el número total de tuplas de la relación, independientemente de los valores NULL, mientras que la segunda devuelve el número valores no NULL en el atributo dado.

### 5.2. Operación de agrupación
Permite agrupar los resultados de las funciones de agregación por uno o varios atributos de la relación.

- Se denota por $\gamma$ (gamma)
- Caso general:
$$\gamma <atributos\ agrupación>; <lista\ funciones\ agregación> (R)$$
- En RelaX:
	- Si no se especifican atributos de agrupación, la relación completa es el grupo
	- la lista de funciones de agregación está compuesta por elementos del tipo:
$$ FUNCIÓN(A_i) \to a $$

>[!example] Ejemplo en Relax
>Agrupar a los empleados por Dno (número de departamento) y calcular el número de empleados y el salario promedio por departamento.
>```
> gamma Dno; COUNT(Dni) to dnis, AVG(Sueldo) to sueldos (Empleado)
>```
>| Dno | dnis | sueldos |
>|---|---|---|
>| 1 | 1 | 55000|
>| 4| 3| 31000|
>|5|4|33250|
>

## 6. Expresando restricciones en álgebra relacional
Hasta ahora hemos visto el uso del álgebra relacional para especificar operaciones de consulta sobre los datos, en este apartado veremos otro de sus usos: la especificación de restricciones.

Hay dos maneras de usar expresiones de álgebra relacional para expresar restricciones:

1. Si $R$ es una expresión de álgebra relacional, entonces $R = \emptyset$ es una restricción que dice: "El valor de $R$ debe estar vacío" o, equivalentemente, "No hay tuplas en el resultado de $R$".
2. Si $R$ y $S$ son expresiones de álgebra relacional, entonces $R \subset S$ es una restricción que dice: "Toda tupla en el resultado de $R$ debe estar también en el resultado de $S$". Por supuesto, el resultado de $S$ puede contener tuplas adicionales no producidas por $R$.

Estas formas de expresar restricciones son en realidad equivalentes en lo que pueden expresar, pero a veces una u otra es más clara o concisa. Es decir, la restricción  $R \subset S$ podría haberse escrito perfectamente como $R — S = \emptyset$. Para entender por qué, observa que si toda tupla en $R$ está también en $S$, entonces $R — S$ tiene que estar vacía. Por el contrario, si $R — S$ no contiene tuplas, entonces cada tupla en $R$ debe estar en $S$ (de lo contrario, estaría en $R — S$).

Por otro lado, una restricción de la primera forma, $R = \emptyset$, podría haberse escrito perfectamente como $R \subset \emptyset$. Técnicamente, $\emptyset$ no es una expresión de álgebra relacional, pero dado que existen expresiones que evalúan a $\emptyset$, como $R — R$, no hay inconveniente en usar $\emptyset$ como expresión de álgebra relacional.

### 6.1. Restricciones de integridad referencial
Un tipo común de restricción, denominada restricción de integridad referencial, establece que un valor que aparece en un contexto también aparece en otro contexto relacionado.

Por ejemplo, en nuestra base de datos de Empresa, si una tupla `Familiar` incluye al empleado `123456789` en el atributo `empleado`, esperaríamos que `123456789` apareciera como el dni de alguna tupla de la relación `EMPLEADO`. 

En general, si tenemos cualquier valor $v$ como componente en el atributo $A$ de alguna tupla en una relación $R$, entonces, debido a nuestras intenciones de diseño, podemos esperar que $v$ aparezca en un componente particular (por ejemplo, para el atributo $B$) de alguna tupla de otra relación $S$. Podemos expresar esta restricción de integridad en álgebra relacional como:
$$\pi_A(R) \subseteq \pi_B (S)$$
, o de forma equivalente,
$$\pi_A(R) - \pi_B (S) = \emptyset$$

### 6.2. Restricciones de clave
La misma notación de restricción nos permite expresar mucho más que la integridad referencial. Aquí veremos cómo podemos expresar algebraicamente la restricción de que un determinado atributo o conjunto de atributos es clave para una relación.

Recordemos que en la relación 

$DEPARTAMENTO(nombre, numero, director, fechaIngresoDirector)$, 

el atributo `numero` es la clave primaria. Es decir, no hay dos tuplas que coincidan en el atributo `numero`. Expresaremos algebraicamente una de las implicaciones de esta restricción: si dos tuplas coinciden en el `numero`, también deben coincidir en el `nombre`. Nótese que, de hecho, estas dos tuplas, que coinciden en la clave `numero`, deben ser la misma tupla y, por lo tanto, concuerdan en todos los atributos.

La idea es que si construimos todos los pares de tuplas $DEPARTMENTO$ $(t_1,t_2)$, no debemos encontrar un par que coincida en el componente de `numero` y discrepe en el de `nombre`. Para construir los pares, utilizamos un producto cartesiano y, para buscar pares que violen la condición, utilizamos una selección. Luego, afirmamos la restricción igualando el resultado a  $\emptyset$.

Para empezar, dado que tomamos el producto de una relación consigo misma, necesitamos renombrar al menos una copia para tener nombres para los atributos del producto. Para simplificar, usemos dos nuevos nombres, D1 y D2, para referirnos a la relación $DEPARTAMENTO$. Entonces, el requisito se puede expresar mediante la restricción algebraica:

$$\sigma_{D1.nombre=D2.nombre\ AND\ D1.nombre \neq D2.nombre\ (D1 \times D2) = \emptyset}$$

Teniendo en cuenta que $D1$ en el producto $D1 x D2$ es el nombre destino de la operación de renombrado:
$$\rho_{D1}(Departamento)$$
y $D2$ es otro renombrado similar.

### 6.3. Otras restricciones
Existen muchos otros tipos de restricciones que podemos expresar en álgebra relacional y que son útiles para restringir el contenido de bases de datos. Una amplia gama de restricciones implica los valores permitidos en un dominio. Por ejemplo, el hecho de que cada atributo tenga un tipo restringe los valores de dicho atributo. A menudo, la restricción es bastante sencilla, como "solo enteros" o "cadenas de caracteres de hasta 30 caracteres". En otras ocasiones, queremos que los valores que pueden aparecer en un atributo se restrinjan a un pequeño conjunto enumerado de valores. En otras ocasiones, existen limitaciones complejas sobre los valores que pueden aparecer. Presentaremos dos ejemplos: uno de una restricción de dominio simple para un atributo y el segundo de una restricción más compleja.

En el primer ejemplo, supongamos que deseamos especificar que los únicos valores legales para el atributo `sexo` de $EMPLEADO$ son 'F', 'M' y 'O'. Se puede expresar esta expresión algebraicamente de la siguiente manera:

$$\sigma_{sexo \neq 'F'\ AND\ sexo \neq 'M'\ AND\ sexo \neq 'O'\ (Empleado) = \emptyset} $$

Es decir, el conjunto de tuplas en $EMPLEADO$ cuyo atributo `sexo` no es igual a 'F' ni a 'M' ni a 'O' está vacío.

En el segundo ejemplo, supongamos que deseamos establecer como requisito que un empleado debe ganar más de 30000 para ser director de departamento. Se puede expresar esta expresión algebraicamente de la siguiente manera.

1. Es necesario hacer un theta-join de las relaciones $EMPLEADO$ y $DEPARTAMENTO$ usando la condición de que `dni` y `director` son iguales. De esta manera, combinamos los datos de los directores con los del departamento que dirigen.
2. Seleccionamos las tuplas con `sueldo` mayor a 30000.

$$\sigma_{Sueldo>30000}\ (Empleado \Join_{Dni=DniDirector} Departamento) = \emptyset $$

Una forma alternativa de expresar la misma restricción sería comparar el conjunto de `DniDirector` que representa a los directores de $DEPARTAMENTO$ con el de $EMPLEADOS$ que cobran más de 30000; el primero debe ser un subconjunto del segundo. 

$$\pi_{DniDirector} (Departamento) \subseteq \pi_{Dni} (\sigma_{Sueldo>30000}(Empleado)) $$
