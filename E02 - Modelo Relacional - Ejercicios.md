# Ejercicios de Modelo Relacional

## Ejercicio 1 - Repaso

Considera las siguientes relaciones:

$EMPLEADO$(<u>codigo</u>,nombre,despacho\*)
Clave externa: despacho -> DESPACHO.codigo

| codigo | nombre | despacho |
| ------ | ------ | -------- |
| E1     | María  | 37       |
| E2     | Pedro  | 37       |
| E3     | Flor   |          |
| E4     | Juan   | 40       | 


```sql
CREATE TABLE EMPLEADO(
	codigo TEXT PRIMARY KEY,
	nombre TEXT NOt NULL,
	despacho TEXT UNIQUE,
	FOREIGN KEY (despacho) REFERENCES DESPACHO(codigo) ON UPDATE CASCADE ON DELETE SET NULL
) STRICT;

CREATE TABLE DESPACHO(
	codigo TEXT PRIMARY KEY,
	pabellon TEXT NOT NULL,
	planta INTEGER CHECK (planta >0 and planta<10)
)STRICT;
```

$DESPACHO$(<u>codigo</u>,pabellon,planta)

| codigo   | pabellon    | planta |
| -------- | ----------- | ------ |
| 37       | central     | 2      |
| 40       | informática | 4      |
| 25       | central     | 2      | 

### Preguntas

1. Indica cuál es el grado y la cardinalidad de cada relación.
2. Indica qué sucede si se intenta añadir una nueva tupla (E5, 1245, 25) a `EMPLEADO`. 
3. Indica qué sucede si se intenta borrar la tupla del empleado E4.
4. Indica qué sucede si se intenta añadir una nueva tupla (E5, Elena, 30) a `EMPLEADO`. 
5. Indica qué sucede si se intenta modificar la PK del despacho 37 a el nuevo valor 50 en `DESPACHO`. ¿Cómo podríamos cambiar este comportamiento?
6. Supón que la opción de borrado de la clave externa es borrado restringido. ¿Qué sucedería al intentar borrar el despacho 40 de `DESPACHO`?
7. Supón que la opción de borrado de la clave externa es borrado restringido. ¿Qué sucedería al intentar borrar el despacho 25 de `DESPACHO`?
8. Supón que la opción de borrado de la clave externa es borrado en cascada. ¿Qué sucedería al intentar borrar el despacho 37 de `DESPACHO`?
9. ¿Qué restricciones existen para añadir un nuevo despacho en DESPACHO?
10. ¿Se  puede borrar un empleado de la tabla `EMPLEADO` que tenga un despacho asignado?
11. ¿Existe alguna restricción para poder borrar una o varias filas de la tabla `EMPLEADO`? 
12. Define la asociación que existe entre `EMPLEADO` y `DESPACHO` según su cardinalidad y la opcionalidad de participación.
13. Escribe el código SQL de creación de estas relaciones tan completo como puedas. Asume aquello que consideres necesario y lógico.
14. Indica qué cambiaría en la definición en SQL si consideramos que los despachos son unipersonales.

## Ejercicio 2 - Relaciones

Considere una relación `ESTUDIANTE` en una base de datos `UNIVERSIDAD` con los siguientes atributos (Nombre, NIF, TlfFamiliar, Dirección, TlfResidencia, Edad, NotaMedia). TlfFamiliar se refiere al de su casa familiar y TlfResidencia se refiere al de su residencia actual. Tenga en cuenta que el TlfFamiliar puede ser de un país diferente al TlfResidencia. A continuación se muestra una posible tupla de la relación:

(’Pedro Blas Fiz’, ‘123456789A’, ‘666111222, ‘Martinicos, 34’, ‘765432234, 19, 3.75)

1. Identifica la información crítica perdida de los atributos TlfFamiliar y TlfResidencia del ejemplo anterior.
2. ¿Almacenaría esta información adicional en los atributos TlfFamiliar y TlfResidencia o añadiría otros nuevos al esquema `ESTUDIANTE`?
3. Considere el atributo Nombre. ¿Cuáles son las ventajas de dividir este valor en tres atributos (nombre, apellido1, apellido2)?
4. ¿Cuál sería el criterio que recomendaría para decidir cuándo almacenar la información en un atributo único y cuándo dividirlo en varios?
5. Suponga que el estudiante puede tener entre ninguno y cinco teléfonos. Sugiera dos tipos de diseños diferentes que permitan este tipo de información.

## Ejercicio 3 - Claves externas

Considere las siguientes relaciones para una base de datos que controla las matriculaciones de los estudiantes en los cursos y los libros utilizados en los mismos: 

$ESTUDIANTE$(<u>dni</u>, nombre, asignatura, fechaNac) 

$CURSO$(<u>codigo</u>, nombre, departamento) 

$MATRICULACION$(<u>dni, codigoCurso, trimestre</u>, Nota) 

$LIBRO_USADO$(<u>codigoCurso, trimestre</u>, isbn) 

$LIBRO$(<u>isbn</u>, título, editorial, autor) 

