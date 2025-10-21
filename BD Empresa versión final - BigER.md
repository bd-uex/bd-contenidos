
```code
erdiagram EmpresaFinal
notation=crowsfoot
```

---
### Entidades
####  Entidad EMPLEADO
```code
entity EMPLEADO {
dni key
nombre
apellido1
apellido2
fechaNac
direccion
sexo
sueldo
}
```
#### Entidad DEPARTAMENTO
```code
entity DEPARTAMENTO {
numero_departamento key
nombre //UNIQUE
}
```
#### Entidad PROYECTO
```code
entity PROYECTO {
numero_proyecto key
nombre //UNIQUE
ubicacion
}
```

---
### Relaciones
#### Relación TRABAJA_PARA
```code
relationship TRABAJA_PARA {
EMPLEADO[1..N] -> DEPARTAMENTO[1..1]
}
```
#### Relación DIRIGE
```code
relationship DIRIGE {
EMPLEADO[0..1] -> DEPARTAMENTO[1..1]
fechaIngresoDirector
}
```
#### Relación TRABAJA_EN
```code
relationship TRABAJA_EN {
EMPLEADO[1..N] -> PROYECTO[1..N]
horas
}
```
#### Relación CONTROLA
```code
relationship CONTROLA {
PROYECTO[1..N] -> DEPARTAMENTO[0..1]
}
```
#### Relación SUPERVISA
```code
relationship SUPERVISA {
EMPLEADO[0..1 | "Supervisor" ] -> EMPLEADO[0..N | "Supervisado"]
}
```

---
### Entidades y relaciones débiles

#### Entidad débil FAMILIAR
```code
weak entity FAMILIAR {
nombre partial-key
sexo
fechaNac
relacion
}
```
#### Relación débil FAMILIAR_DE 
```code
weak relationship FAMILIAR_DE {
FAMILIAR[1..N] -> EMPLEADO[0..1]
}
```
#### Entidad débil UBICACION_DPTO
```code
weak entity UBICACION {
nombre partial-key
}
```
#### Relación débil UBICADO_EN
```code
weak relationship UBICADO_EN {
DEPARTAMENTO[1..1] -> UBICACION_DPTO[1..N]
}
```

---
### Diagrama resultante en notación crow's foot

![](resources/BD%20Empresa%20versión%20final%20Crows%20Foot.png)

>[!tip] Atributos de relaciones
>Recuerda que, aunque en la notación textual sí están incluidos los atributos de relación horas en TRABAJA_EN y fechaIngresoDirector en DIRIGE, al mostrarlo de forma gráfica no aparecen y se han añadido de forma manual al diagrama