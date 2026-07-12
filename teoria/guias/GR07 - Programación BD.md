# Tema 7 — Programación de Bases de Datos
## Guía de repaso

> **Idea central del tema:** las aplicaciones necesitan guardar y recuperar datos que sobreviven a su ejecución (persistencia). Para ello, el código de la aplicación debe comunicarse con un SGBD, y existen **cuatro alternativas** para hacerlo, con distintos niveles de abstracción: SQL embebido, bibliotecas de código (APIs), frameworks ORM y procedimientos almacenados.

---

## 1. Persistencia de datos

La **persistencia** es la capacidad de un sistema para mantener la información disponible **más allá del proceso que la creó**: los datos siguen existiendo aunque la aplicación termine o el sistema se apague.

Los datos deben "persistir" a pesar de:

- **El ciclo de vida de la aplicación** (p. ej., el carrito de la compra se conserva entre sesiones).
- **La evolución del esquema** de la base de datos.
- **Cambios de hardware o migraciones** entre sistemas.
- **La rotación de los desarrolladores** que crearon el sistema.

Principales tecnologías de persistencia: **sistemas de archivos** (almacenamiento básico), **bases de datos relacionales** (datos estructurados con integridad transaccional), **bases de datos NoSQL** (flexibilidad y escalabilidad), **caché en memoria** (velocidad) y **almacenamiento de objetos** en la nube (grandes archivos no estructurados).

---

## 2. Aplicaciones de bases de datos

Una **aplicación de base de datos** es software que permite a los **usuarios finales** interactuar con los datos de un SGBD a través de una interfaz amigable y **transacciones predefinidas** ("añadir producto", "consultar saldo"), sin ver el SQL subyacente.

### Tres formas de interactuar con una base de datos

| Método | Quién lo usa | Para qué |
|---|---|---|
| SQL directo (cliente interactivo: `psql`, pgAdmin…) | Desarrolladores y administradores | Consultas *ad hoc*, mantenimiento, pruebas |
| Scripts de comandos SQL | Desarrolladores y administradores | Automatizar tareas: crear esquemas, cargar datos |
| Aplicaciones de BD | **Usuarios finales** | Transacciones predefinidas vía interfaz de usuario |

### Secuencia básica de interacción programática

Prácticamente universal, sea cual sea el lenguaje o el SGBD:

1. **Abrir conexión** con el servidor (credenciales, dirección, puerto).
2. **Interactuar**: enviar consultas (`SELECT`), actualizaciones (`INSERT`, `UPDATE`, `DELETE`) u otros comandos (DDL como `CREATE TABLE`).
3. **Cerrar la conexión** — imprescindible para liberar recursos del servidor (rendimiento y escalabilidad).

---

## 3. Las cuatro alternativas de acceso a datos

| Alternativa | ¿Dónde está el SQL? | Nivel de abstracción | Situación actual |
|---|---|---|---|
| **SQL embebido** | Intercalado en el código fuente (precompilador) | Bajo | *Legacy*, en desuso |
| **Bibliotecas de código (API/driver)** | Cadenas de texto en el código (JDBC, ODBC, psycopg2) | Medio | El método más común |
| **Frameworks ORM** | Lo genera el framework automáticamente | Alto | Muy extendido |
| **Procedimientos almacenados** | Dentro del propio SGBD | — (lógica en servidor) | Casos específicos |

---

## 4. SQL embebido (Embedded SQL)

Sentencias SQL escritas **directamente dentro del código** de un lenguaje anfitrión (C, C++, COBOL), marcadas con `EXEC SQL` y vinculadas a variables del programa mediante `:variable`.

```c
EXEC SQL SELECT name, age INTO :name, :age
         FROM users WHERE id = :userId;
```

### El precompilador

Antes de compilar, un **precompilador** (proporcionado por el fabricante del SGBD):

- Escanea el código y localiza las sentencias `EXEC SQL`.
- Las **reemplaza por llamadas a funciones** de la API de la base de datos.
- Detecta **errores de sintaxis SQL** y comprueba que las variables anfitrionas están declaradas y son de tipos compatibles.

### ¿Por qué está en desuso?

