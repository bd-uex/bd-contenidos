
```code
erdiagram EmpresaFinal
notation=crowsfoot
```

---
### Entidades
####  Entidad Empleado
```code
entity Empleado {
dni key
nombre
apellido_1
apellido_2
fecha_nacimiento
direccion
sexo
sueldo
}
```
#### Entidad Departamento
```code
entity Departamento {
numero_departamento key
nombre //UNIQUE
ubicaciones
}
```
#### Entidad Proyecto
```code
entity Proyecto {
numero_proyecto key
nombre //UNIQUE
ubicacion
}
```
#### Entidad Familiar
```code
entity Familiar {
nombre key
sexo
fecha_nacimiento
relacion
}
```

---
### Relaciones
#### Relación trabaja_para
```code
relationship trabaja_para {
Empleado[1..N] -> Departamento[1..1]
}
```
#### Relación dirige
```code
relationship dirige {
Empleado[0..1] -> Departamento[0..1]
fecha_ingreso_director
}
```
#### Relación trabaja_en
```code
relationship trabaja_en {
Empleado[1..N] -> Proyecto[1..N]
horas
}
```
#### Relación controla
```code
relationship controla {
Proyecto[1..N] -> Departamento[0..1]
}
```
#### Relación supervisa
```code
relationship supervisa {
Empleado[0..1 | "Supervisor" ] -> Empleado[0..N | "Supervisado"]
}
```
#### Relación familiar_de 
```code
relationship familiar_de {
Familiar[1..N] -> Empleado[0..1]
}
```

---
### Diagrama resultante en notación crow's foot

![](../../imgs/BD%20Empresa%20versión%20inicial%20incorrecta%20Crows%20Foot.png)

>[!tip] Atributos de relaciones
>Recuerda que, aunque en la notación textual sí están incluidos los atributos de relación *horas* en *trabaja_en* y *fecha_ingreso_director* en *dirige*, al mostrarlo de forma gráfica no aparecen y se han añadido de forma manual al diagrama. Del mismo modo, el subrayado discontinuo en los atributos *nombre* de *Proyecto* y *nombre* de *Departamento* se ha añadido de forma manual al diagrama para indicar que son atributos de tipo UNIQUE 
