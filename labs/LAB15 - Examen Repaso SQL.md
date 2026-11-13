


# Examen 1

---

## Ejercicio 1

Mostrar el nombre, el apellido y la edad de la persona voluntaria más joven de la ONG, junto con el nombre del centro al que está asignada.

Solución:
```sql

```

Tabla resultados:

| PERSONA      | EDAD | CENTRO | 
| ------------ | ---- | ------ |
| DAMIAN PEREZ | 25   | RAFIKI |

---

## Ejercicio 2

Crear una vista VSOMALIA que muestre los datos de los centros de SOMALIA creados antes de 2020 y ubicados en ciudades cuyo nombre incluye la letra A, mostrando el código y nombre del centro, el nombre de la ciudad y el nombre completo del director del centro.

Solución:
```sql

```

Tabla resultados:


---
## Ejercicio 3

Obtener el nombre completo, junto con el nombre del centro en que reside, de los niños menores de 8 años que han tenido más de un tutor. Ordenar alfabéticamente por el nombre del centro y dentro del centro por el apellido y nombre del niño.

Solución:
```sql

```

Tabla resultados:


---
## Ejercicio 4

Obtener el nombre del centro o centros donde trabajan el mayor número de personas (teniendo en cuenta también a los voluntarios), mostrando junto con el nombre del centro, el año de apertura y los nombres de la ciudad y del país donde se ubica. Ordenar por el año de apertura de mayor a menor, y, a igualdad de año, por el nombre del país y el nombre de centro alfabéticamente.

Solución:
```sql

```

Tabla resultados:


---
## Ejercicio 5

Escribe la sentencia que permitiría eliminar de la base de datos a las personas trabajadoras (tipo T) que no han sido tutores nunca.

Solución:
```sql

```

Tabla resultados:


---
## Ejercicio 6

A partir de mañana el niño de código 10140 que está en el centro AFRICAN KIDS cambia de tutor; el nuevo tutor será la persona que es el director de ese centro. Realiza las acciones oportunas en la base de datos para incluir esta nueva información.

Solución:
```sql

```

Tabla resultados:


---

# Examen 2

---

## Ejercicio - Consulta 1

Mostrar el nombre, apellido y edad del niño/a de mayor edad acogido/a en cualquiera de los centros de la ONG, indicando también el nombre del centro en el que reside.

Solución:

```sql

```

Tabla resultado:

| NINO           | EDAD | CENTRO        | 
| -------------- | ---- | ------------- |
| MUFASA OCHIENG | 12   | HAKUNA MATATA |

---

## Ejercicio - Consulta  2

Crear una vista VKENIA que muestre el nombre completo de los niños en cuyo apellido se incluye una I, que llegaron a centros de KENIA después de 2016. Mostrar también el nombre del centro donde está alojado el niño y la ciudad donde se ubica el centro. Ordenar alfabéticamente por el centro y por los  apellidos y nombre del niño.

No olvides consultar la vista:

```
SELECT * FROM VKENIA;
```

Solución:
```sql

```

Tabla resultados:

| NIÑO/A           | CENTRO                 | 
| ---------------- | ---------------------- |
| CHANTAL INGABIRE | AFRICAN KIDS(LAMU)     |
| ERNEST INGABIRE  | AFRICAN KIDS(LAMU)     |
| DIANE ULMULISA   | AFRICAN KIDS(LAMU)     |
| MATU SIMIYU      | HAKUNA MATATA(MOMBASA) |

---

## Ejercicio - Consulta  3

Obtener el apellido y nombre de las personas mayores de 40 años que han sido tutores de más de dos niños junto con el nombre del centro en el que está asignada dicha persona. Ordenar alfabéticamente por el nombre del centro y dentro del centro por el apellido y nombre del tutor.

Solución:
```sql

```

Tabla resultados:

| TUTOR            | CENTRO        | 
| ---------------- | ------------- |
| LUCAS, MARIA     | AFRICAN KIDS  |
| DILUCA, MARIO    | HAKUNA MATATA |
| MALDON, CRISTINE | RAFIKI        |

---
## Ejercicio - Consulta  4

Obtener el nombre del centro (o centros) que acoge al mayor número de niños, mostrando junto con el nombre del centro, el año de apertura y los nombres de la ciudad y el pais donde se ubica. Ordenar por el año de apertura del centro de mayor a menor, y, a igualdad de año, por el nombre del país y el nombre de centro alfabéticamente. 

Solución:
```sql

```

Tabla resultados:

| CENTRO        | AÑO APERTURA | CIUDAD  | PAIS  | 
| ------------- | ------------ | ------- | ----- |
| HAKUNA MATATA | 2013         | MOMBASA | KENIA |

---
## Ejercicio - INSERT

Escribe la sentencia SQL para insertar los datos del siguiente voluntario:

    Código: 13
    Nombre: JAVIER
    Apelliado: AMATE
    Fecha de nacimiento: 10/06/1999
    Centro asignado: KARIBU

No olvides consultar la tabla persona:

```
SELECT * FROM PERSONA;
```

Solución:
```sql

```

Tabla resultados:

| COD_PER | NOM_PER  | APELL_PER | FECNAC_PER | TIPO_PER | SALARIO | COD_CENTRO | 
| ------- | -------- | --------- | ---------- | -------- | ------- | ---------- |
| 1       | MARIO    | DILUCA    | 1970-02-20 | T        |         | 100        |
| 2       | JOHN     | RITMAN    | 1961-05-22 | T        |         | 200        |
| 3       | DAMIAN   | PEREZ     | 2000-07-25 | V        |         | 200        |
| 4       | CRISTINE | MALDON    | 1982-12-31 | T        |         | 200        |
| 5       | ELENA    | RUSO      | 1990-02-02 | T        |         | 100        |
| 6       | ESTHER   | MACIAS    | 1985-03-21 | V        |         | 300        |
| 7       | LUCIA    | ESPINOSA  | 1992-11-12 | T        |         | 300        |
| 8       | MARIA    | LUCAS     | 1963-02-14 | T        |         | 500        |
| 9       | LUC      | SORIAK    | 1999-03-28 | T        |         | 100        |
| 10      | ALI      | SAKALA    | 2000-01-23 | V        |         | 500        |
| 11      | MUNA     | KATO      | 1998-11-11 | T        |         | 400        |
| 12      | CHARITY  | GARUDA    | 1992-07-04 | T        |         | 500        |
| 13      | JAVIER   | AMATE     | 1999-06-10 | V        |         | 300        |

---
## Ejercicio - DELETE

Escribe la sentencia que permitiría eliminar de la base de datos aquellos centros que no están abiertos y que no tienen ninguna persona asignada (ya sea trabajador o voluntario).

No olvides consultar la tabla persona:

```
SELECT * FROM CENTRO;
```

Solución:
```sql

```

Tabla resultados:

| COD_CENTRO | NOM_CENTRO    | FECHA_APERT | CAPACIDAD | COD_CIUDAD | ESTADO | DIRECTOR | 
| ---------- | ------------- | ----------- | --------- | ---------- | ------ | -------- |
| 100        | HAKUNA MATATA | 2013-05-01  | 80        | 120        | A      | 1        |
| 200        | RAFIKI        | 2014-10-10  | 100       | 170        | A      | 4        |
| 300        | KARIBU        | 2014-12-20  | 70        | 205        | A      | 7        |
| 400        | WATOTO        | 2015-02-01  | 50        | 210        | O      | 10       |
| 500        | AFRICAN KIDS  | 2018-12-31  | 100       | 130        | A      | 12       |

---
