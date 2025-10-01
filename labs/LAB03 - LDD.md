# Lenguaje de Definición de Datos y Operaciones

---

## Objetivos

Dentro del **Lenguaje de Definición de Datos**, veremos:

- CREATE/ALTER/DROP TABLE
- CREATE/DROP VIEW
- CHECK
- PRIMARY KEY
- AUTOINCREMENT (Secuencias)
- UNIQUE
- FOREIGN KEY

Dentro del **Lenguaje de Manipulación de Datos**, veremos las **3 operaciones** de alteración de datos:
- INSERT
- UPDATE
- DELETE

--- 

## Ejercicio 1 - Repaso SELECT

Escribe una consulta que utilice un filtro para calcular simultáneamente las masas corporales (`body_mass_g`) promedio de pingüinos pesados ​​(aquellos que pesan más de 4500 gramos) y pingüinos livianos (aquellos que pesan menos de 3500 gramos), tomando las columnas resultado los nombres `avg_heavy` y `avg_light`respectivamente. Redondea el resultado a 2 decimales.

Solución:
```sql

```

Resultado:

| avg_heavy | avg_light |
| --------- | --------- |
| 5159.13   | 3239.44   |

---
## Borrado de tablas

```sql
-- BD Empresa

-- borrar tablas
drop table if exists empleado;
drop table if exists departamento;

-- consultar el esquema de la base de datos en SQLite
select * from sqlite_schema where type = 'table';
```

Tabla resultado (consulta del esquema):
*(no hay resultados)*

