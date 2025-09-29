# Ejercicios de Modelo Relacional

## Ejercicio 1 - Repaso

Considera las siguientes relaciones:

$$EMPLEADO$$(<u>codigo</u>,nombre,despacho\*)
Clave externa: despacho -> DESPACHO.codigo

| codigo | nombre | despacho |
| ------ | ------ | -------- |
| E1     | María  | 37       |
| E2     | Pedro  | 37       |
| E3     | Flor   |          |
| E4     | Juan   | 40       | 

$$DESPACHO$$(<u>codigo</u>,pabellon,planta)

| despacho | pabellon    | planta |
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

**Especifique las claves necesarias** para este esquema, argumentando todas sus decisiones. 


