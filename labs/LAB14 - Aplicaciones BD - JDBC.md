# JDBC

## Objetivos

**Objetivo**: Aprender JDBC para acceder a los datos de una base de datos relacional desde el código fuente de una aplicación.

- Clases del modelo de datos
- ¿Qué es JDBC?
- Creación de una conexión en JDBC
- Clases útiles para realizar consultas mediante JDBC
	- Statement
	- PreparedStatement
	- ResultSet

Para esta sesión utilizaremos el proyecto [BD-JDBC](https://github.com/rre-uex/BD-JDBC), que incluye la [base de datos DGT](https://i3lab.unex.es/sql-snippets/index.html?db=dgt).

---
## Clases del modelo de datos

```java
package model;

public class Vehiculo {
	private String matricula;
	private int codMarca;
	private int codModelo;
	private String propietario;
	private String nomMarca;
	private String nomModelo;
	
	// getters y setters
	
}
```

- En lenguajes orientados a objetos, como JAVA, los datos se almacenan como objetos en memoria de clases definidas para su representación. 
- Generalmente, estas clases se denominan clases del modelo de datos.
- Todas las clases del modelo suelen organizarse dentro de un mismo paquete `model`.
- Suele definirse una clase por cada tabla.
- También pueden definirse clases que representen resultados de consultas JOIN.

>[!note] Revisa las clases del modelo en el proyecto de ejemplo

---
## ¿Qué es JDBC?

[**Java DataBase Connectivity** (**JDBC**)](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) es una API de Java que permite a las aplicaciones Java interactuar con bases de datos de manera estandarizada y eficiente. Proporciona un conjunto de interfaces y clases que facilitan la conexión y la manipulación de datos almacenados en diferentes sistemas de gestión de bases de datos (SGBD).

### Componentes clave

1. **Drivers de bases de datos**. Los drivers son componentes que permiten que la aplicación Java se comunique con un SGBD específico. Existen diferentes tipos de drivers, como los de tipo 1 (puentes), tipo 2 (nativos), tipo 3 (red) y tipo 4 (puros Java), cada uno con sus propias ventajas y desventajas.
2. **Conexiones**. JDBC proporciona la clase **`DriverManager`**, que se encarga de gestionar las conexiones a las bases de datos. Permite establecer conexiones utilizando cadenas de conexión (connection strings) que contienen la información necesaria, como la URL de la base de datos, el usuario y la contraseña.
3. **Sentencias SQL**. A través de la interfaz **`Statement`**, las aplicaciones pueden ejecutar sentencias SQL en la base de datos. JDBC soporta varios tipos de sentencias, incluyendo **`Statement`**, **`PreparedStatement`** (para sentencias precompiladas) y **`CallableStatement`** (para procedimientos almacenados).
4. **Resultados**. Los resultados de las consultas se gestionan a través de la interfaz **`ResultSet`**, que permite iterar sobre los registros devueltos y acceder a sus datos de forma estructurada.

---

## Creación de una Conexión en JDBC para SQLite

### **¿Qué es una Conexión en JDBC?**

- La conexión es el enlace entre la aplicación Java y la base de datos.
- Permite enviar consultas SQL y recibir resultados.
- Para bases de datos SQLite, se requiere el driver específico de SQLite.

### **Pasos para Crear una Conexión con SQLite**

1. **Agregar el Driver JDBC de SQLite**
    
    - Asegúrate de tener el archivo `.jar` del driver de SQLite (por ejemplo, `sqlite-jdbc.jar`) en el proyecto.
2. **Establecer la Conexión con `DriverManager`**
    
    - **Con SQLite, la URL de conexión tiene el formato:**
        
        jdbc:sqlite:ruta_de_la_base_de_datos
        
3. **Ejemplo Completo: Conectar a una Base de Datos SQLite**
    
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConexionSQLite {
    public static void main(String[] args) {
        String url = "jdbc:sqlite:mi_base.db"; // Ruta de la base de datos SQLite

        try {
            Connection connection = DriverManager.getConnection(url);
            System.out.println("Conexión a SQLite exitosa");
        } catch (SQLException e) {
            System.out.println("Error de conexión: " + e.getMessage());
        }
    }
}
```    

### **Buenas Prácticas**

- Es importante **cerrar la conexión** después de usarla para liberar recursos:

```java 
connection.close();
```

---

## Statement en JDBC

### **¿Qué es un `Statement` en JDBC?**

- Es una interfaz utilizada para ejecutar sentencias SQL simples directamente desde una aplicación Java.
- Permite realizar consultas **sin parámetros** en la base de datos.
- Ideal para operaciones únicas y sencillas (ej. consultas de selección de todos los registros).

**Uso Básico de `Statement`**

1. **Crear el Statement**:
```java
Statement statement = connection.createStatement();
```
    
2. **Ejecutar la Consulta**:  
    - Para consultas de lectura: `executeQuery()`
```java
ResultSet rs = statement.executeQuery("SELECT * FROM empleados");
```       

- - Para operaciones de actualización: `executeUpdate()`
```java
int filas = 
  statement.executeUpdate("UPDATE empleados SET salario = 5000 WHERE id = 1");
```
        

### **Métodos Principales de `Statement`**

| Método          | Descripción                                                                                                          | 
| --------------- | -------------------------------------------------------------------------------------------------------------------- |
| `executeQuery`  | Ejecuta consultas SQL que devuelven datos (SELECT). Devuelve un ResultSet que permite operar sobre el resultado      |
| `executeUpdate` | Ejecuta operaciones de modificación (INSERT, UPDATE, DELETE). Devuelve un entero con la cantidad de tuplas afectadas |
| `execute`       | Ejecuta cualquier sentencia SQL (generalmente no se usa).                                                            |

### **Consideraciones Importantes**

- **Riesgo de SQL Injection**: `Statement` permite la inyección SQL al no parametrizar las consultas.
- **Mejor para consultas únicas**: Evitar en consultas repetitivas; usar `PreparedStatement` en esos casos.

---

## Ejercicio 01 - Listar vehículos

Sigue los siguientes pasos:
1. Ejecuta el programa y selecciona la opción 1: Listar vehículos
2. Escribe a continuación la marca y el modelo (en mayúsculas) separadas por - del vehículo en octava posición

Solución: 

---

## PreparedStatement en JDBC

### **¿Qué es un `PreparedStatement` en JDBC?**

- Es una interfaz de JDBC para ejecutar sentencias SQL con **parámetros**.
- Mejora la **seguridad** y **rendimiento** frente a **`Statement`**:
    - **Previene SQL Injection** al separar la lógica SQL de los datos.
    - Optimiza el rendimiento en consultas repetidas.

### **Uso Básico de `PreparedStatement`**

1. **Crear el `PreparedStatement` con una consulta parametrizada:**
```java
String sql = "SELECT * FROM empleados WHERE departamento = ?";  
PreparedStatement preparedStatement = connection.prepareStatement(sql);
```
    
2. **Asignar valores a los parámetros:**
```java
preparedStatement.setString(1, "Ventas");
```    
3. **Ejecutar la consulta:**
```java
ResultSet rs = preparedStatement.executeQuery();
```

### **Métodos Principales de `PreparedStatement`**

| Método                   | Descripción                                                            | 
| ------------------------ | ---------------------------------------------------------------------- |
| `setString(int, String)` | Asigna un valor de tipo `String` al parámetro en la posición indicada. |
| `setInt(int, int)`       | Asigna un valor de tipo `int` a un parámetro.                          |
| `executeQuery`           | Ejecuta consultas SQL que devuelven resultados (SELECT).               |
| `executeUpdate`          | Ejecuta sentencias de modificación de datos (INSERT, UPDATE, DELETE).  |

### **Ventajas de `PreparedStatement`**

- **Seguridad**: Evita inyección de SQL, ya que los parámetros se gestionan de forma segura.
- **Rendimiento**: Reutiliza la consulta en el servidor de la base de datos, mejorando la eficiencia en consultas repetidas.

---

## Ejercicio 02 - Obtener propietario de un vehículo

Sigue los siguientes pasos:
1. Ejecuta el programa y selecciona la opción 2: Buscar propietario de vehículo por matrícula
2. Introduce la matrícula 3456KRS
3. Escribe a continuación el nif del propietario (en mayúsculas)

Solución: 

---

## ResultSet en JDBC

### **¿Qué es un `ResultSet` en JDBC?**

- **`ResultSet`** es una interfaz que representa el conjunto de resultados de una consulta SQL.
- Permite **navegar**, **leer** y **manipular** los datos devueltos por una consulta **`SELECT`**.
- Se obtiene al ejecutar `executeQuery()` desde un **`Statement`** o **`PreparedStatement`**.

### **Creación y Obtención de un `ResultSet`**

1. **Ejecutar la consulta**
```java
Statement statement = connection.createStatement();
ResultSet rs = statement.executeQuery("SELECT * FROM empleados");
```
    
2. **Recorrer los resultados**
```java
while (rs.next()) {
    int id = rs.getInt("id");
    String nombre = rs.getString("nombre");
    System.out.println("ID: " + id + ", Nombre: " + nombre);
}
```    

**Métodos Principales de `ResultSet`**

| Método                  | Descripción                                                                      | 
| ----------------------- | -------------------------------------------------------------------------------- |
| `next()`                | Mueve el cursor al siguiente registro; devuelve `false` si no hay más registros. |
| `getInt(String col)`    | Obtiene un valor de tipo `int` de una columna especificada.                      |
| `getString(String col)` | Obtiene un valor de tipo `String` de una columna especificada.                   |
| `close()`               | Cierra el `ResultSet` para liberar recursos.                                     |

### **Buenas Prácticas**

- **Cerrar el `ResultSet`** al finalizar el procesamiento para evitar fugas de memoria.
```java
rs.close();
```

---

## Ejercicio 03 - Buscar personas por apellido

Sigue los siguientes pasos:
1. Ejecuta el programa y selecciona la opción 3: Buscar persona por apellido
2. Introduce el texto R, que nos dará todas las personas cuyo apellido1 o apellido2 contengan la letra R
3. Escribe a continuación el nif de la persona en cuarta posición (en mayúsculas)

Solución: 

---

##  Ejercicio 04  -  Listar artículos de las sanciones

Sigue los siguientes pasos:
1. Implementa el método `getArticulos()` para que muestre los distintos artículos de las sanciones ordenados de forma ascentente
2. Escribe a continuación el artículo en tercera posición

Solución (código): 

```java

```

Solución:


--- 

## Ejercicio 05  - Listar sanciones de una persona por su nif

Sigue los siguientes pasos:
1. Implementa el método `getSancionesByNif(String nif)` para que permita buscar las sanciones donde el infractor coincida con el nif introducido por el usuario. Debería mostrar al usuario, al menos, la fecha de la sanción, el articulo y el importe.  
2. Mostrar dicha información ordenada de forma ascendente por la fecha de la sanción.
3. Escribe a continuación la fecha de la primera sanción del infractor con nif 12345678T

Solución (código): 
```java
   
```

Solución:


---

## Ejercicio 06  - Buscar vechículos por artículo de la sanción 

Sigue los siguientes pasos:
1. Implementa el método `getVehiculosByArticuloSancion(String articulo)` para que permita buscar vechículos por el artículo de las sanciones impuestas. Debería mostrar al usuario, al menos, el expediente, el nombre de la marca, el nombre del modelo y la matrícula del vehículo.  
2. Mostrar dicha información ordenada de forma ascendente por el expediente.
3. Escribe a continuación la matrícula de la tercera sanción con el artículo 156-C

Solución (código): 

```java

```

Solución:


---

## Ejercicio 07  - Buscar las sanciones para una marca de vehículo

Sigue los siguientes pasos:
1. Implementa el método getSancionesByMarca(String marca) para que permita buscar las sanciones cuya marca de vehículo sea igual a la marca introducida por el usuario. Debería mostrar al usuario, al menos, el expediente de la sanción, el nombre, apellido1, apellido2 y el nif del infractor así como la marca, el modelo y la matrícula del vehículo y la fecha y el importe de la sanción.
2. Mostrar dicha información ordenada de forma ascendente por el expediente.
3. Escribe a continuación el nif del infractor de la primera sanción de vehículos con marca VOLVO

Solución (código): 

```java

```

Solución:


---

## Ejercicio 08  - Delete

Sigue los siguientes pasos:
1. Implementa el método `deleteSancion(int expediente)` para que permita eliminar sanciones por el número de expediente introducido por el usuario. Muestra al usuario el número de filas actualizadas tras la operación o un mensaje "No se ha encontrado el expediente X" (siendo X el número de expediente introducido) si se actualizaron 0 filas.
2. Intenta eliminar el expediente 123
3. Escribe el número de filas actualizadas por dicha operación

Solución (código): 

```java

```

Solución:

---
