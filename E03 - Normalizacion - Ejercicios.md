# Ejercicios de Normalización


## Ejercicio 1 

Considere la siguiente relación R e indique si, para el conjunto de tuplas almacenadas en este momento, $R$ satisface o no las dependencias funcionales:
- $BE \to D$, 
- $D \to B$, 
- $AD \to E$, 
- $C \to AB$ 
- y $E \to B$


| A   | B   | C   | D   | E   |
| --- | --- | --- | --- | --- |
| a3  | b2  | c2  | d4  | e1  |
| a2  | b1  | c4  | d2  | e1  |
| a1  | b2  | c5  | d1  | e3  |
| a4  | b2  | c3  | d1  | e2  |
| a3  | b2  | c3  | d1  | e3  |

---

## Ejercicio 2

Sea la relación:
$R(A, B, C, D, E, G, H)$ 
$F={E \to GH, C \to D, D \to A, H \to C}$

Supongamos que la relación R tiene ya almacenadas las tuplas:

| A   | B   | C   | D   | E   | G   | H   | 
| --- | --- | --- | --- | --- | --- | --- |
| a1  | b1  | c1  | d2  | e1  | g1  | h1  |
| a1  | b2  | c2  | d2  | e2  | g1  | h2  |
| a1  | b1  | c2  | d2  | e2  | g1  | h2  |
| a1  | b2  | c3  | d1  | e3  | g2  | h3  |

Decidir si cada una de las siguientes tuplas podría estar almacenada en R:

        1. (a1, b1, c1, d1, e2, g1, h2)         
        2. (a1, b2, c3, d1, e4, g2, h3)
        3. (a1, b3, c2, d2, e1, g1, h1)
        4. (a1, b1, c2, d2, e2, g1, h2)

---

## Ejercicio 3

Dado la relación MATRICULA siguiente, encontrar las dependencias funcionales y normalizar.

**MATRICULA** (Sólo 1 GRUPO por asignatura)

| **dni** | **apellido** | **nombre** | **asignatura** | **curso** | **nota** | **aula** | **pabellón** |
| ------- | ------------ | ---------- | -------------- | --------- | -------- | -------- | ------------ |
| 3422    | Pérez        | Ana        | EAI            | 3º        | 7        | I1       | Informática  |
| 3422    | Pérez        | Ana        | BD             | 3º        |          | I1       | Informática  |
| 2543    | Martín       | José       | EDA            | 2º        | 5        | C3       | Central      |
| 2543    | Martín       | José       | ABD            | 3º        |          | Norba    | Informática  |
| 2543    | Martín       | José       | EAI            | 3º        |          | I1       | Informática  |
| 2543    | Martín       | José       | BD             | 3º        |          | I1       | Informática  |
| 7777    | Martín       | Lucía      | BDA            | 4º        |          | I1       | Informática  |




