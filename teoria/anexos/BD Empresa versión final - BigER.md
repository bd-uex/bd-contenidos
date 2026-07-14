
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
Empleado[1..N] -> Proyecto[0..N]
horas
}
```
#### Relación controla
```code
relationship controla {
Proyecto[1..N] -> Departamento[1..1]
}
```
#### Relación supervisa
```code
relationship supervisa {
Empleado[0..1 | "Supervisor" ] -> Empleado[0..N | "Supervisado"]
}
```

---
### Entidades y relaciones débiles

#### Entidad débil Familiar
```code
weak entity Familiar {
nombre partial-key
sexo
fecha_nacimiento
relacion
}
```
#### Relación débil familiar_de 
```code
weak relationship familiar_de {
Familiar[0..N] -> Empleado[1..1]
}
```
#### Entidad débil Ubicacion_Departamento
```code
weak entity Ubicacion_Departamento {
nombre partial-key
}
```
#### Relación débil ubicado_en
```code
weak relationship ubicado_en {
Departamento[1..1] -> Ubicacion_Departamento[1..N]
}
```

---
### Diagrama resultante en notación crow's foot

![](../../imgs/BD%20Empresa%20versión%20final%20Crows%20Foot.png)

>[!tip] Retoques posteriores del diagrama
>Recuerda que, aunque en la notación textual sí están incluidos los atributos de relación *horas* en *trabaja_en* y *fecha_ingreso_director* en *dirige*, al mostrarlo de forma gráfica no aparecen y se han añadido de forma manual al diagrama. Del mismo modo, el subrayado discontinuo en los atributos *nombre* de *Proyecto* y *nombre* de *Departamento* se ha añadido de forma manual al diagrama para indicar que son atributos de tipo UNIQUE 