**Especifique las claves externas necesarias** para este esquema, argumentando todas sus decisiones. 

## Ejercicio 4 - De SQL a relaciones

A partir de las sentencias SQL siguientes, define las relaciones en el modelo relacional de forma exhaustiva indicando todo lo que puedas de las mismas.

```sql
-- Tabla de Bandas de Rock
CREATE TABLE bandas (
    id_banda INT PRIMARY KEY,
    nombre_banda VARCHAR(100) NOT NULL,
    ano_formacion INT NOT NULL,
    pais_origen VARCHAR(50) NOT NULL,
    genero_rock VARCHAR(50) NOT NULL,
    esta_activa CHAR(1) DEFAULT 'S',
    CHECK (ano_formacion >= 1950 AND ano_formacion <= 2025),
    CHECK (esta_activa IN ('S', 'N'))
);

-- Tabla de Artistas
CREATE TABLE artistas (
    id_artista INT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    fecha_nacimiento DATE NOT NULL,
    instrumento_principal VARCHAR(50) NOT NULL,
    pais_nacimiento VARCHAR(50) NOT NULL,
    CHECK (fecha_nacimiento >= '1930-01-01'),
    CHECK (instrumento_principal IN ('Voz', 'Guitarra', 'Bajo', 'Batería', 'Teclado', 'Otro'))
);

-- Tabla Histórico de pertenencia de artistas a bandas
CREATE TABLE artistas_bandas (
    id_artista INT NOT NULL,
    id_banda INT NOT NULL,
    fecha_inicio DATE NOT NULL,
    fecha_fin DATE,
    es_lider CHAR(1) DEFAULT 'N',
    rol_en_banda VARCHAR(100),
    PRIMARY KEY (id_artista, id_banda, fecha_inicio),
    FOREIGN KEY (id_artista) REFERENCES artistas(id_artista),
    FOREIGN KEY (id_banda) REFERENCES bandas(id_banda),
    CHECK (es_lider IN ('S', 'N')),
    CHECK (fecha_fin IS NULL OR fecha_fin > fecha_inicio)
);
```


## Ejercicio 5 - Repaso conceptual

Responde a las siguientes preguntas:

1. ¿Por qué las tuplas de una relación no están ordenadas?
2. ¿Por qué no se permiten tuplas duplicadas en una relación?
3. ¿Cuál es la diferencia entre una clave y una superclave?
4. ¿Por qué designamos una de las claves candidatas de una relación como clave principal?
5. Analiza las características de las relaciones que las diferencian de las tablas y archivos comunes.
6. Analiza las diversas razones que llevan a la aparición de valores NULL en las relaciones.
7. Analiza las restricciones de integridad de entidad e integridad referencial. ¿Por qué se considera importante cada una?
8. Defina clave externa o foránea. ¿Para qué se utiliza este concepto?
9. ¿Qué es una transacción? ¿En qué se diferencia de una operación de actualización?

## Ejercicio 6 - Restricciones de integridad

Supongamos que cada una de las siguientes operaciones de actualización se aplica directamente al estado de la [[BD Empresa - Estado|base de datos de Empresa]]. Analiza todas las restricciones de integridad violadas por cada operación, si las hay, y las diferentes maneras de aplicar estas restricciones.

1. Insertar (‘Roberto’, ‘Fernández’, ‘Soto’, ‘943775543’, ‘1992-06-21’, ‘AV. Universidad, Madrid, ES’, 'M', 58000, ‘888665555’, 1) en EMPLEADO.
2. Insertar (‘ProductoA’, 4, ‘Sevilla’, 2) en PROYECTO.
3. Insertar (‘Producción’, 4, ‘943775543’, ‘2007-10-01’) en DEPARTAMENTO. 
4. Insertar (‘677678989’, NULL, ‘40.0’) en TRABAJA_EN.
5. Insertar (‘453453453’, ‘Juan’, ‘M’, ‘1990-12-12’, ‘Conyuge’) en FAMILIAR.
6. Eliminar las tuplas de TRABAJA_EN con empleado = ‘333445555’.
7. Eliminar la tupla de EMPLEADO con dni = ‘987654321’.
8. Eliminar la tupla de PROYECTO con nombre = ‘ProductX’.
9. Modificar director y fechaIngresoDirector de la tupla de DEPARTAMENTO con numero = 5 a ‘123456789’ y ‘2007-10-01’, respectivamente.
10. Modificar el atributo supervisor de la tupla de EMPLEADO con dni = '999887777' a '943775543'.
11. Modificar el atributo horas de la tupla de TRABAJA_EN con empleado = '999887777' y proyecto = 10 a '5.0'.


## Bibliografía

Estos ejercicios han sido extraídos de las siguientes referencias bibliográficas:

1. Garcia-Molina, H., Ullman, J. D., & Widom, J. (2008). Database Systems: The Complete Book.
2. Elmasri, Ramez, & Navathe, Shamkant B. (2017). Fundamentals of Database Systems (7th ed.). Pearson Education Ltd



