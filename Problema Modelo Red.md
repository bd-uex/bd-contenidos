# El Problema de la **complejidad** del [[Historia Bases de Datos### Modelo de Red (o grafo)|Modelo en Red]]: La Telaraña

---

tags: #database, #lecture
author: aeprieto
origin: Generado con Gemini 2.5 Pro. Refinado y editado por aeprieto
date: 2025-09-23

---

El modelo en red se creó precisamente para resolver [[Problema Modelo Jerárquico|el problema de la rigidez]] del [[Historia Bases de Datos###ModeloJerárquico|modelo jerárquico]] también conocido como problema de"un solo padre". Su regla fundamental es: **un "hijo" puede tener múltiples "padres"**. Esto resolvió el problema de la duplicación, pero creó uno nuevo.

**Ejemplo Práctico: La Misma Clínica Médica**

Con el modelo en red, podríamos representar la situación así:

- **Médico A** <--- **Paciente 1**
    
- **Médico B** <--- **Paciente 3**
    
- **Médico A** <--- **Paciente 2** ---> **Médico B**
    

El registro único del **Paciente 2** está conectado a dos "padres" diferentes. ¡Perfecto! El problema de la redundancia de datos del modelo jerárquico está resuelto. ¿O no?

El nuevo problema era la **complejidad oculta**. Para que esto funcionara, el sistema creaba una red de "punteros" o "enlaces" físicos entre los registros. Y aquí es donde nace la nueva pesadilla: la **complejidad de navegación**. Para obtener los datos de un paciente, un programa tenía que "navegar" por estos punteros. La lógica de cómo se conectaban los datos no estaba en un mapa conceptual, sino **enterrada en la propia estructura física de la base de datos**.

## El Problema Real: El Programador como "Navegante" en un Laberinto 

Si la estructura de la clínica cambiaba (por ejemplo, añadiendo especialidades que conectan a médicos y pacientes), los programadores tenían que reescribir complejas rutinas de navegación. Era como intentar encontrar el camino en una ciudad sin un mapa, siguiendo una serie de indicaciones complicadas y fáciles de romper.

### Las Consultas eran "Rutas" Físicas, no Preguntas Lógicas

Un programador no podía simplemente preguntar: `“¿Qué médicos atienden al Paciente 2?”`. En cambio, tenía que escribir un programa que actuara como un "navegante", siguiendo los punteros paso a paso. El código se vería conceptualmente así:

1. `IR AL registro del Paciente 2.`
    
2. `UNA VEZ AHÍ, buscar el primer puntero que me lleve a un "padre" (un médico).`
    
3. `SEGUIR ese puntero para llegar al Médico A.`
    
4. `VOLVER al Paciente 2.`
    
5. `BUSCAR el siguiente puntero que me lleve a otro "padre".`
    
6. `SEGUIR ese puntero para llegar al Médico B.`
    
7. `...y así sucesivamente hasta que no queden más punteros.`
    

Toda esta lógica de "ir a", "volver" y "seguir el siguiente" estaba **escrita directamente en el código de la aplicación**.
### El Sistema era Frágil y Rígido:

Ahora imagina que la clínica cambia sus reglas. Decide que los pacientes también deben estar vinculados a una "Especialidad" (Cardiología, Pediatría, etc.). Esto añade nuevas conexiones y punteros a la base de datos.

- **La consecuencia:** **Todos los programas** que navegaban por las antiguas rutas de punteros ahora están rotos o son incorrectos. Hay que encontrar cada uno de esos programas y reescribir a mano las complejas instrucciones de navegación para que tengan en cuenta las nuevas rutas.
    

El mantenimiento se convertía en una tarea titánica y muy propensa a errores. Cambiar algo en la estructura de la base de datos provocaba un efecto dominó que podía romper docenas de aplicaciones.

---

## Conclusión del Dilema

El modelo en red solucionó el problema de la **redundancia de datos**, pero lo cambió por un problema de **complejidad de acceso y mantenimiento**.

- **Dependencia total:** Las aplicaciones dependían completamente de la estructura física de la base de datos. No existía la "independencia de datos".
    
- **Complejidad para el programador:** El trabajo de consultar datos era una tarea de bajo nivel, compleja y tediosa, en lugar de una simple pregunta lógica.