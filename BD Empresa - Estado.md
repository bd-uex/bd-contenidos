
#### EMPLEADO

| nombre   | apellido1 | apellido2 | dni       | fechaNac   | direccion     | sexo | sueldo | superDni  | dpto | 
| -------- | --------- | --------- | --------- | ---------- | ------------- | ---- | ------ | --------- | ---------- |
| José     | Pérez     | Pérez     | 123456789 | 01-09-1965 | Eloy 1, 98    | F    | 30000  | 333445555 | 5          |
| Alberto  | Campos    | Sastre    | 333445555 | 08-12-1955 | Avda. Ríos, 9 | F    | 40000  | 888665555 | 5          |
| Alicia   | Jiménez   | Celaya    | 999887777 | 12-05-1968 | Gran Vía, 38  | M    | 25000  | 987654321 | 4          |
| Juana    | Sáinz     | Oreja     | 987654321 | 20-06-1941 | Canalejas, 67 | M    | 43000  | 888665555 | 4          |
| Fernando | Ojeda     | Ordóñez   | 666884444 | 15-09-1962 | Portillo, s/n | F    | 38000  | 333445555 | 5          |
| Aurora   | Oliva     | Azevuela  | 453453453 | 31-07-1972 | Antón, 6      | M    | 25000  | 333445555 | 5          |
| Luis     | Pajares   | Morera    | 987987987 | 29-03-1969 | Embrujos, 91  | F    | 25000  | 987654321 | 4          |
| Eduardo  | Ochoa     | Paredes   | 888665555 | 10-11-1937 | Las Peñas, 1  | F    | 55000  | NULL      | 1          |

#### DEPARTAMENTO

| nombre         | numero | director  | fechaIngresoDirector |
| -------------- | ------ | --------- | -------------------- |
| Investigación  | 5      | 333445555 | 22-05-1988           |
| Administración | 4      | 987654321 | 01-01-1995           |
| Sede Central   | 1      | 888665555 | 19-06-1981           |

#### LOCALIZACIONES_DPTO

| dpto | ubicacion | 
| ---- | --------- |
| 1    | Madrid    |
| 4    | Gijón     |
| 5    | Valencia  |
| 5    | Sevilla   |
| 5    | Madrid    |

#### PROYECTO

| nombre         | numero | ubicacion | dpto | 
| -------------- | ------ | --------- | ---- |
| ProductoX      | 1      | Valencia  | 5    |
| ProductoY      | 2      | Sevilla   | 5    |
| ProductoZ      | 3      | Madrid    | 5    |
| Computación    | 10     | Gijón     | 4    |
| Reorganización | 20     | Madrid    | 1    |
| Comunicaciones | 30     | Gijón     | 4    |

#### TRABAJA_EN

| empleado  | proyecto | horas | 
| --------- | -------- | ----- |
| 123456789 | 1        | 32,5  |
| 123456789 | 2        | 7,5   |
| 666884444 | 3        | 40,0  |
| 453453453 | 1        | 20,0  |
| 453453453 | 2        | 20,0  |
| 333445555 | 2        | 10,0  |
| 333445555 | 3        | 10,0  |
| 333445555 | 10       | 10,0  |
| 333445555 | 20       | 10,0  |
| 999887777 | 30       | 30,0  |
| 999887777 | 10       | 10,0  |
| 987987987 | 10       | 35,0  |
| 987987987 | 30       | 5,0   |
| 987654321 | 30       | 20,0  |
| 987654321 | 20       | 15,0  |
| 888665555 | 20       | NULL  |

#### FAMILIAR

| empleado  | nombre  | sexo | fechaNac   | relacion |
| --------- | ------- | ---- | ---------- | -------- |
| 333445555 | Alicia  | M    | 06-04-1986 | Hija     |
| 333445555 | Teodoro | F    | 25-10-1983 | Hijo     |
| 333445555 | Luisa   | M    | 03-05-1958 | Esposa   |
| 987654321 | Alfonso | F    | 28-02-1942 | Esposo   |
| 123456789 | Miguel  | F    | 04-01-1988 | Hijo     |
| 123456789 | Alice   | M    | 30-12-1988 | Hija     |
| 123456789 | Elisa   | M    | 05-05-1967 | Esposa   |