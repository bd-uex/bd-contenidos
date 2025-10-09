# Ejercicios de Álgebra Relacional


## Ejercicio 1 - Repaso

El siguiente esquema relacional representa información relacionada con vehículos, personas y multas. Resuelve los ejercicios que se proponen a continuación en álgebra relacional.
Puedes usar la [calculadora de álgebra relacional configurada con esta base de datos](https://dbis-uibk.github.io/relax/calc/gist/08c4d28d448a00511a31f5330a3907d5) 

$MARCA$(<u>cod_marca</u>,nom_marca)
$MODELO$(<u>cod_marca, cod_modelo</u>, nom_modelo, potencia)
FK:
- {cod_marca} -> $MARCA$(cod_marca) B:R M:C

$VEHICULO$(<u>matricula</u>, fec_matriculacion, propietario\*, cod_marca, cod_modelo)
FK:
- {cod_marca, cod_modelo} -> $MODELO$(cod_marca, cod_modelo) B:R M:C
- {propietario} -> $PERSONA$(dni) B:N M:C

$PERSONA$(<u>nif</u>, apellido1, apellido2, nombre, domicilio, cod_postal, municipio)
$MULTA$(<u>expediente</u>,infractor, fecha, importe, matricula, articulo, lugar, kilometro)
FK:
- {infractor} -> $PERSONA$(dni) B:C M:C
- {matricula} -> $VEHICULO$(matricula) B:R M:C

---

1.	Datos de los modelos de coche cuya potencia está entre 120 y 200.
```
solución en notación RelaX
```

2.	Nombre y apellidos de las personas que viven en el municipio de Cáceres, en una dirección que contiene una I, ordenando alfabéticamente por apellidos y nombre.
```
solución en notación RelaX
```

3.	Número de expediente, infractor y fecha de las multas impuestas en el primer semestre del 2023, en la Carretera EX-100, entre el km 10 y el 14.
```
solución en notación RelaX
```

4.	Datos de las multas cuyo importe sea mayor de 1000 euros o que correspondan a infracciones en la EX-100, ordenando descendentemente por el importe y, a importes iguales, ascendentemente por el número de expediente.
```
solución en notación RelaX
```

5.	Nif de las personas que han cometido infracciones y eran a su vez propietarios de un vehículo de marca AUDI.
```
solución en notación RelaX
```

6.	Obtener el valor medio y máximo del importe de las multas y el número de multas que se han impuesto en el 2022.
```
solución en notación RelaX
```

7.	Códigos de artículos que se han infringido tanto en la EX-100 como en la EX-101 (los que tengan en común) ordenando por código de artículo.
```
solución en notación RelaX
```

8.	Matriculas de los coches que o bien han intervenido en infracciones o bien el nif de su propietario empieza por 1 y contiene un '39' en la 4ª y 5ª posición.
```
solución en notación RelaX
```

9.	Matrículas de vehículos matriculados a partir del 1 de noviembre de 2018 o cuyo propietario tenga un nif que termine con la letra C. 
```
solución en notación RelaX
```

10.	Apellidos y nombre de las personas que viven en C/ París y el número de expediente e importe de las multas que se le han impuesto en el 2021; si alguno de ellos no ha participado en ninguna multa debe aparecer también en la salida. 
```
solución en notación RelaX
```

11.	Número de vehículos por cada marca y modelo.
```
solución en notación RelaX
```

12.	Nombre de las personas de Badajoz que son propietarios de más de 1 vehículo.
```
solución en notación RelaX
```

13.	Código de artículo (o artículos) para el que existen más multas.
```
solución en notación RelaX
```

14.	Nif de las personas que nunca han recibido ninguna multa. 
```
solución en notación RelaX
```

15.	Nombre de las personas que han recibido alguna multa en la que no estaban conduciendo su propio vehículo (es decir el vehículo que participa en la multa no es de su propiedad)
```
solución en notación RelaX
```

16.	 Nombre de las personas que han infringido más de 2 veces el artículo 301-A.
```
solución en notación RelaX
```

17.	Datos de la persona (o personas) que tienen acumulado un importe total de multas mayor.
```
solución en notación RelaX
```

18.	Para la o las multas de importe más alto obtener el nif y nombre y apellidos del infractor y la matricula, marca y modelo del vehículo que ha participado en esa multa.
```
solución en notación RelaX
```

19.	Número de vehículos de más de 150 de potencia que han participado en multas.
```
solución en notación RelaX
```

20.	Datos de los infractores que han infringido todos los artículos que se reflejan en multas. 
```
solución en notación RelaX
```

21.	Nif de cada persona residente en el código postal 10005 con el número de multas que ha recibido; si la persona no ha recibido ninguna multa deberá aparecer con el valor 0.
```
solución en notación RelaX
```

22.	Matrícula de los vehículos de marca AUDI y el número de multas que se le han impuesto; si alguno de ellos no ha participado en ninguna multa debe aparecer también con un número de multas 0.
```
solución en notación RelaX
```

## Ejercicio 2 - Restricciones 

Expresa las siguientes restricciones sobre las relaciones siguientes:

Producto(fabricante, modelo, tipo)
PC(modelo, velocidad, RAM, disco duro, precio)
Portátil(modelo, velocidad, RAM, disco duro, pantalla, precio)
Impresora(modelo, color, tipo, precio)

Puedes escribir tus restricciones como "contiene" o igualando una expresión al conjunto vacío.

1. Un PC con una velocidad de procesador inferior a 2.00 no debe venderse por más de $500.
```
solución en notación RelaX
```
1. Un portátil con una pantalla inferior a 15.4 pulgadas debe tener al menos un disco duro de 100 gigabytes o venderse por menos de $1000.
```
solución en notación RelaX
```
1. Ningún fabricante de PC puede fabricar también portátiles.
```
solución en notación RelaX
```
1. Un fabricante de PC debe fabricar también un portátil con una velocidad de procesador al menos igual.
```
solución en notación RelaX
```
1. Si una portátil tiene una memoria principal más grande que un PC, entonces el portátil también debe tener un precio más alto que el PC.
```
solución en notación RelaX
```

## Ejercicio 3 - Restricciones

Expresa las siguientes restricciones en álgebra relacional. Las restricciones se basan en las relaciones:

Clases(clase, tipo, país, numCañones, calibre, desplazamiento)
Barcos(nombre, clase, botado)
Batallas(nombre, fecha)
Resultados(barco, batalla, resultado)

Puedes escribir tus restricciones como "contiene" o igualando una expresión al conjunto vacío.

1. Ninguna clase de barco puede tener cañones con un calibre mayor a 16 pulgadas.
```
solución en notación RelaX
```
1. Si una clase de barco tiene más de 9 cañones, su calibre no debe ser mayor a 14 pulgadas.
```
solución en notación RelaX
```
1. Ninguna clase puede tener más de 2 barcos.
```
solución en notación RelaX
```
1. Ningún país puede tener acorazados y cruceros de batalla.
```
solución en notación RelaX
```
1. Ningún barco con más de 9 cañones puede estar en batalla con un barco con menos de 9 cañones que haya sido hundido.
```
solución en notación RelaX
```