- **Seguridad**: propenso a errores del desarrollador → más vulnerable a inyección SQL.
- **Mantenibilidad**: alta rigidez y acoplamiento con el esquema.
- **Sin separación de responsabilidades**: mezcla lógica de negocio y acceso a datos.
- Fuerte **dependencia del precompilador** del proveedor.

Aún se encuentra en: sistemas heredados (banca), aplicaciones de muy alto rendimiento y sistemas embebidos con recursos limitados.

---

## 5. Bibliotecas de código: JDBC

El programador escribe el SQL como **cadenas de texto** y usa una biblioteca estándar para enviarlo al SGBD y procesar los resultados. Ejemplos: **JDBC** (Java), **ODBC** (.NET/C++), **psycopg2** (Python).

### Componentes fundamentales de JDBC

| Componente | Función |
|---|---|
| `DriverManager` | Gestiona los drivers y **establece conexiones** |
| `Connection` | Representa la conexión; ejecuta consultas y gestiona transacciones |
| `Statement` / `PreparedStatement` / `CallableStatement` | Consultas simples / parametrizadas / procedimientos almacenados |
| `ResultSet` | Contiene los **resultados** de la consulta |

### Ejemplo básico

```java
Connection conn = DriverManager.getConnection("jdbc:sqlite:my_database.db");
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM users");

while (rs.next()) {
    System.out.println(rs.getString("name") + " - " + rs.getInt("age"));
}

rs.close(); stmt.close(); conn.close();  // ¡Cerrar siempre los recursos!
```

### ⚠️ Statement vs PreparedStatement: inyección SQL

**Concepto clave del tema.** Construir consultas concatenando cadenas con entrada del usuario es **vulnerable a inyección SQL**:

```java
// PELIGROSO
String sql = "SELECT * FROM users WHERE username = '" + userInput + "'";
```

Si el usuario introduce `'; DROP TABLE users; --`, la consulta resultante **ejecuta el borrado de la tabla**:

```sql
SELECT * FROM users WHERE username = ''; DROP TABLE users; --'
```

**`PreparedStatement`** resuelve el problema usando **marcadores de posición (`?`)**:

```java
String sql = "SELECT * FROM users WHERE username = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, userInput);   // la entrada se trata como DATO, nunca como código
ResultSet rs = pstmt.executeQuery();
```

Ahora la misma entrada maliciosa simplemente busca un usuario cuyo nombre sea esa cadena. Ventaja adicional: las consultas parametrizadas pueden ser **precompiladas y cacheadas** por el SGBD → mejor rendimiento en consultas frecuentes.

### Ventajas y desventajas de JDBC

| ✅ Ventajas | ❌ Desventajas |
|---|---|
| Independencia del SGBD (solo cambia el driver) | Mucho **código repetitivo** (conexiones, resultados, errores) |
| Control total sobre el SQL y las conexiones | No detecta errores de SQL en compilación |
| Estándar de Java, sin librerías externas | Riesgo de **fugas de recursos** si no se cierra todo |
| Soporte de transacciones | Abstracción de bajo nivel: hay que escribir SQL a mano |
| Flexible: SQL dinámico, parametrizado, procedimientos | Difícil de mantener y escalar en aplicaciones grandes |

---

## 6. El desajuste de impedancias (impedance mismatch)

Es la **dificultad de mapear el modelo relacional** (tablas, filas, columnas) **con el modelo orientado a objetos** (clases, objetos, herencia, referencias). Ambos representan los datos de forma fundamentalmente distinta.

Manifestaciones concretas al trabajar con JDBC:

1. **Conversión de datos manual.** JDBC no mapea automáticamente filas a objetos: el desarrollador debe leer cada columna del `ResultSet` y construir el objeto a mano (y al revés para guardar). Resultado: código repetitivo y propenso a errores.

2. **Relaciones complejas.** Para una relación 1:N (un `Student` con su lista de `Grade`) hay que escribir consultas separadas y asociar los resultados manualmente.

3. **Falta de herencia.** Las BD relacionales no soportan herencia de forma nativa. Hay que simularla: una tabla por subclase, o una única tabla con todos los campos.

