# Base de Datos de Ejemplo: Universidad

## ESTUDIANTE

| NumEstudiante | Nombre | Clase | Especialidad |
| ------------- | ------ | ----- | ------------ |
| 17            | Luis   | 1     | CS           |
| 8             | Carlos | 2     | CS           |

## CURSO

| NumCurso | NombreCurso                   | Horas | Departamento |
| -------- | ----------------------------- | ----- | ------------ |
| CC1310   | Introducción a la computación | 4     | CC           |
| CC3320   | Estructuras de datos          | 4     | CC           |
| MAT2410  | Matemáticas discretas         | 3     | MAT          |
| CC3380   | Bases de datos                | 3     | CC           |

## EDICION

| IDEdicion | NumCurso | Semestre  | Año | Profesor |
| --------- | -------- | --------- | --- | -------- |
| 85        | MAT2410  | Otoño     | 04  | Pedro    |
| 92        | CC1310   | Otoño     | 04  | Ana      |
| 102       | CC3320   | Primavera | 05  | Elisa    |
| 112       | MAT2410  | Otoño     | 05  | Antonio  |
| 119       | CC1310   | Otoño     | 05  | Juan     |
| 135       | CC3380   | Otoño     | 05  | Enrique  |

## CALIFICACION

| NumEstudiante | IDEdicion | Nota |
| ------------- | --------- | ---- |
| 17            | 112       | B    |
| 17            | 119       | C    |
| 8             | 85        | A    |
| 8             | 92        | A    |
| 8             | 102       | B    |
| 8             | 135       | A    |

## REQUISITO

| NumCurso | NumRequisito |
| -------- | ------------ |
| CC3380   | CC3320       |
| CC3380   | MAT2410      |
| CC3320   | CC1310       |