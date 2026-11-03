# El Problema de la **rigidez** del [Modelo Jerárquico](Historia%20Bases%20de%20Datos.md#Modelo%20Jerárquico): El Callejón sin Salida

---

tags: #database, #lecture
author: aeprieto
origin: Generado con Gemini 2.5 Pro. Refinado y editado por aeprieto
date: 2025-09-23

---

Este modelo organizaba los datos como un árbol genealógico. Cada "hijo" solo podía tener un "padre". Era simple, el problema es que la vida real rara vez lo es.

**Ejemplo Práctico: Una Clínica Médica**

Imagina que queremos registrar los datos de una clínica. La estructura podría ser:

- **Clínica**
    
    - **Médico A**
        
        - Paciente 1
            
        - Paciente 2
            
    - **Médico B**
        
        - Paciente 3
            

Todo funciona bien hasta que el **Paciente 2** tiene una cita con el **Médico B**. Ahora tenemos un problema:

- **¿Dónde ponemos al Paciente 2?** Según la regla, solo puede tener un "padre" (un médico).
    
- **Opción A (Incorrecta):** Mover al Paciente 2 debajo del Médico B. Al hacer esto, la base de datos **borra por completo** el hecho de que el Paciente 2 fue alguna vez paciente del Médico A. Se pierde el historial. Si alguien consultara la base de datos, pensaría que el Paciente 2 _siempre_ ha sido paciente del Médico B y nunca del A. Es una **representación incorrecta y con pérdida de información**.
    
- **Opción B (Ineficiente):** Dejamos el registro original del Paciente 2 con el Médico A. Luego, creamos un **registro completamente nuevo e idéntico** para el Paciente 2 y lo ponemos debajo del Médico B. Esto crea **redundancia de datos**. Existen dos registros para la misma persona. Imagina que el Paciente 2 cambia su número de teléfono. Un administrativo podría actualizar una de las copias y olvidarse de la otra. Ahora la base de datos es **inconsistente**: tiene dos números de teléfono diferentes para la misma persona. Esto es muy peligroso y conduce a errores graves.
    

Este es el principal problema del modelo jerárquico: **no puede manejar relaciones de "varios a varios" de forma natural**, obligando a a elegir entre:

1. **Perder datos históricos** (Opción A).
    
2. **Crear datos duplicados e inconsistentes** (Opción B).
    

Ambas opciones son inaceptables para una gestión de datos fiable. Este simple ejemplo demuestra por qué se necesitaba un modelo más flexible para representar las relaciones complejas del mundo real.