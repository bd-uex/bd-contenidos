# Programación de Bases de Datos

---

tags: #database, #lecture, #entity-relationship #relational
author: rre
origin: [“Fundamentos de Sistemas de Bases de Datos”](https://explora.unex.es/discovery/fulldisplay?docid=alma991004714442207611&context=L&vid=34UEX_INST:34UEX&lang=es&search_scope=MyInst_and_CI&adaptor=Local%20Search%20Engine&tab=Everything&query=any,contains,elmasri&sortby=date_d&facet=frbrgroupid,include,9013732198023457128&offset=0). R. Elmasri, R. y S. B. Navathe.  Addison-Wesley, 2008 (5ª edición) 
date: 2025-11-24

---
## Contenidos

- Persistencia de datos
- Programación de bases de datos
- SQL embebido
- Programación de bases de datos con llamadas a funciones:
	- Bibliotecas de código: JDBC
	- Patrones de diseño
	- Frameworks ORM
- Procedimientos almacenados de bases de datos

---

## Persistencia de datos

La **persistencia de datos** es un concepto fundamental en la ingeniería de *software*. La persistencia de datos es la capacidad de un sistema para mantener la información disponible más allá del proceso que la creó. Fundamentalmente consiste en, **almacenar los datos para que puedan sobrevivir más allá de la ejecución de un programa**. De manera que, siguen existiendo incluso después de que la aplicación que los generó haya finalizado o, incluso, se haya apagado el sistema.

En el desarrollo de *software*, la persistencia es crucial porque garantiza que la información importante (como registros de usuarios, historial de transacciones, configuraciones de la aplicación o contenido generado) **se almacene y se pueda recuperar** de manera fiable en el futuro.

La persistencia de datos debe mantenerse a través de una serie de retos y cambios, demostrando que los datos "persisten" más allá de:

* **El Ciclo de Vida de las Aplicaciones:** Permite que los datos de un usuario se mantengan entre sesiones (por ejemplo, el carrito de compras en una *e-commerce*).
* **La Migración y Evolución del Esquema:** Asegura que los datos sigan siendo válidos y accesibles incluso cuando la estructura subyacente de la base de datos (**esquema**) se modifica con el tiempo.
* **El *Hardware* y la Migración entre Sistemas:** Facilita que los datos puedan ser trasladados, mantenidos y procesados en máquinas completamente nuevas o en entornos de nube diferentes.
* **Los Desarrolladores Originales:** El conocimiento y la estructura de los datos deben ser independientes de si los desarrolladores que los crearon siguen o no en la empresa.

Existen múltiples formas de realizar la persistencia de datos, clasificándose principalmente en **sistemas de archivos** para almacenamiento básico y configuraciones, **bases de datos relacionales (SQL)** para datos estructurados que requieren estricta integridad transaccional, y **bases de datos NoSQL** para escenarios que demandan flexibilidad de esquema y escalabilidad masiva. A estos pilares se suman estrategias especializadas como la **persistencia en memoria** (caching) para optimizar la velocidad de acceso y el **almacenamiento de objetos** en la nube para gestionar grandes volúmenes de archivos no estructurados, siendo crucial elegir la tecnología adecuada según los requisitos de consistencia y rendimiento del software.

---
## Programación de bases de datos

### Introducción

Una **aplicación de base de datos** es un _software_ diseñado para permitir a los **usuarios finales** interactuar con la información persistente almacenada en un sistema de gestión de bases de datos (DBMS). A diferencia de interactuar directamente con el sistema de base de datos, estas aplicaciones facilitan el acceso y la manipulación de datos a través de interfaces amigables y transacciones predefinidas.

#### Métodos de Interacción con Bases de Datos

Existen tres formas principales de interactuar con una base de datos, que representan diferentes niveles de abstracción:

- **Comandos SQL Directos (Interfaces Interactivas):**
    
    - Implica escribir sentencias **SQL (Structured Query Language)** directamente en un cliente de base de datos (como la línea de comandos o herramientas gráficas tipo _SQL Developer_ o _pgAdmin_).
        
    - Este método lo utilizan principalmente **desarrolladores y administradores** para tareas de configuración, mantenimiento, prueba o consultas específicas _ad hoc_.
        
- **Ejecución de Archivos de Comandos (_Scripts_):**
    
    - Se trata de un archivo de texto (`nombre_de_archivo`) que contiene múltiples comandos SQL secuenciales.
        
    - Se utiliza para automatizar tareas repetitivas, como la creación inicial de un esquema de base de datos o la inserción de grandes volúmenes de datos de prueba.
        
- **Aplicaciones de Bases de Datos:**
    
    - Son las aplicaciones que utilizan los **usuarios finales**.
        
    - Proporcionan una **interfaz de usuario (UI)** que oculta la complejidad del SQL subyacente.
        
    - Los usuarios realizan **transacciones predefinidas** (por ejemplo, "Añadir nuevo producto", "Consultar saldo") haciendo clic en botones o rellenando formularios, y la aplicación traduce estas acciones en comandos SQL que se envían a la base de datos.
        

####  Secuencia Base de Interacción Programática

La programación de una aplicación de base de datos requiere seguir un protocolo estándar para garantizar una comunicación eficiente y segura con el servidor. Esta secuencia se aplica a casi todos los lenguajes de programación y tipos de bases de datos:

1. **Abrir una Conexión:**
    
    - La aplicación debe iniciar una **conexión** o sesión con el servidor de la base de datos.
        
    - Esto requiere proporcionar credenciales de acceso (usuario y contraseña) y detalles de conexión (dirección del servidor y puerto).
        
2. **Interactuar con la Base de Datos:**
    
    - Una vez abierta la conexión, la aplicación envía comandos al servidor. Estos comandos se clasifican generalmente como:
        
        - **Consultas (Queries):** Sentencias `SELECT` para **recuperar** datos.
            
        - **Actualizaciones (Updates):** Sentencias `INSERT`, `UPDATE` y `DELETE` para **modificar** los datos persistentes.
            
        - **Otros Comandos:** Incluye comandos de definición de datos (DDL) como `CREATE TABLE` o comandos de control.
            
3. **Terminar o Cerrar la Conexión:**
    
    - Una vez que la aplicación ha completado todas sus operaciones de datos, es **fundamental** **cerrar la conexión**.
        
    - Cerrar la conexión libera los recursos del servidor de la base de datos, lo que es esencial para el rendimiento y la escalabilidad del sistema.
        

Este flujo asegura que los datos se accedan y manipulen de manera controlada dentro del contexto de la aplicación, manteniendo la **integridad** y la **persistencia** de la información.

---
#### Opciones de acceso a datos desde aplicaciones

Existen cuatro alternativas básicas para el acceso a datos en un base de datos relacional desde el código fuente de una aplicación software. 
1. **SQL Embebido (Embedded SQL)**. Es una **técnica antigua**, pero aún vigente en sistemas "legacy" (como COBOL o C/C++). Consiste en escribir sentencias SQL **directamente intercaladas dentro del código fuente** del lenguaje de programación anfitrión.
2. **Bibliotecas de Código (APIs / Drivers)**. Es el **método más común** para el acceso directo y flexible. El lenguaje de programación utiliza una librería estándar (como **JDBC** en Java, **ODBC** en .NET/C++, o `psycopg2` en Python) para establecer una conexión.
3. **ORM (Object-Relational Mapping)**. Es una **capa de abstracción que busca eliminar la necesidad de escribir SQL manualmente**. El ORM permite manipular la base de datos utilizando **objetos y clases** del lenguaje de programación.
4. **Procedimientos Almacenados (Stored Procedures)**. Aquí la lógica no se encuentra en la aplicación (Java/Python), sino **dentro del motor de la base de datos**. Son scripts o rutinas escritas en SQL (o variantes como PL/SQL) que se guardan y ejecutan en el servidor de base de datos.
    
---
### SQL Embebido

Permite escribir consultas SQL dentro del código de un lenguaje anfitrión, como C, C++ o COBOL. Las consultas son preprocesadas por un precompilador antes de la compilación del programa.

Ejemplo (en C):
```c
EXEC SQL SELECT * INTO :variable FROM users WHERE id = :id;
```

`EXEC SQL` marca las sentencias SQL embebidas, y las variables en el lenguaje anfitrión (como :`variable` y :`id`) se usan para vincular datos.

#### Precompilador
Se requiere un **precompilador** que procesa el código antes de la compilación final, traduciendo las sentencias SQL a llamadas a funciones que la base de datos pueda entender.

- Escanea el código fuente y procesa las sentencias SQL embebidas.
- Reemplaza el SQL embebido con llamadas a funciones y estructuras de datos que el lenguaje anfitrión pueda entender y usar para comunicarse con la base de datos. Por ejemplo, las sentencias SQL embebidas se convierten en código C que utiliza la API de la base de datos para manejar operaciones como la conexión, la ejecución de consultas y la recuperación de datos.

Esta alternativa presenta una fuerte dependencia del precompilador, que suele ser una herramienta proporcionada por el mismo proveedor de la base de datos.

Un fragmento de código C con SQL embebido:

```c
#include <stdio.h>
EXEC SQL BEGIN DECLARE SECTION;
char name[50];
int age;
int userld = 123;
EXEC SQL END DECLARE SECTION;

int main() {
	EXEC SQL CONNECT TO my_database;
	EXEC SQL SELECT name, age INTO : name, age FROM users WHERE id = :userId;
	printf("Name: %s, Age: %d\n", name, age);
	EXEC SQL COMMIT:
	return 0;
}
```

Ejemplo ilustrativo de código generado:

```c
#include <stdio.h>
#include "db_library-h" // Encabezado hipotético de biblioteca de base de datos

char name[501;
int age;
int userld = 123;

int main(){
	// Conectar a la base de datos
	db_connect ("my _database"); 
	// Preparar la consulta
	db_prepare ("SELECT name, age FROM users WHERE id = ?", userId); 
	// Ejecutar la consulta 
	db_execute(); 
	// Obtener los resultados en las variables
	db_fetch (name, &age): 
	printf( "Name: %s, Age: %d\n", name, age);
	// Confirmar la transacción 
	db_commit(); 
	return 0;
}
```

El precompilador puede **identificar errores de sintaxis básicos en las sentencias SQL** embebidas. Por ejemplo, si falta una palabra clave o hay una puntuación incorrecta en la consulta SQL, el precompilador marcará el error.

>[!note] Ejemplo
>Si escribes:
> `SELECT * FROM users WHERE id =` 
> el precompilador detectará que la sintaxis del SQL está incompleta.

El precompilador puede **verificar si las variables del lenguaje anfitrión (host) están declaradas correctamente** y si son **compatibles con los tipos de datos SQL** esperados por la base de datos. 

>[!note] Ejemplo
>Si usas una variable del host sin declararla en la sección `EXEC SQL BEGIN DECLARE SECTION`
el precompilador lo señalará como un error.

#### Uso actual
Actualmente, esta alternativa se considera *legacy* (solucion heredada) y está **claramente en desuso** por las siguientes razones:
- **Seguridad**. Es una alternativa que aumenta la posibilidad de error por parte de los desarrolladores (propensa a errores) y, por lo tanto, la hace más vulnerable a ataques de inyección SQL.
- **Mantenibilidad del Código**. Presenta una alta rigidez y acoplamiento que la hace más difícil de mantener y refactorizar a medida que cambian los esquemas de la base de datos.
- **Separación de Responsabilidades**. No permite una organización de código correcta que favorezca la separación de la lógica de negocio y la lógica de acceso a datos

Todavía es posible encontrar el uso de esta alternativa en algunos contextos específicos:
- **Sistemas Heredados**. En industrias que dependen de tecnologías más antiguas y son muy reacias al cambio, como la banca.
- **Aplicaciones de Alto Rendimiento**. Se necesita acceso directo y controlado a la base de datos.
- **Sistemas Embebidos**. Cuando los recursos limitados no permiten el uso de bibliotecas o frameworks generalistas

### Bibliotecas de código
Es el método más común para el acceso directo y flexible. El lenguaje de programación utiliza una librería estándar (como **JDBC** en Java, **ODBC** en .NET/C++, o `psycopg2` en Python) para establecer una conexión.

- **Cómo funciona:** El programador escribe el SQL como **cadenas de texto (strings)** dentro del código y utiliza la biblioteca para enviarlas a la base de datos y recibir los resultados.
    
- **Clave:** Ofrece control total sobre la consulta SQL, pero requiere gestionar manualmente las conexiones, los errores y la conversión de los datos recibidos.
    
#### JDBC (Java Database Connectivity) 
API (Interfaz de Programación de Aplicaciones) proporcionada por Java para conectar y ejecutar consultas a bases de datos desde aplicaciones.
	
- Permite a las aplicaciones interactuar con bases de datos relacionales de manera sencilla y eficiente. 

JDBC proporciona un conjunto estándar de interfaces y clases que los desarrolladores pueden usar para conectarse a bases de datos, ejecutar consultas SQL, y gestionar los resultados.

 Sus componentes fundamentales son los siguientes:
- **DriverManager**. Gestiona una lista de controladores de bases de datos. Se utiliza para establecer conexiones.
- **Connection**. Representa una conexión establecida. Se utiliza para ejecutar consultas y gestionar transacciones.
- **Statement, PreparedStatement, CallableStatement**. Consultas SQL simples y parametrizadas, y procedimientos almancenados.
- **ResultSet**. Contiene los resultados de la consulta.

**Ejemplo**

```java
import java.sql.Connection; 
import java.sql.DriverManager;
import java.sql.ResultSet; 
import java.sql.Statement;

public class Example {
	public static void main (String[] args) {
		try {
			// Establecer la conexion
			Connection conn = 
				DriverManager.getConnection("jdbc:sqlite:my _database.db");
			
			// Crear una consulta
			Statement stmt = conn.createStatement():
			ResultSet rs = stmt.executeQuery("SELECT * FROM users"):

			// Procesar los resultados
			while (rs.next()) {
				System.out.println(
					rs.getString ("name") + " - " + rs.getInt ("age")
				);
			}

			// Cerrar recursos
			rs.close();
			stmt.close();
			conn.close();
		} catch (Exception e) {
			e.printstacktrace():
		}
	}
}
```

**Statement vs PreparedStatement**

**Statement**
- Se escriben las consultas SQL como cadenas de texto.
- Vulnerables a inyección de SQL al construir dinámicamente una consulta mediante la concatenación de cadenas. Por ejemplo: un usuario puede introducir datos para manipular la consulta 

```java
Statement stmt = conn.createStatement();
String sql = "SELECT * FROM users WHERE username = '" + userInput + "'";
ResultSet rs = stmt.executeQuery(sql);
```

Si userInput es: `“’; DROP TABLE users; --’` la consulta se convierte en

```sql
SELECT * FROM users WHERE username = ''; DROP TABLE users; --'
```

**PreparedStatement (consulta parametrizada)**
- Usa marcadores de posición (?) para los parámetros y vincula los valores a los mismos usando métodos setter
- Separa el código SQL de la entrada del usuario, tratada como datos
	- La entrada de usuario no modifica la consulta

```java
String sql = "SELECT * FROM users WHERE username = ?";
PreparedStatement pstmt = conn.preparestatement(sql);
pstmt.setString(1, userInput); // Bind the user input to the placeholder
ResultSet rs = pstmt.executeQuery();
```

Si userInput es: `“’; DROP TABLE users; --’` la consulta buscará un usuario cuyo `username` sea esa cadena.

Estás consultas pueden ser **precompiladas y cacheadas por la base de datos**. De manera que, mejora notablemente el rendimiento en consultas con alta frecuencia de ejecución.

**Ventajas**

- **Independencia de la Base de Datos**. Funciona con múltiples sistemas de gestión de bases de datos (SGBD) como MySQL, PostgreSQL, Oracle, SQLite, etc. El mismo código puede ser reutilizado con diferentes bases de datos, cambiando solo el controlador JDBC correspondiente.
- **Control Completo**. Proporciona un control de grano fino sobre las consultas SQL y la gestión de la conexión, útil cuando se necesita optimizar el rendimiento o implementar características específicas de la base de datos.
- **Integración Directa con Java**. Es parte del estándar de Java, sin necesidad de librerías externas.
- **Soporte de Transacciones**. Esencial para aplicaciones que requieren asegurar la consistencia de los datos.
- **Flexibilidad**. Permite ejecutar consultas SQL dinámicas y parametrizadas, así como invocar procedimientos almacenados. Adecuado para aplicaciones complejas que necesitan manejar una amplia variedad de interacciones con la base de datos.

**Desventajas**

- **Complejidad del Código**. Resulta en una cantidad significativa de **código repetitivo** para gestionar las conexiones, manejar las consultas y procesar los resultados. Este código es más difícil de mantener y propenso a errores, especialmente en aplicaciones grandes. Además, **no puede detectar errores de sintaxis SQL** en tiempo de compilación.
- **Pérdida de Recursos**. Si no se cierran correctamente las conexiones, sentencias, y conjuntos de resultados (por ejemplo, en casos de excepción), pueden ocurrir pérdidas de memoria.
- **Baja abstracción (bajo nivel)**. Capa de abstracción limitada sobre la base de datos, los desarrolladores deben escribir SQL directamente. 
- **Curva de Aprendizaje**. Para los desarrolladores nuevos puede tener una curva de aprendizaje significativa.
- **Mantenimiento y Escalabilidad**. El código JDBC es **difícil de mantener y escalar** debido a la cantidad de código SQL y la falta de herramientas avanzadas que proporcionan frameworks de mayor nivel, como los ORMs (Object-Relational Mappers).

### Desajuste de impedancias (impedance mismatch)

El [[desajuste de impedancias]] se refiere a las dificultades que surgen al intentar mapear el modelo relacional de una base de datos (tablas, filas y columnas) con el modelo orientado a objetos utilizado en lenguajes de programación como Java (clases, objetos, herencia, etc.). 
Este desajuste ocurre porque las bases de datos relacionales y la programación orientada a objetos manejan y representan los datos de manera fundamentalmente diferente.

Ejemplo de representación de datos en SQL:
```sql
-- Tabla de usuarios
CREATE TABLE Users (
	user_id INT PRIMARY KEY, 
	name VARCHAR(100),
	email VARCHAR(100)
);

-- Tabla de Pedidos
CREATE TABLE Orders (
	order_id INT PRIMARY KEY, 
	order_date DATE,
	amount DECIMAL (10, 2),
	user_id INT,
	FOREIGN KEY (user_ id) REFERENCES Users(user_id)
);
```

Ejemplo de representación de datos en Java:
```java
// Clase Usuario
public class User {
	private int userid;
	private string name;
	private string email;
	private List<order> orders: //Relación uno a muchos
	
	// Getters y setters
}

// Clase Pedido
public class Order {
	private int orderId;
	private Date orderbate:
	private double amount;
	private User user;  // Relación inversa
	
	// Getters y setters
}
```


#### Conversión de Datos
- JDBC no proporciona soluciones integradas para mapear automáticamente datos relacionales a objetos Java. 
- Los desarrolladores deben convertir manualmente los datos obtenidos de la base de datos (ResultSet) en objetos Java. 
- De manera similar, al guardar objetos en la base de datos, deben extraer y mapear manualmente los atributos del objeto a los parámetros SQL.

```java
ResultSet rs = stmt.executeQuery ("SELECT id, name, age FROM users");
while (rs.next()) {
	int id = rs.getInt("id");
	String name = rs.getString("name");
	int age = rs.getInt("age");

	// Crear manualmente un objeto User
	User user = new User(id, name, age)
```

**Problema**. Código repetitivo y propenso a errores, especialmente en aplicaciones complejas con muchas tablas y relaciones.

#### Manejo de Relaciones Complejas
- Relaciones Uno a Muchos y Muchos a Muchos. Los desarrolladores deben escribir consultas SQL adicionales y gestionar las estructuras de datos manualmente para manejar estas relaciones. Por ejemplo: Si un objeto `Student` tiene una lista de objetos `Grade`, necesitarás escribir consultas separadas para obtener, por un lado, el estudiante y, por otro lado, las calificaciones y luego asociarlas manualmente con el estudiante.

![[BD - programacion relaciones.png]]

#### Falta de Soporte de Herencia
- Las bases de Datos Relacionales no (suelen) soportar la herencia de forma nativa. Mientras que los lenguajes de programación orientados a objetos, como Java, admiten la herencia entre datos.

**Solución en JDBC**. Los desarrolladores deben usar patrones de diseño o implementaciones personalizadas para manejar la herencia. Por ejemplo, crear tablas separadas para cada subclase o usar una sola tabla con todos los campos, lo que requiere un manejo más complejo de las consultas en Java.

#### Manejo de Valores Nulos y Tipos de Datos
Para solucionar el desajuste de Tipos de Datos entre SQL y Java, JDBC proporciona métodos como `getString(), getInt(), getDate()`, etc., para manejar la conversión de tipos entre SQL y Java. 
Pero es **responsabilidad del desarrollador tener cuidado con los desajustes de tipos y manejar explícitamente los valores null** para que no se mapeen directamente a tipos primitivos de Java.

>[!note] Ejemplo
>Si una columna en la base de datos es NULL, `getInt()` devuelve 0 por defecto, que puede no ser el comportamiento deseado desde la lógica de negocio. Para poder manejar los valores nulos, se necesitaría usar `wasNull()` para verificar si el valor era NULL.
>https://schneide.blog/2021/02/22/jdbcs-wasnull-method-pitfall/ 

**Manejo de Valores Nulos con Optional (Java 8)**
[`Optional<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) en Java es un contenedor (una especie de caja envoltorio) que puede contener un valor no nulo o estar vacío. Fue introducido en **Java 8** (`java.util.Optional`) con un objetivo principal: **evitar el famoso error `NullPointerException` (NPE)** y hacer el código más expresivo.

```java
public class ResultSetWrapper {
	private final ResultSet resultSet;
	
	public ResultSetWrapper (ResultSet resultSet) {
		this.resultSet = resultset;
	｝

	// Método para obtener un Optional de Integer
	public Optional< Integer> getOptionalInt(String columnLabel) 
	throws SQLException {
		int value = resultSet getInt (columnLabel);
		return resultSet.wasNull() ? Optional.empty() : Optional.of(value);
	}
	
	// ...
	
}
```

```java
// Usar el ResultSetWrapper para obtener valores con Optional
ResultSetWrapper wrapper = new ResultSetWrapper(rs);

while (rs.next ()) {
	Optional<Integer> id = wrapper.getOptionalInt("id");
	Optional‹String > name = wrapper getOptionalString ("name");
	Optional<Double> salary = wrapper.getOptionalDouble ("salary");

	// Ejemplo de cómo manejar los Optional
	id.ifPresent (value -> System.out.println("ID: " + value));
	
	// ...

}
```

### Patrones de diseño

Los patrones de diseño son soluciones probadas y eficientes a problemas recurrentes.

En este caso, nuestro problema se puede enunciar como "organización del código SQL dentro del código de la aplicación (separar ambos)".

Algunos patrones de diseño que permiten solucionar este problema son:
- Data Access Object (DAO) (objeto de acceso a datos)
- Active Record (registro activo)
- Query Object (objeto consulta)

#### Patrón Data Access Object (DAO)
Propone una interfaz uniforme para realizar operaciones CRUD (crear, leer, actualizar, eliminar) en los datos sin exponer los detalles de implementación de la BD. 
Separa lógica de negocio de lógica de acceso a los datos, facilitando mantenimiento y pruebas del código.

Su estructura es la siguiente:
- Una clase DAO para cada entidad de datos. Por ejemplo, `UserDAO` para gestionar la entidad `User`.
- Métodos en la clase DAO para operaciones CRUD, como `save()`, `update()`, `delete()`, y `findById()`.

```java
public interface UserDAO {
	void save(User user);
	User FindByld(int id);
	void update (User user);
	void delete (int id);
}

public class UserDAOImpl implements UserDAO {
	// Implementación de métodos de interacción con base de datos
}
```

```java
UserDAO userDAO = new UserDAOImpl(connection);

// Crear un nuevo usuario
User newUser = new User(0, "John Doe", "john.doe@example.com");
userDAO.save(newUser);

// Encontrar un usuario por ID
User userFound = userDAO.findById(1);
```

#### Active Record
En este caso, el patrón sugiere que sean las propias entidades de datos las que contengan los métodos para realizar operaciones de base de datos. De esta manera, se simplifica el acceso a la base de datos al combinar el comportamiento de la entidad y las operaciones de persistencia. En definitiva, las entidades de datos son las responsables de su persistencia en bases de datos.

Su estructura es la siguiente:
- Métodos como `save()`, `update()`, `delete()`, y `find()` dentro de la misma clase de entidad de datos.

```java
public class User {
	private int id;
	private String name;
	
	public void save() {
		// Lógica para guardar el usuario en la base de datos
	}

	public static User find (int id) {
		// Lógica para buscar un usuario por ID
		return new User();
	}
}
```

```java
// Crear un nuevo usuario y guardarlo en la base de datos
User newUser = new User (0,"John Doe", "john.doe@example.com");
newUser.save();
System.out.println("Usuario guardado con ID: " + newUser.getId ());

// Buscar un usuario por ID
User userFound = User.find(newUser.getId());
```

#### Query Object
Este patrón sugiere **encapsular consultas complejas en un solo objeto**. 
- Permite construir y ejecutar consultas de manera flexible y reutilizable.
- Simplificar la construcción de consultas complejas y proporcionar una forma más fluida de manejar los criterios de búsqueda o selección.

Su estructura es la siguiente:
- Un objeto de consulta que se configura con los criterios de búsqueda y luego se ejecuta para devolver resultados.

```java
public class UserQuery {
	private String lastName;
	private boolean isActive;
	
	public UserQuery withLastName(String lastName) {
		// Configura el criterio de filtrado
	} 
	
	public UserQuery onlyActive() {
		// Configura el criterio de filtrado
	} 
	
	public List<User> execute() {
		// Ejecuta la consulta
	}
```

```java
// Crear un objeto UserQuery y configurar los filtros
UserQuery query = new UserQuery()
			.withLastName("Smith") // Filtrar usuarios con el nombre "John"
			.onlyActive(); // Solo usuarios activos

// Ejecutar la consulta y obtener los resultados
List<User> users = query.execute();

// Imprimir los resultados
for (User user : users) 
	System.out.println("ID: " + user.getId() +
					   ", Name: " + user.getName() +
					   ", Email: " + user.getEmail());
```

#### El patrón de organización en capas
El patrón de organización en capas es una arquitectura de software que estructura la aplicación en niveles jerárquicos horizontales (típicamente Presentación, Lógica de Negocio y Acceso a Datos), donde cada capa encapsula una responsabilidad específica y solo se comunica con la capa inmediatamente inferior o adyacente.

La figura siguiente es un ejemplo de del modelo de tres capas (instancia del patrón capas).

![[BD - 3layermodel.png]]

Este diseño promueve la **separación de intereses** (_Separation of Concerns_), permitiendo que los cambios internos en un nivel (por ejemplo, cambiar el motor de base de datos en la capa inferior) no afecten a los niveles superiores, lo que mejora drásticamente la modularidad, la facilidad de pruebas y el mantenimiento del sistema a largo plazo.

---

### Frameworks Software

Un framework de desarrollo software es un **conjunto de herramientas, estructuras, y reglas predefinidas para construir aplicaciones software de manera más eficiente**. El framework establece una arquitectura base y ofrece componentes reutilizables, que ayuda a los desarrolladores a enfocarse más en la lógica de la aplicación en lugar de en los detalles básicos de la implementación.

No confundir con biblioteca/librería de clases (class library, aka API) que es un conjunto de clases relacionadas y reusables diseñadas para proporcionar funcionalidad útil de propósito general.

Para entender el concepto de framework de desarrollo software, puedes pensar en el problema de construir una casa a partir de los componentes de construcción más simples, como ladrillos, tuberías, cables, etc. Este problema es muy complejo y requiere de gran experiencia y el seguimiento de soluciones probadas por parte de los desarrolladores. Tal y como, sucede en el desarrollo software cuando tratamos de construir sistemas software complejos sobre plataformas tecnológicas complejas usando simplemente los elementos que nos proporciona un lenguaje de programación y sus librerías de código.

![[BD - framework.png]]

El uso de frameworks de desarrollo software se asemeja más al uso de módulos prefabricados para la construcción de casas, como se ilustra en la figura. Estos módulos ya están  probados y están específicamente diseñados para integrarse unos con otros en la construcción de casas más complejas. Los módulos prefabricados necesitan adaptarse y configurarse para su uso correcto en un proyecto: no están completos. Como resultado del uso de estos módulos prefabricados, la construcción de una casa es un problema mucho más sencillo y eficiente de resolver porque reutilizamos todo el conocimiento y experiencia implícito en el diseño de esos módulos. Si bien, el uso de estos módulos prefabricados no permite la construcción de cualquier casa, sino solo de aquellas que puedan montarse a partir de la composición de los mismos. Del mismo modo, al usar un framework de desarrollo software, estamos perdiendo algo de libertad en el desarrollo de la solución final.

![[BD - framework2.jpg]]

#### Frameworks ORM (Object-Relational Mapping)

Un framework ORM es una **capa de abstracción que busca eliminar la necesidad de escribir SQL manualmente**. El ORM permite manipular la base de datos utilizando **objetos y clases** del lenguaje de programación. 

Básicamente, consiste en mapear las tablas de una base de datos relacional a clases en un lenguaje de programación orientado a objetos, y las filas de esas tablas a instancias de esas clases (objetos). Por ejemplo, una tabla `User` en una base de datos puede mapearse a una clase `User` en código, y cada fila en la tabla se convertirá en un objeto de esa clase.

- **Cómo funciona:** Si tienes una clase `Cliente` en tu código, el ORM la traduce automáticamente a una fila en la tabla `clientes`. Al guardar el objeto (`cliente.save()`), el ORM genera el `INSERT` por ti.
    
- **Clave:** Aumenta drásticamente la velocidad de desarrollo y la legibilidad del código, aunque puede sacrificar rendimiento en consultas muy complejas.
    
**Ventajas de un ORM**
- Facilita el Desarrollo. 
	- No es necesario escribir y mantener consultas SQL complejas.
	- Interacción con datos mediante objetos como el resto de la aplicación.
	- Facilita la sincronización de los cambios hechos en memoria con la base de datos.
- Código Más Limpio y Mantenible. Básicamente, porque no hay SQL.
- Independencia de la Base de Datos. Los ORM son compatibles con diferentes SGBDs.
- Gestión Automática de Transacciones. Respeto de las propiedades ACID.

**Ejemplos de Framework ORM**
- [Hibernate](https://hibernate.org/orm/). Un framework ORM muy popular en Java
- [Entity Framework](https://learn.microsoft.com/es-es/ef/). Un framework ORM en .NET
- [SQLAlchemy](https://www.sqlalchemy.org/). Un framework ORM en Python

Ejemplo de uso de Hibernate

```java
@Entity // Indica que esta clase es una entidad de Hibernate
@Table (name = "users") // Mapea esta clase a la tabla "users"
public class User {
	@Id // Define la clave primaria
	// Generación automática del ID
	@GeneratedValue(strategy = GenerationType. IDENTITY) 
	private Long id;
	private String name;
	private String email;
	
	// Constructores
	public User() () // Constructor sin argumentos requerido por Hibernate
	public User(String name, String email) {
		this.name = name;
		this.email = email;
	}

	// Getters y setters
	public Long getid() {
		return id;
	}
```

```java
// Crear una fábrica de sesiones
SessionFactory factory = new Configuration()
			.configure ("hibernate.cfg.xml") // Cargar la configuración
			.addAnnotatedClass(User.class) // Añadir la clase de entidad
			•buildSessionFactory();
			
// Crear una sesión
Session session = factory getCurrentSession ();

try {
	// Crear un nuevo usuario
	User newUser = new User("John Doe", "john.doe@example.com");
	
	// Iniciar una transacción
	session.beginTransaction();
	// Guardar el usuario en la base de datos
	session.save(newUser);

	// Confirmar la transacción
	session.getTransaction().commit();
	
	// ...
```

```java
// Recuperar el usuario por ID
session = factory.getCurrentSession();
session.beginTransaction();
User retrievedUser = session.get(User.class, newUser.getId());
session.getTransaction().commit();

System.out.println("Usuario recuperado: " + retrievedUser.getName());
```

>[!note] Anotaciones de JAVA
>La mayoría de ORMs en Java usan [anotaciones de Java](https://docs.oracle.com/javase/tutorial/java/annotations/) para definir el mapeo objeto-relacional.
Son útiles porque permiten definir la configuración del mapeo directamente en las clases de entidad de Java, haciendo que el código sea más limpio y fácil de mantener. 
En lugar de usar archivos de configuración externos como XML, integran la configuración de la base de datos con el modelo de datos de la aplicación, facilitando el desarrollo.

**Anotaciones de JAVA en Hibernate**
- Hibernate escanea las clases de entidad en busca de anotaciones para comprender cómo mapear las clases de Java a las estructuras de la BD.
- Según las anotaciones, Hibernate genera automáticamente las consultas SQL necesarias para realizar operaciones de persistencia.
- Para las operaciones como `session.save(entity)`, `session.update(entity)`, o `session.delete(entity)`, Hibernate usa las anotaciones para determinar cómo construir las consultas SQL apropiadas.

### Procedimientos Almacenados (Stored Procedures)

En esta alternativa la lógica de negocio no se encuentra en el código de la aplicación (Java/Python), sino **dentro del motor de la base de datos**. Son scripts o rutinas escritas en SQL (o variantes como PL/SQL) que se guardan y ejecutan en el servidor de base de datos.

Estos procedimientos se almacenan de forma persistente por el SGBD. Según el estándar SQL, se denominan SQL/PSM (Persistent Stored Modules). 

Se consideran principalmente de utilidad cuando varias aplicaciones necesitan una misma lógica de operaciones sobre la base de datos. Además, como ventaja adicional, permiten reducir los costes de transferencia de datos y comunicación entre el cliente y el servidor en determinadas situaciones.

- **Cómo funciona:** La aplicación simplemente llama al procedimiento por su nombre (ej. `EjecutarCalculoIntereses`) y la base de datos hace todo el trabajo pesado internamente, devolviendo solo el resultado final.
    
- **Clave:** Excelente rendimiento (código precompilado) y seguridad, pero hace que la lógica de negocio sea difícil de migrar a otro motor de base de datos.

#### Oracle PL/SQL (Procedural Language/Structured Query Language) 
Es un lenguaje de programación procedural para extender las capacidades de SQL en bases de datos Oracle. Permite combinar la potencia de SQL con la funcionalidad y elementos de los lenguajes de programación procedurales: variables, estructuras de control, procedimientos y paquetes.

**Casos de uso comunes**
- **Automatización de tareas administrativas**, como la generación de informes o la actualización de datos. 
- **Validaciones y Reglas de Negocio**. Validaciones y reglas de negocio sencillas de uso común por parte de múltiples aplicaciones que usen la misma base de datos.
- **Procesamiento en Lote**. Actualización de múltiples registros a la vez.
- **Triggers**. Ejecutar acciones automáticamente en respuesta a eventos en la base de datos.

>[!note] Ejemplo
>Imagina que una empresa necesita actualizar los salarios de los empleados periódicamente y, por cuestiones de auditoría y cumplimiento normativo, debe mantener un registro de cada cambio de salario en una tabla de auditoría. Un procedimiento almacenado en PL/SQL es perfecto para encapsular esta lógica de negocio y asegurar que las actualizaciones y los registros de auditoría se realicen de manera consistente.

```plsql
CREATE OR REPLACE PROCEDURE update_employee_salary (
	p_employee_id IN NUMBER, 
	p_new_salary IN NUMBER, 
	p_updated_by IN VARCHAR2
) IS
	v_old_salary NUMBER;
BEGIN
	-- Obtener el salario actual del empleado
SELECT salary INTO v_old_salary
FROM employees
WHERE employee_id = P_employee_id;

-- Actualizar el salario del empleado
UPDATE employees
SET salary = p_new_salary
WHERE employee_id = p_employee_id;

-- Insortar un registro en la tabla de auditoría
INSERT INTO salary_audit (
	audit_id, employee_id, old_salary, new_salary, change_date, updated_by
) VALUES (
	salary_audit_seq.NEXTVAL, -- si secuencia para el ID de auditoria
	p_employee_id,
	v_old_salary,
	p_new_salary,
	SYSDATE,
	p_updated_by
);

-- Confirmar la transacción
COMMIT:
```
---
