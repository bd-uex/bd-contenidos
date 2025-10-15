## Preguntas Conceptuales sobre Sakila

### **Procesos de Negocio**

**1. ¿Qué crees que hay que hacer (qué campos actualizar) cuando se devuelve un DVD que estaba alquilado? ¿En qué tabla(s)?**

**2. Un cliente quiere alquilar una película, pero no está seguro de cuál. Observa las tablas: ¿cómo sabe el sistema en qué tienda (store) puede encontrar copias disponibles de una película específica?**

**3. ¿Por qué crees que existe una tabla `film_actor` separada en lugar de guardar los actores directamente en la tabla `film`?**

---

### **Integridad y Reglas de Negocio**

**4. Observa la tabla `payment`. ¿Qué información falta si quieres saber QUÉ película específica pagó el cliente? ¿Por qué crees que está diseñado así?**


**5. ¿Qué pasaría si intentas borrar un actor que aparece en varias películas? ¿El sistema debería permitirlo? ¿Por qué?**


**6. Mira la tabla `staff`. ¿Qué campo(s) te indica(n) a qué tienda pertenece cada empleado? Si un empleado cambia de tienda, ¿qué otros datos podrían verse afectados?**

---

### **Análisis del Modelo**

**7. ¿Por qué hay dos tablas separadas `city` y `country` en lugar de tener solo "ciudad" y "país" como columnas en la tabla `address`?**

**8. Observa la tabla `customer`. Tiene un campo `active` (0 o 1). ¿En qué situaciones crees que un cliente estaría "inactivo"? ¿Debería el sistema permitir alquileres a clientes inactivos?**

---
# Consultas: Base de Datos Sakila

---

### **Nivel 1: Exploración Básica** - (10 querycoins)

**1. ¿Cuántas películas hay en total en la base de datos y cuál es el título de la película más cara de reemplazar?**


**2. Lista los nombres y apellidos de todos los actores cuyo nombre empiece con 'A'. ¿Cuántos son?**


---

### **Nivel 2: Relaciones Simples** - (10 querycoins)

**3. ¿Qué películas ha protagonizado el actor llamado "PENELOPE GUINESS"? Muestra los títulos ordenados alfabéticamente.**


**4. ¿Cuántos clientes tiene cada tienda (store)? Muestra el ID de la tienda y el número de clientes.**

---

### **Nivel 3: Agregaciones y Análisis** - (10 querycoins)

**5. ¿Cuál es la categoría de películas más popular (la que tiene más películas)? Muestra el nombre de la categoría y la cantidad.**


**6. ¿Cuál es el cliente que más dinero ha gastado en alquileres? Muestra su nombre completo y el monto total.**


---

### **Nivel 4: Consultas Complejas** - (20 querycoins)

**7. ¿Qué película ha generado más ingresos por alquileres? Muestra el título de la película y el ingreso total.**


**8. ¿Qué actor ha aparecido en más películas de la categoría "Action"? Muestra el nombre del actor y el número de películas.**


---

### **Nivel 5: Análisis Avanzado** - (30 querycoins)

**9. Para cada país, muestra cuántos clientes tiene y cuál es el ingreso total generado por esos clientes. Ordena por ingreso descendente.**


**10. ¿Qué películas nunca han sido alquiladas? Muestra el título y el año de lanzamiento.**


---