4. **Valores nulos y tipos.** Si una columna es `NULL`, `getInt()` devuelve `0` — puede no ser lo que la lógica de negocio espera. Hay que comprobar explícitamente con `wasNull()`.

> **La conclusión importante:** el desajuste de impedancias es *el problema* que motiva los patrones de diseño y, sobre todo, los frameworks ORM del siguiente apartado.

---

## 7. Patrones de diseño para el acceso a datos

Los patrones de diseño son **soluciones probadas a problemas recurrentes**. El problema aquí: *separar el código SQL del resto del código de la aplicación*.

### DAO (Data Access Object)

Una **clase por entidad** (`UserDAO` para `User`) que ofrece una interfaz uniforme de operaciones **CRUD** (`save`, `findById`, `update`, `delete`) sin exponer los detalles de la BD. Separa lógica de negocio y lógica de acceso a datos → más fácil de mantener y probar.

```java
public interface UserDAO {
    void save(User user);
    User findById(int id);
    void update(User user);
    void delete(int id);
}

// Uso:
UserDAO userDAO = new UserDAOImpl(connection);
userDAO.save(new User(0, "John Doe", "john@example.com"));
User u = userDAO.findById(1);
```

### Active Record

La **propia entidad** contiene sus métodos de persistencia: cada objeto es responsable de guardarse, actualizarse y borrarse a sí mismo.

```java
public class User {
    private int id;
    private String name;

    public void save()  { /* guardar este usuario en la BD */ }
    public static User find(int id) { /* buscar por ID */ }
}

// Uso:
User newUser = new User(0, "John Doe", "john@example.com");
newUser.save();
User found = User.find(newUser.getId());
```

### Query Object

**Encapsula una consulta compleja en un objeto** que se configura con criterios de búsqueda (interfaz fluida) y luego se ejecuta.

```java
List<User> users = new UserQuery()
        .withLastName("Smith")
        .onlyActive()
        .execute();
```

### El patrón de capas

Arquitectura que organiza la aplicación en niveles horizontales — típicamente **Presentación → Lógica de Negocio → Acceso a Datos** — donde cada capa solo se comunica con la adyacente. Promueve la **separación de intereses**: cambiar el motor de BD (capa inferior) no afecta a las capas superiores. Los patrones anteriores (DAO, etc.) suelen vivir en la capa de acceso a datos.

| Patrón | Idea en una frase |
|---|---|
| DAO | Clase separada dedicada al acceso a datos de cada entidad |
| Active Record | La entidad se persiste a sí misma |
| Query Object | La consulta compleja es un objeto configurable |
| Capas | Presentación / Negocio / Datos, comunicación solo entre capas adyacentes |

---

## 8. Frameworks ORM

### ¿Qué es un framework?

Un **conjunto de herramientas, estructuras y reglas predefinidas** para construir aplicaciones de forma más eficiente. Establece una arquitectura base con componentes reutilizables.

- **Analogía:** construir con módulos prefabricados en vez de con ladrillos sueltos. Ganamos rapidez y reutilizamos experiencia probada, pero perdemos algo de libertad (solo podemos construir lo que los módulos permiten componer).
- **No confundir con una biblioteca (API):** una biblioteca la *llamas tú*; un framework impone la estructura y *te llama a ti*.

### ORM (Object-Relational Mapping)

Capa de abstracción que **elimina la necesidad de escribir SQL manualmente**: es la respuesta directa al desajuste de impedancias.

El mapeo básico: **tabla → clase**, **fila → objeto**. Al hacer `cliente.save()`, el ORM genera el `INSERT` automáticamente.

**Ventajas:**

- Desarrollo más rápido: sin SQL manual, sincronización automática memoria ↔ BD.
- Código más limpio y mantenible.
- **Independencia del SGBD.**
- Gestión automática de transacciones (propiedades ACID).

**Contrapartida:** puede sacrificar rendimiento en consultas muy complejas.

**Frameworks ORM populares:** Hibernate (Java), Entity Framework (.NET), SQLAlchemy (Python).

### Ejemplo: Hibernate

Las clases de entidad se configuran con **anotaciones de Java**, que integran el mapeo directamente en el código (en lugar de XML externo):

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    // constructor vacío (requerido), getters y setters...
}
```

```java
Session session = factory.getCurrentSession();
session.beginTransaction();