Vamos a empezar borrando las tablas que ya existen en la [BD Empresa en SQL Snippets](https://i3lab.unex.es/sql-snippets/index.html?db=empresa)
- `DROP TABLE` elimina la tabla del esquema y todos sus contenidos
- `IF EXISTS`: evita error si se intenta borrar una tabla que no existe (propio de SQLite)

Ahora ya tenemos una base de datos vacía en SQL Snippets para poder continuar con la lección.

---
## Creación de tablas

```sql
-- EMPLEADO(nombre, apellido1, apellido2, dni, fechaNac, dirección, sexo, sueldo, superDni, numeroDpto )

CREATE TABLE EMPLEADO (	
	nombre TEXT NOT NULL,
	apellido1 TEXT NOT NULL,
	apellido2 TEXT,
	dni TEXT NOT NULL,
	fechaNac TEXT NOT NULL,
	direccion TEXT,
	sexo TEXT NOT NULL,
	sueldo REAL NOT NULL,
	supervisor TEXT,
	dpto INTEGER NOT NULL
) STRICT;

-- DEPARTAMENTO(nombre, numero, director, fechaIngresoDirector)

CREATE TABLE DEPARTAMENTO (
	nombre TEXT NOT NULL,
	numero INTEGER NOT NULL,
	director TEXT NOT NULL,
	fechaIngresoDirector TEXT NOT NULL
) STRICT;

-- consultar el esquema de la base de datos en SQLite
select * from sqlite_schema where type = 'table';
```

Tabla resultado (consulta del esquema):

| type  | name         | tbl_name     | rootpage | sql                                                                                                                                                                                                                                                                                         |
| ----- | ------------ | ------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| table | EMPLEADO     | EMPLEADO     | 2        | CREATE TABLE EMPLEADO ( nombre VARCHAR(40) NOT NULL, apellido1 VARCHAR(30) NOT NULL, apellido2 VARCHAR(30), dni VARCHAR(10) NOT NULL, fechaNac DATE NOT NULL, direccion VARCHAR(150), sexo CHAR(1) NOT NULL, sueldo NUMERIC(12,2) NOT NULL, supervisor VARCHAR(10), dpto INTEGER NOT NULL ) |
| table | DEPARTAMENTO | DEPARTAMENTO | 3        | CREATE TABLE DEPARTAMENTO ( nombre VARCHAR(40) NOT NULL, numero INTEGER NOT NULL, director VARCHAR(10) NOT NULL, fechaIngresoDirector DATE NOT NULL )                                                                                                                                       |

Crea un nombre de tabla seguido de una lista de columnas entre paréntesis:
- Cada columna es un nombre, un tipo de datos y modificadores adicionales
- Por ejemplo, not null evita que se agreguen valores nulos (restricción de dominio)

Algunas adiciones de SQLite no presentes en el estándar de SQL:
- STRICT: activa el control estricto de tipos en SQLite (por defecto, flexible)
	- Tipos permitidos en STRICT: INT, INTEGER, REAL, TEXT, BLOB y ANY.
	- Las columnas `PRIMARY KEY`son implícitamente `NOT NULL`
- CREATE TABLE IF NOT EXISTS: evita error si se intenta crear dos veces la misma tabla

---

## Inserción de datos

```sql
-- INSERCIÓN DE DATOS - TABLA EMPLEADO
INSERT INTO EMPLEADO (nombre, apellido1, apellido2, dni, fechaNac, direccion, sexo, sueldo, supervisor, dpto) VALUES
('José', 'Pérez', 'Pérez', '123456789', '1965-09-01', 'Eloy 1, 98', 'M', 30000, '333445555', 5),
('Alberto', 'Campos', 'Sastre', '333445555', '1955-12-08', 'Avda. Ríos, 9', 'M', 40000, '888665555', 5),
('Alicia', 'Jiménez', 'Celaya', '999887777', '1968-05-12', 'Gran Vía, 38', 'F', 25000, '987654321', 4),
('Juana', 'Sáinz', 'Oreja', '987654321', '1941-06-20', 'Canalejas, 67', 'F', 43000, '888665555', 4),
('Fernando', 'Ojeda', 'Ordóñez', '666884444', '1962-09-15', 'Portillo, s/n', 'M', 38000, '333445555', 5),
('Aurora', 'Oliva', 'Azevuela', '453453453', '1972-07-31', 'Antón, 6', 'F', 25000, '333445555', 5),
('Luis', 'Pajares', 'Morera', '987987987', '1969-03-29', 'Embrujos, 91', 'M', 25000, '987654321', 4),
('Eduardo', 'Ochoa', 'Paredes', '888665555', '1937-11-10', 'Las Peñas, 1', 'M', 55000, NULL, 1);

-- INSERCIÓN DE DATOS - TABLA DEPARTAMENTO
INSERT INTO DEPARTAMENTO VALUES
('Investigación', 5, '333445555', '1988-05-22'),
('Administración', 4, '987654321', '1995-01-01'),
('Sede Central', 1, '888665555', '1981-06-19');
```

La operación `INSERT INTO`permite insertar datos en una tabla.
Si se añaden valores a todas las columnas de la tabla, no se necesita especificar los nombres de las columnas, siempre que se mantenga el orden de definición de las columnas.

Veamos los valores insertados en la tabla EMPLEADO:
```sql
select * from empleado;
```

Tabla resultado:

| nombre   | apellido1 | apellido2 | dni       | fechaNac   | direccion     | sexo | sueldo | supervisor | dpto |
| -------- | --------- | --------- | --------- | ---------- | ------------- | ---- | ------ | ---------- | ---- |
| José     | Pérez     | Pérez     | 123456789 | 1965-09-01 | Eloy 1, 98    | M    | 30000  | 333445555  | 5    |
| Alberto  | Campos    | Sastre    | 333445555 | 1955-12-08 | Avda. Ríos, 9 | M    | 40000  | 888665555  | 5    |
| Alicia   | Jiménez   | Celaya    | 999887777 | 1968-05-12 | Gran Vía, 38  | F    | 25000  | 987654321  | 4    |
| Juana    | Sáinz     | Oreja     | 987654321 | 1941-06-20 | Canalejas, 67 | F    | 43000  | 888665555  | 4    |
| Fernando | Ojeda     | Ordóñez   | 666884444 | 1962-09-15 | Portillo, s/n | M    | 38000  | 333445555  | 5    |
| Aurora   | Oliva     | Azevuela  | 453453453 | 1972-07-31 | Antón, 6      | F    | 25000  | 333445555  | 5    |
| Luis     | Pajares   | Morera    | 987987987 | 1969-03-29 | Embrujos, 91  | M    | 25000  | 987654321  | 4    |
| Eduardo  | Ochoa     | Paredes   | 888665555 | 1937-11-10 | Las Peñas, 1  | M    | 55000  |            | 1    |

Veamos los valores insertados en la tabla DEPARTAMENTO:
```sql
select * from departamento;
```

Tabla resultado:

| nombre         | numero | director  | fechaIngresoDirector |
| -------------- | ------ | --------- | -------------------- |
| Investigación  | 5      | 333445555 | 1988-05-22           |
| Administración | 4      | 987654321 | 1995-01-01           |
| Sede Central   | 1      | 888665555 | 1981-06-19           |

---

## Ejercicio 2 - Crear tabla e insertar datos

1. Crea la  tabla `FAMILIAR`, según el esquema de relación siguiente:

$FAMILIAR$(empleado, nombre, sexo, fechaNac, relación\*)

2. Inserta los datos de la siguiente tabla:

| empleado  | nombre  | sexo | fechaNac   | relacion |
| --------- | ------- | ---- | ---------- | -------- |
| 333445555 | Alicia  | F    | 1986-04-06 | Hija     |
| 333445555 | Teodoro | M    | 1983-10-25 | Hijo     |
| 333445555 | Luisa   | F    | 1958-05-03 | Esposa   |
| 987654321 | Alfonso | M    | 1942-02-28 | Esposo   |
| 123456789 | Miguel  | M    | 1988-01-04 | Hijo     |
| 123456789 | Alice   | F    | 1988-12-30 | Hija     |
| 123456789 | Elisa   | F    | 1967-05-05 | Esposa   |

3. Escribe una consulta que devuelva todos los familiares hombre.

Solución:
```sql

```

Tabla resultado:

| empleado  | nombre  | sexo | fechaNac   | relacion |
| --------- | ------- | ---- | ---------- | -------- |
| 333445555 | Teodoro | M    | 1983-10-25 | Hijo     |
| 987654321 | Alfonso | M    | 1942-02-28 | Esposo   |
| 123456789 | Miguel  | M    | 1988-01-04 | Hijo     |

---

## Restricciones de dominio

```sql
-- borra tabla empleado
drop table if exists empleado;

CREATE TABLE EMPLEADO (	
	nombre TEXT NOT NULL,
	apellido1 TEXT NOT NULL,
	apellido2 TEXT,
	dni TEXT NOT NULL,
	fechaNac TEXT NOT NULL,
	direccion TEXT,
	sexo TEXT NOT NULL CHECK (sexo IN ('M', 'F', 'O')), -- enumerado
	sueldo REAL NOT NULL CHECK (sueldo > 0), -- subrango
	supervisor TEXT,
	dpto INTEGER NOT NULL
) STRICT;
```

`CHECK` permite agregar restricciones de dominio a una de las columnas de la tabla
- debe producir un resultado booleano
- se ejecuta cada vez que se agregan o modifican valores

Si intentamos insertar una fila que viola alguna de estas restricciones, el SGBDR no nos dejará y devolverá un error.

```sql
INSERT INTO EMPLEADO (nombre, apellido1, apellido2, dni, fechaNac, direccion, sexo, sueldo, supervisor, dpto) VALUES
('José', 'Pérez', 'Pérez', '123456789', '1965-09-01', 'Eloy 1, 98', 'Hombre', 30000, '333445555', 5);
```

Tabla resultado:
**CHECK constraint failed: sexo IN ('M', 'F', 'O')**

---

## Ejercicio 3 - Restricciones de dominio

Reescribe la definición de la tabla de `FAMILIAR` para agregar las siguientes restricciones de dominio:

- la columna `sexo` debe contener uno de los siguientes valores 'F', 'M' o 'O'.
- la columna `relacion`debe contener uno de los siguientes valores 'Hijo/a', 'Conyuge' o 'Hermano/a'.

Solución
```sql

```

Para verificar tu respuesta, selecciona su definición de la tabla sqlite_schema mediante la siguiente consulta:

```sql
SELECT 
	Lower(tbl_name) as name, 
	Lower(REPLACE(REPLACE(REPLACE(REPLACE(sql, ' ', ''), CHAR(9), ''), CHAR(10), ''), CHAR(13), '')) as sql
from sqlite_schema where type='table' and Lower(name)='familiar';
```

Tabla resultado:

| name     | sql                                                                                                                                                                                                       |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| familiar | createtablefamiliar(empleadotextnotnull,nombretextnotnull,sexotextnotnullcheck(sexoin('m','f','o')),fechanactextnotnull,relaciontextcheck(relacionin('hijo','hija','conyuge','hermano','hermana')))strict |

---

## Borrado de filas

```sql
-- borra filas de la tabla que cumplen la condición
delete from departamento
where nombre like '%ión';

select * from departamento;
```

Tabla resultado:

| nombre       | numero | director  | fechaIngresoDirector |
| ------------ | ------ | --------- | -------------------- |
| Sede Central | 1      | 888665555 | 1981-06-19           |

`DELETE FROM` borra **filas** de una tabla
- (Casi) siempre tendrás que especificar las filas a eliminar usando la cláusula `WHERE`
- Si no pones `WHERE`, se eliminarán todos los datos de la tabla

---

## Clave primaria: motivación

```sql
-- borrar todos las filas de la tabla
delete from departamento;

insert into DEPARTAMENTO (nombre, numero, director, fechaIngresoDirector) values
('Investigación', 5, '333445555', '1988-05-22'),
('Investigación', 5, '333445555', '1988-05-22');

select * from departamento;
```

Tabla resultado:

| nombre | apellido1 | apellido2 | dni       | fechaNac   | direccion  | sexo | sueldo | supervisor | dpto |
| ------ | --------- | --------- | --------- | ---------- | ---------- | ---- | ------ | ---------- | ---- |
| José   | Pérez     | Pérez     | 123456789 | 1965-09-01 | Eloy 1, 98 | M    | 30000  | 333445555  | 5    |
| José   | Pérez     | Pérez     | 123456789 | 1965-09-01 | Eloy 1, 98 | M    | 30000  | 333445555  | 5    |

En nuestra definición de tablas actual, se pueden **duplicar filas**, porque no hemos definido **ninguna forma de identificar una fila** del resto y, en ese caso, SQL permite filas repetidas.

---

## Clave primaria: definición

```sql
-- borrar tabla depatamento
drop table if exists departamento;

-- PK formada por una sola columna

CREATE TABLE DEPARTAMENTO (
	nombre TEXT UNIQUE NOT NULL, -- UNIQUE para clave alternativa
	numero INTEGER PRIMARY KEY NOT NULL, -- PRIMARY KEY implica NOT NULL
	director TEXT,
	fechaIngresoDirector TEXT NOT NULL -- omitida antes para reducir número casos
) STRICT;

-- PK formada por varias columnas

CREATE TABLE LOCALIZACIONES_DPTO (
	dpto INTEGER NOT NULL,
	ubicacion TEXT NOT NULL,
	PRIMARY KEY (dpto, ubicacion) -- PRIMARY KEY implica NOT NULL
) STRICT;
```

En SQL podemos indicar la restricción de clave primaria mediante las palabras reservadas `PRIMARY KEY` y en el resto de las claves candidatas deben usarse el modificador `UNIQUE` para restringir que en esa columna pueda haber valores repetidos.

`PRIMARY KEY` identifica de forma única una fila concreta en una tabla concreta.

Se puede utilizar cualquier campo (o combinación de campos) de una tabla como clave primaria siempre que los valores sean únicos para cada fila.

```sql
-- borrar todos las filas de la tabla
insert into DEPARTAMENTO (nombre, numero, director, fechaIngresoDirector) values
('Investigación', 5, '333445555', '1988-05-22'),
('Investigación', 5, '333445555', '1988-05-22');
```

Tabla resultado:
**UNIQUE constraint failed: DEPARTAMENTO.numero**

Ahora el SGBDR comprueba la unicidad del valor de la columna clave primaria y, por lo tanto, ya no podremos insertar filas que contengan un valor de clave primaria que ya exista en la tabla.

---
## Clave primaria sustituta
(surrogate key)

```sql
-- borrar tabla depatamento
drop table if exists departamento;

-- PK formada por una sola columna

CREATE TABLE DEPARTAMENTO (
	nombre TEXT UNIQUE NOT NULL, -- UNIQUE para clave alternativa
	numero INTEGER PRIMARY KEY AUTOINCREMENT, -- PRIMARY KEY implica NOT NULL
	director TEXT,
	fechaIngresoDirector TEXT NOT NULL -- omitida antes para reducir número casos
) STRICT;

```

Clave primaria sustituta: 
- no tiene significado en el dominio
- su única función es identificar a la fila

`AUTOINCREMENT` La base de datos incrementa automáticamente el valor de la columna `numero` cada vez que se agrega una nueva fila.
- Común en claves principales: proporciona un valor único para cada fila
- Con una clave primaria autoincremental, no le damos valores en la inserción.

```sql
INSERT INTO DEPARTAMENTO (nombre, director, fechaIngresoDirector) VALUES
('Sede Central', '888665555', '1981-06-19'),
('Administración', '987654321', '1995-01-01'),
('Investigación', '333445555', '1988-05-22');

select * from departamento;
```

Tabla resultado:

| nombre         | numero | director  | fechaIngresoDirector |
| -------------- | ------ | --------- | -------------------- |
| Sede Central   | 1      | 888665555 | 1981-06-19           |
| Administración | 2      | 987654321 | 1995-01-01           |
| Investigación  | 3      | 333445555 | 1988-05-22           |

---

## Secuencias

```sql
select * from sqlite_sequence;
```

Tabla resultado:

| name         | seq |
| ------------ | --- |
| DEPARTAMENTO | 3   |

La tabla `sqlite_sequence` se crea automáticamente, si aún no existe, siempre que se crea una tabla normal que contiene una columna `AUTOINCREMENT`.
- la columna `name` contiene el nombre la tabla a la que corresponde la secuencia
- la columna `seq` contiene el último valor usado en la secuencia

Las **secuencias son otro de los componentes fundamentales** de una base de datos relacional.

Los números de secuencia (o secuencias) no se restablecen cuando se eliminan filas para que puedan usarse como valores de claves primarias.

---

## Ejercicio 4 - Definir clave primaria

Revisa la definición de la tabla `EMPLEADO` para indicar cuál es su clave primaria y sus claves alternativas, si tiene, según el esquema de relación siguiente:

$EMPLEADO$(nombre, apellido1, apellido2, <u>dni</u>, fechaNac, dirección, sexo, sueldo, supervisor, dpto )

En caso de ser una sola columna, crea una restricción a nivel de columna.

Solución:
```sql

```

Para verificar tu respuesta, selecciona su definición de la tabla sqlite_schema mediante la siguiente consulta:

```sql
SELECT 
	Lower(tbl_name) as name, 
	Lower(REPLACE(REPLACE(REPLACE(REPLACE(sql, ' ', ''), CHAR(9), ''), CHAR(10), ''), CHAR(13), '')) as sql
from sqlite_schema where type='table' and Lower(name)='empleado';
```

Tabla resultado:

| name     | sql                                                                                                                                                                                                            | 
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| empleado | createtableempleado(nombretextnotnull,apellido1textnotnull,apellido2text,dnitextprimarykeynotnull,fechanactextnotnull,direcciontext,sexotextnotnull,sueldorealnotnull,supervisortext,dptointegernotnull)strict |

---
## Clave externa: motivación

```sql
INSERT INTO EMPLEADO (nombre, apellido1, apellido2, dni, fechaNac, direccion, sexo, sueldo, supervisor, dpto) VALUES
('José', 'Pérez', 'Pérez', '123456789', '1965-09-01', 'Eloy 1, 98', 'M', 30000, '333445555', 5),
('Luis', 'Pajares', 'Morera', '987987987', '1969-03-29', 'Embrujos, 91', 'M', 25000, '987654321', 4),
('Eduardo', 'Ochoa', 'Paredes', '888665555', '1937-11-10', 'Las Peñas, 1', 'M', 55000, NULL, 1);

select * from empleado;
```

Tabla resultado:

| nombre   | apellido1 | apellido2 | dni       | fechaNac   | direccion     | sexo | sueldo | supervisor | dpto |
| -------- | --------- | --------- | --------- | ---------- | ------------- | ---- | ------ | ---------- | ---- |
| José     | Pérez     | Pérez     | 123456789 | 1965-09-01 | Eloy 1, 98    | M    | 30000  | 333445555  | 5    |
| Luis     | Pajares   | Morera    | 987987987 | 1969-03-29 | Embrujos, 91  | M    | 25000  | 987654321  | 4    |
| Eduardo  | Ochoa     | Paredes   | 888665555 | 1937-11-10 | Las Peñas, 1  | M    | 55000  |            | 1    |

En este momento el contenido de la tabla `DEPARTAMENTO` es:

| nombre         | numero | director  | fechaIngresoDirector |
| -------------- | ------ | --------- | -------------------- |
| Sede Central   | 1      | 888665555 | 1981-06-19           |
| Administración | 2      | 987654321 | 1995-01-01           |
| Investigación  | 3      | 333445555 | 1988-05-22           |

Por lo tanto, **prácticamente todos las filas de `EMPLEADO` tienen un valor inválido en la columna `dpto`**, porque no existen los departamentos con `numero` 4 o 5.

En nuestra definición de tablas actual, se pueden insertar empleados con valores en la columna `dpto` inexistentes en la tabla `DEPARTAMENTO`. Por lo tanto, nuestra base de datos estaría en un estado no válido; ya que se ha perdido la consistencia de los datos. Para evitar estas situaciones se deben definir **restricciones de clave externa** `FOREIGN KEY`.

---

## Clave externa: definición

```sql
drop table if exists empleado;

CREATE TABLE EMPLEADO (	
	nombre TEXT NOT NULL,
	apellido1 TEXT NOT NULL,
	apellido2 TEXT,
	dni TEXT PRIMARY KEY NOT NULL,
	fechaNac TEXT NOT NULL,
	direccion TEXT,
	sexo TEXT NOT NULL,
	sueldo REAL NOT NULL,
	supervisor TEXT,
	dpto INTEGER NOT NULL,
	FOREIGN KEY (supervisor) REFERENCES EMPLEADO (dni),
	FOREIGN KEY (dpto) REFERENCES DEPARTAMENTO (numero)
) STRICT;
```

`FOREIGN KEY` permite establecer una restricción de clave externa:
	- La columna `dpto` de la tabla `EMPLEADO` referencia (`REFERENCES`) 
	- a la columna `numero` de la tabla `DEPARTAMENTO`

Una clave externa es un **valor** en una tabla que **identifica** una **clave primaria** en **otra tabla**.

Se utiliza una **clave externa para garantizar la integridad referencial** en la base de datos. La integridad referencial garantiza que la relación entre las tablas sea válida.

Por tanto, **constituye una parte fundamental de una base de datos relacional**.

---
## Activar integridad referencial en SQLite

```sql
PRAGMA foreign_keys = ON;
PRAGMA foreign_keys;      -- resultado: 1
```

Tabla resultado:

| foreign_keys |
|--------------|
|         1    |

En SQLite puede ser necesario activar el soporte de clave externa o integridad referencial.

- `PRAGMA foreign_keys` permite consultar si el soporte está activo (1) o no (0).
- `PRAGMA foreign_keys = ON` activa el soporte
- `PRAGMA foreign_keys = OFF` desactiva el soporte

En algunas ocasiones puede ser necesario desactivarlo. Por ejemplo, cuando se van a insertar datos en dos tablas que se referencian con claves externas recíprocamente, como el caso de las tablas `EMPLEADO` y `DEPARTAMENTO`, en las que tenemos `dpto` y `director` como claves externas.

---
## Claves externas: funcionamiento

```sql
INSERT INTO EMPLEADO (nombre, apellido1, apellido2, dni, fechaNac, direccion, sexo, sueldo, supervisor, dpto) VALUES
('José', 'Pérez', 'Pérez', '123456789', '1965-09-01', 'Eloy 1, 98', 'M', 30000, '333445555', 5),
('Luis', 'Pajares', 'Morera', '987987987', '1969-03-29', 'Embrujos, 91', 'M', 25000, '987654321', 4),
('Eduardo', 'Ochoa', 'Paredes', '888665555', '1937-11-10', 'Las Peñas, 1', 'M', 55000, NULL, 1);

select * from empleado;
```

Tabla resultado:
**FOREIGN KEY constraint failed**

Ahora, al tener definida una clave externa en la columna dpto, el SGBDR rechazará la inserción de filas cuya columna `dpto` no contenga un valor válido según la integridad referencial (exista como clave primaria en `DEPARTAMENTO`).

---
## Clave externa: borrado

```sql
-- inserción de datos válidos en empleado
INSERT INTO EMPLEADO (nombre, apellido1, apellido2, dni, fechaNac, direccion, sexo, sueldo, supervisor, dpto) VALUES
('Alberto', 'Campos', 'Sastre', '333445555', '1955-12-08', 'Avda. Ríos, 9', 'M', 40000, '888665555', 2),
('Juana', 'Sáinz', 'Oreja', '987654321', '1941-06-20', 'Canalejas, 67', 'F', 43000, '888665555', 2),
('Eduardo', 'Ochoa', 'Paredes', '888665555', '1937-11-10', 'Las Peñas, 1', 'M', 55000, NULL, 1);

-- borrado de datos en departamento que viola FK
delete from departamento where Lower(nombre) like 'adm%';
```

Tabla resultado:
**FOREIGN KEY constraint failed**

Al haber definido una clave externa en `EMPLEADO` que referencia a `DEPARTAMENTO`, el comportamiento por defecto del SGBDR es `NO ACTION` y, por lo tanto, no permite borrar filas de departamento cuyo valor de clave primaria esté siendo usado como valor de la clave externa `dpto` en la tabla `EMPLEADO`. 

```sql
-- borrado de datos en departamento que no viola FK
delete from departamento where Lower(nombre) like 'inv%';

select * from departamento;
```

Tabla resultado:

| nombre         | numero | director  | fechaIngresoDirector |
| -------------- | ------ | --------- | -------------------- |
| Sede Central   | 1      | 888665555 | 1981-06-19           |
| Administración | 2      | 987654321 | 1995-01-01           |

En este caso, sí se realiza la operación de borrado porque no hay ninguna fila en `EMPLEADO` que contenga el valor 3 en su columna FK `dpto`.

---
## Clave externa: actualización

```sql
-- borrado de datos en departamento que viola FK
update departamento 
set numero = 4
where Lower(nombre) like 'adm%';
```

Tabla resultado:
**FOREIGN KEY constraint failed**

Al haber definido una clave externa en `EMPLEADO` que referencia a `DEPARTAMENTO`, el comportamiento por defecto del SGBDR es `NO ACTION` y, por lo tanto, no permite actualizar la clave primaria de las filas de departamento cuyo valor esté siendo usado como valor de la clave externa `dpto` en la tabla `EMPLEADO`. 

`UPDATE` permite actualizar columnas concretas de una colección de filas de una tabla.
- (Casi) siempre tendrás que especificar las filas a modificar usando la cláusula `WHERE`
- Si no pones `WHERE`, se actualizarán todas las filas de la tabla

---
## Clave externa: acciones para integridad referencial

```sql
-- borra tabla empleado
drop table if exists empleado;

CREATE TABLE EMPLEADO (	
	nombre TEXT NOT NULL,
	apellido1 TEXT NOT NULL,
	apellido2 TEXT,
	dni TEXT NOT NULL,
	fechaNac TEXT NOT NULL,
	direccion TEXT,
	sexo TEXT NOT NULL CHECK (sexo IN ('M', 'F', 'O')),
	sueldo REAL NOT NULL CHECK (sueldo > 0),
	supervisor TEXT,
	dpto INTEGER DEFAULT 1 NOT NULL,  -- El departamento 1 siempre existe
	PRIMARY KEY (dni),
	FOREIGN KEY (supervisor) REFERENCES EMPLEADO (dni) ON UPDATE CASCADE ON DELETE SET NULL,
	FOREIGN KEY (dpto) REFERENCES DEPARTAMENTO (numero) ON UPDATE CASCADE ON DELETE SET DEFAULT
) STRICT;
```

En este caso, para la clave externa `dpto`, se ha decidido que sus valores:
- se actualicen en cascada en el caso de que cambien las claves primarias referenciadas (`ON UPDATE CASCADE`)
- se ponga el valor por defecto 1 en caso de que se borren las filas con claves primarias referenciadas (`ON DELETE SET DEFAULT`)

Las acciones disponibles para mantener la integridad referencial son: 
- **Cancelar la operación** que causa la infracción. Diferentes opciones: 
		- `NO ACTION`: permite la operación y verifica al final la integridad. Si hay violación, se deshacen los cambios. **Opción por defecto** en muchos SGBD.
		- `RESTRICT`: rechaza la operación si se voila la integridad. Se verifica antes de realizar la operación.
- Lanzar **actualizaciones adicionales** para corregir la infracción. Diferentes opciones:
		- `CASCADE`: propaga la modificación a otras tablas (lo vemos más adelante)
		- `SET NULL`: pone a NULL una clave externa si su clave primaria referenciada se ve modificada. La clave externa debe admitir valores NULL.
		- `SET DEFAULT`: pone el valor por defecto en una clave externa si su clave primaria referenciada se ve modificada. El valor por defecto debe estar especificado.

Se tienen indican las acciones a realizar en caso de que:
- `ON UPDATE`, se actualicen los valores de las claves primarias referenciadas
- `ON DELETE`, se eliminen filas con los valores de las claves primarias referenciadas

---
## Clave externa: acciones para integridad referencial (update)

```sql
-- actualización de datos en departamento que viola FK
update departamento 
set numero = 4
where Lower(nombre) like 'adm%';

select * from empleado;
```

Tabla resultado:

| nombre  | apellido1 | apellido2 | dni       | fechaNac   | direccion     | sexo | sueldo | supervisor | dpto |
| ------- | --------- | --------- | --------- | ---------- | ------------- | ---- | ------ | ---------- | ---- |
| Alberto | Campos    | Sastre    | 333445555 | 1955-12-08 | Avda. Ríos, 9 | M    | 40000  | 888665555  | 4    |
| Juana   | Sáinz     | Oreja     | 987654321 | 1941-06-20 | Canalejas, 67 | F    | 43000  | 888665555  | 4    |
| Eduardo | Ochoa     | Paredes   | 888665555 | 1937-11-10 | Las Peñas, 1  | M    | 55000  |            | 1    |

La modificación de la clave primaria (el valor 2 se ha modificado a 4) en la tabla de `DEPARTAMENTO` se ha reproducido en cascada en la columna `dpto` de la tabla `EMPLEADO.

---
## Clave externa: acciones para integridad referencial (delete)

```sql
-- borrado de datos en departamento que viola FK
delete from departamento where Lower(nombre) like 'adm%';

select nombre, dni, dpto from empleado;
```

Tabla resultado:

| nombre  | dni       | dpto |
| ------- | --------- | ---- |
| Alberto | 333445555 | 1    |
| Juana   | 987654321 | 1    |
| Eduardo | 888665555 | 1    |

El borrado de la fila con clave primaria 4 en la tabla de `DEPARTAMENTO` ocasiona una violación de la restricción de integridad de la clave externa `dpto` en `EMPLEADO` que se ha solucionado poniendo el valor por defecto 1 en esa columna.

---
## Ejercicio 5 - Clave externa

Reescribe la definición de la tabla de `FAMILIAR` para agregar su clave primaria y clave externa, según el siguiente esquema de relación (mantén todos los cambios realizados hasta ahora en esa tabla):

 $FAMILIAR$(<u>empleado, nombre</u>, sexo, fechaNac, relación)
	- Clave primaria (PK): `{empleado, nombre}`
	- Claves externas (FK): 
		- `{empleado}` referencia al atributo `dni` de la relación $EMPLEADO$. Indica el empleado del que es pariente M:C B:C 

Solución
```sql

```

Para verificar tu respuesta, selecciona su definición de la tabla sqlite_schema mediante la siguiente consulta:

```sql
SELECT 
	Lower(tbl_name) as name, 
	Lower(REPLACE(REPLACE(REPLACE(REPLACE(sql, ' ', ''), CHAR(9), ''), CHAR(10), ''), CHAR(13), '')) as sql
from sqlite_schema where type='table' and Lower(name)='familiar';
```

Tabla resultado:

| name     | sql                                                                                                                                                                                                                                                                                                | 
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| familiar | createtablefamiliar(empleadotextnotnull,nombretextnotnull,sexotextnotnullcheck(sexoin('m','f','o')),fechanactextnotnull,relaciontextcheck(relacionin('hijo/a','conyuge','hermano/a')),primarykey(empleado,nombre),foreignkey(empleado)referencesempleado(dni)onupdatecascadeondeletecascade)strict |

---
## Tipos de asociaciones entre tablas

Considerando su **cardinalidad**, podemos hablar de tres tipos de asociaciones:
- **1:1 (uno-a-uno)**. Asocian una fila de una tabla con una fila de otra.
- **1:N (uno-a-muchos)**. Asocian una fila de una tabla con muchas filas de otra.
- **N:M (muchos-a-muchos)**. Asocian muchas filas de una tabla con muchas filas de otra. Se implementan con una tabla JOIN.

Además, podemos considerar también si todas las filas de una tabla están obligadas o no a estar asociadas a una o más filas de otra. Se conoce como la **opcionalidad** de participar o no en la relación.

Básicamente, en SQL podemos usar la clave externa (FK) para indicar cardinalidad y opcionalidad de la siguiente manera:

|        | participación |             | cardinalidad |        |
| ------ | ------------- | ----------- | ------------ | ------ |
|        | opcional      | obligatoria | uno          | muchos |
| **FK** |               | NOT NULL    | UNIQUE       |        |

---
## **1:1 (Uno-a-Uno)**

```sql
CREATE TABLE DEPARTAMENTO (
	nombre TEXT UNIQUE NOT NULL,
	numero INTEGER NOT NULL,
	director TEXT UNIQUE NOT NULL,       -- EMPLEADO[0..1] -> DEPARTAMENTO[1..1] 
	fechaIngresoDirector TEXT NOT NULL,
	PRIMARY KEY (numero),
	FOREIGN KEY (director) REFERENCES EMPLEADO(dni) ON UPDATE CASCADE ON DELETE SET NULL
) STRICT;
```

En este caso, la clave externa `director` representa la asociación uno-a-uno entre `EMPLEADO` y `DEPARTAMENTO` que establece que 1 empleado puede dirigir 0 o 1 departamento y que un departamento tiene que estar dirigido por 1 empleado. 

Asociación uno-a-uno en SQL:
- **Obligatorio**: FK con `NOT NULL UNIQUE`
- **Opcional**: FK con `UNIQUE` (sin NOT NULL)
- La FK va normalmente en la entidad *menos importante*

---

## **1:N (Uno-a-Muchos)**

```sql
CREATE TABLE EMPLEADO (	
	nombre TEXT NOT NULL,
	apellido1 TEXT NOT NULL,
	apellido2 TEXT,
	dni TEXT NOT NULL,
	fechaNac TEXT NOT NULL,
	direccion TEXT,
	sexo TEXT NOT NULL CHECK (sexo IN ('M', 'F', 'O')),
	sueldo REAL NOT NULL CHECK (sueldo > 0),
	supervisor TEXT,
	dpto INTEGER DEFAULT 1 NOT NULL,    -- DEPARTAMENTO[1..1] -> EMPLEADO[0..N]
	PRIMARY KEY (dni),
	FOREIGN KEY (supervisor) REFERENCES EMPLEADO (dni) ON UPDATE CASCADE ON DELETE SET NULL,
	FOREIGN KEY (dpto) REFERENCES DEPARTAMENTO (numero) ON UPDATE CASCADE ON DELETE SET DEFAULT
) STRICT;
```

En este caso, la clave externa `dpto` representa la asociación uno-a-muchos entre `DEPARTAMENTO` y `EMPLEADO`  que establece que 1 empleado pertenece a 1 departamento y que 1 departamento puede tener muchos empleados, aunque también puede no tener ninguno. En definitiva, la participación de `EMPLEADO` en la relación es obligatoria.

Asociación uno-a-muchos en SQL:
- **Obligatorio**: FK con `NOT NULL` en el lado "muchos"
- **Opcional**: FK sin `NOT NULL` en el lado "muchos"
- La FK siempre va en el lado "muchos"

---
## Modificar las columnas de una tabla

```sql
CREATE TABLE IF NOT EXISTS LOCALIZACIONES_DPTO (
    dpto INTEGER NOT NULL,
    ubicacion TEXT NOT NULL,
    PRIMARY KEY (dpto, ubicacion) -- PRIMARY KEY implica NOT NULL
) STRICT;

-- añadir una nueva columna a una tabla
ALTER TABLE localizaciones_dpto
ADD valoracion integer not null default 0 check (valoracion > -1 and valoracion <6);

select name, sql from sqlite_schema where type='table' and name like 'local%';
```

Tabla resultado:

| name                | sql                                                                                                                                                                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| LOCALIZACIONES_DPTO | CREATE TABLE LOCALIZACIONES_DPTO ( dpto INTEGER NOT NULL, ubicacion TEXT NOT NULL, valoracion integer not null default 0 check (valoracion > -1 and valoracion <6), PRIMARY KEY (dpto, ubicacion) -- PRIMARY KEY implica NOT NULL ) STRICT |

En SQLite, `ALTER TABLE` permite modificar las columnas de una tabla:
- `ADD column` añade una nueva columna al final.
- `DROP column` elimina una columna de la tabla, excepto si es PK o UNIQUE
- `RENAME column` renombra una columna
- Como no puede contener valores nulos, tenemos que proporcionar un valor predeterminado (default)

Luego, usa update para modificar los registros existentes
- Puedes modificar cualquier cantidad de registros a la vez
- Ten cuidado con la cláusula where

---

## Renombrar una tabla

```sql
--renombramos tabla de copia de seguridad
ALTER TABLE LOCALIZACIONES_DPTO rename to UBICACION;

select name, sql from sqlite_schema where type='table' and name like 'ubi%';
```

Tabla resultado:

| name      | sql                                                                                                                        | 
| --------- | -------------------------------------------------------------------------------------------------------------------------- |
| ubicacion | createtable"ubicacion"(dptointegernotnull,ubicaciontextnotnull,primarykey(dpto,ubicacion)--primarykeyimplicanotnull)strict |

En SQLite, `ALTER TABLE` permite modificar de una tabla:
- `RENAME TO` cambia el nombre de la tabla

Funcionamiento:
- SQLite almacena el esquema en la tabla sqlite_schema como el texto original de las instrucciones CREATE que definen el esquema. 
- Por lo tanto, ALTER TABLE necesita revisar el texto de la instrucción CREATE. 

La mayoría de los motores de base de datos SQL almacenan el esquema en varias tablas del sistema. ALTER TABLE solo tiene que realizar modificaciones en las tablas del sistema correspondientes.

[Sentencia ALTER TABLE en SQLite](https://www.sqlite.org/lang_altertable.html)

---

## Ejercicio 6 - ALTER TABLE

Elimina la columna `valoracion` de la tabla `UBICACION`  (anteriormente, `LOCALIZACIONES_DPTO`).

Solución:
```sql

```

Para verificar tu respuesta, selecciona su definición de la tabla sqlite_schema mediante la siguiente consulta:

```sql
SELECT 
	Lower(tbl_name) as name, 
	Lower(REPLACE(REPLACE(REPLACE(REPLACE(sql, ' ', ''), CHAR(9), ''), CHAR(10), ''), CHAR(13), '')) as sql
from sqlite_schema where type='table' and Lower(name)='ubicacion';
```

Tabla resultado:

| name      | sql                                                                                                                        |
| --------- | -------------------------------------------------------------------------------------------------------------------------- |
| ubicacion | createtable"ubicacion"(dptointegernotnull,ubicaciontextnotnull,primarykey(dpto,ubicacion)--primarykeyimplicanotnull)strict |

---

## Vistas

```sql
create view if not exists
empleado_senior (
 dni,
 sexo,
 sueldo,
 supervisor,
 dpto
) as
select
 dni,
 sexo,
 sueldo,
 supervisor,
 dpto
from empleado
where sueldo > 40000;

select
   sexo,
   count(*) as num
from empleado_senior
group by sexo;

drop view if exists empleado_senior;
```

Tabla resultado:

| sexo | num |
| ---- | --- |
| F    | 1   |
| M    | 1   |

`CREATE VIEW` permite crear una vista:
- Una vista es una consulta almacenada que otras consultas pueden invocar
- La vista se vuelve a ejecutar cada vez que se utiliza
- Puede compartirse entre consultas

`DROP VIEW` permite eliminar una vista.

---

## Ejercicio 7 - Vistas

Con fines analíticos y comparativos, se desea crear una nueva vista (`avg_weight_by_species_sex`) en la base de datos penguins que muestre el peso promedio, con una precisión de dos decimales, de los pingüinos por cada especie y sexo. Se debe evitar la aparición de valores nulos para que no complique el análisis posterior.

¿De qué especie son las hembras más pesadas?

Para comprobar tu solución usa la vista definida desde una setencia `SELECT *`;

Abre en otra pestaña [SQL Snippets con la base de datos pnguins](https://i3lab.unex.es/sql-snippets/index.html?db=penguins) para solucionar este ejercicio.

Solución:

```sql

```

Tabla resultado:

| species   | sex    | peso_promedio |
| --------- | ------ | ------------- |
| Adelie    | FEMALE | 3368.84       |
| Adelie    | MALE   | 4043.49       |
| Chinstrap | FEMALE | 3527.21       |
| Chinstrap | MALE   | 3938.97       |
| Gentoo    | FEMALE | 4679.74       |
| Gentoo    | MALE   | 5484.84       |

---
