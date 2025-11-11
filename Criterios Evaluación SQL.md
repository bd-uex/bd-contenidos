- 1 fallo grave supone un 0 en la pregunta
- 2 fallos medios suponen un 0 en la pregunta
- 1 fallo medio y varios fallos leves pueden supone un 0 en la pregunta

## Fallos graves
Suponen un 0 en el ejercicio.

- G01. No indicar correctamente las claves primarias o externas de una tabla en su creación.
- G02. No usar todas las tablas necesarias en una consulta
- G03. Falta una cláusula fundamental en una consulta, por ejemplo, un group by cuando se requiere agrupación
- G04. Falta el uso de una función de agregación requerida para obtener el resultado
- G05. No usar subconsultas o JOINs cuando se indica explícitamente
- G06. La subconsulta no devuelve la clase de datos adecuada (un dato vs una colección de datos)

## Fallos medios 
Restan el 50% del valor del ejercicio.

- M01. Falta un atributo fundamental en la tabla resultado
- M02. Indicar un JOIN incorrecto (por tipo o por condición)
- M03. Fallar en las condiciones del WHERE o del HAVING
- M04. Fallar en el atributo de agrupación
- M05. No tratar de forma adecuada los valores NULL
- M06. Falta la cláusula ORDER BY
- M07. Usar una función de agregación incorrecta
- M08. Fallo en el orden de las cláusulas de una SELECT

## Fallos pequeños
Algunos restan 0,25 puntos de la calificación máxima del ejercicio.

- L01. Falta un atributo no fundamental en la tabla resultado
- L02. Error de sintaxis de alguna palabra reservada de SQL
- L03. Error menor en algún nombre de atributo o tabla