User newUser = new User("John Doe", "john@example.com");
session.save(newUser);                 // Hibernate genera el INSERT

session.getTransaction().commit();
```

```java
// Recuperar por ID: Hibernate genera el SELECT
User retrieved = session.get(User.class, newUser.getId());
```

Hibernate **lee las anotaciones** para saber cómo mapear cada clase a su tabla y genera automáticamente el SQL de cada operación (`save`, `update`, `delete`, `get`).

---

## 9. Procedimientos almacenados (Stored Procedures)

La lógica **no está en la aplicación, sino dentro del motor de la base de datos**: rutinas escritas en SQL (o extensiones como PL/SQL) que se guardan y ejecutan en el servidor. El estándar SQL los denomina **SQL/PSM** (Persistent Stored Modules).

- **Cómo funciona:** la aplicación llama al procedimiento por su nombre y la BD hace el trabajo internamente, devolviendo solo el resultado.
- **Cuándo usarlos:** cuando **varias aplicaciones comparten la misma lógica** sobre la BD; también reducen la transferencia de datos cliente ↔ servidor.
- **Ventajas:** rendimiento excelente (código precompilado) y seguridad.
- **Inconveniente clave:** la lógica de negocio queda **atada al SGBD** → difícil de migrar a otro motor.

### PL/SQL (Oracle)

Lenguaje procedural que extiende SQL con variables, estructuras de control, procedimientos y paquetes.

**Casos de uso:** automatización de tareas administrativas, validaciones y reglas de negocio compartidas, procesamiento en lote, *triggers* (acciones automáticas ante eventos).

**Ejemplo — actualizar salario dejando rastro de auditoría** (una operación que debe hacerse siempre de forma consistente, la use quien la use):

```plsql
CREATE OR REPLACE PROCEDURE update_employee_salary (
    p_employee_id IN NUMBER,
    p_new_salary  IN NUMBER,
    p_updated_by  IN VARCHAR2
) IS
    v_old_salary NUMBER;
BEGIN
    SELECT salary INTO v_old_salary
    FROM employees WHERE employee_id = p_employee_id;

    UPDATE employees SET salary = p_new_salary
    WHERE employee_id = p_employee_id;

    INSERT INTO salary_audit (audit_id, employee_id, old_salary,
                              new_salary, change_date, updated_by)
    VALUES (salary_audit_seq.NEXTVAL, p_employee_id, v_old_salary,
            p_new_salary, SYSDATE, p_updated_by);

    COMMIT;
END;
```

---

## 10. Resumen final: ¿qué alternativa elegir?

| Criterio | SQL embebido | JDBC (API) | ORM | Proc. almacenados |
|---|---|---|---|---|
| Control sobre el SQL | Alto | **Total** | Bajo (lo genera el framework) | Total (en servidor) |
| Velocidad de desarrollo | Baja | Media | **Alta** | Media |
| Riesgo de inyección SQL | Alto | Bajo si se usa `PreparedStatement` | Bajo | Bajo |
| Portabilidad entre SGBD | Baja | Alta | **Alta** | **Baja** |
| ¿Resuelve el desajuste de impedancias? | No | No (manual) | **Sí** | N/A |
| Uso hoy | Legacy | Muy común | Muy común | Casos específicos |

**Las cinco ideas que debes llevarte:**

1. La **persistencia** permite que los datos sobrevivan a la ejecución del programa; el patrón universal es *conectar → interactuar → cerrar*.
2. Hay **cuatro alternativas** de acceso a datos, ordenadas por nivel de abstracción creciente sobre el SQL.
3. **Nunca concatenes entrada de usuario en una consulta**: usa siempre consultas parametrizadas (`PreparedStatement`) para evitar la inyección SQL.
4. El **desajuste de impedancias** (tablas ↔ objetos) es el problema de fondo que motiva los patrones DAO/Active Record/Query Object y que los **ORM** resuelven automatizando el mapeo.
5. Los **procedimientos almacenados** trasladan la lógica al servidor: gran rendimiento y consistencia, a costa de portabilidad.