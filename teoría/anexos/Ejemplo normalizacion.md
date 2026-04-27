## Dataset Propuesto: "Sistema de Gestión de Librería Universitaria"

Te presento una tabla inicial desnormalizada con datos típicos que podrías encontrar o crear basándote en datasets públicos:

### Tabla Original (Sin Normalizar)

```csv
LibroID, ISBN, Titulo, Autores, Generos, Editorial, AñoPublicacion, Precio, Stock, ClienteID, NombreCliente, EmailCliente, DireccionCliente, FechaPedido, CantidadPedida, EstadoPedido, MetodoPago
1, 978-0134685991, "Database Systems", "Elmasri,Navathe", "Informática,Educación", "Pearson", 2015, 89.99, 15, C001, "Juan Pérez", "juan@email.com", "Calle Principal 123", 2024-01-15, 2, "Enviado", "Tarjeta"
2, 978-0072465631, "Database Management Systems", "Ramakrishnan,Gehrke", "Informática,Base de Datos", "McGraw-Hill", 2003, 75.50, 8, C002, "María García", "maria@email.com", "Av. Universidad 456", 2024-01-16, 1, "Procesando", "PayPal"
1, 978-0134685991, "Database Systems", "Elmasri,Navathe", "Informática,Educación", "Pearson", 2015, 89.99, 15, C003, "Carlos López", "carlos@email.com", "Plaza Mayor 789", 2024-01-17, 1, "Entregado", "Transferencia"
3, 978-0321369574, "Introduction to Algorithms", "Cormen,Leiserson,Rivest,Stein", "Informática,Algoritmos,Matemáticas", "MIT Press", 2009, 95.00, 20, C001, "Juan Pérez", "juan@email.com", "Calle Principal 123", 2024-01-18, 1, "Procesando", "Tarjeta"
```

| LibroID | ISBN           | Titulo                      | Autores                       | Generos                            | Editorial   | AñoPublicacion | Precio | Stock | ClienteID | NombreCliente | EmailCliente     | DireccionCliente    | FechaPedido | CantidadPedida | EstadoPedido | MetodoPago    | 
| ------- | -------------- | --------------------------- | ----------------------------- | ---------------------------------- | ----------- | -------------- | ------ | ----- | --------- | ------------- | ---------------- | ------------------- | ----------- | -------------- | ------------ | ------------- |
| 1       | 978-0134685991 | Database Systems            | Elmasri,Navathe               | Informática,Educación              | Pearson     | 2015           | 89.99  | 15    | C001      | Juan Pérez    | juan@email.com   | Calle Principal 123 | 2024-01-15  | 2              | Enviado      | Tarjeta       |
| 2       | 978-0072465631 | Database Management Systems | Ramakrishnan,Gehrke           | Informática,Base de Datos          | McGraw-Hill | 2003           | 75.50  | 8     | C002      | María García  | maria@email.com  | Av. Universidad 456 | 2024-01-16  | 1              | Procesando   | PayPal        |
| 1       | 978-0134685991 | Database Systems            | Elmasri,Navathe               | Informática,Educación              | Pearson     | 2015           | 89.99  | 15    | C003      | Carlos López  | carlos@email.com | Plaza Mayor 789     | 2024-01-17  | 1              | Entregado    | Transferencia |
| 3       | 978-0321369574 | Introduction to Algorithms  | Cormen,Leiserson;Rivest,Stein | Informática,Algoritmos,Matemáticas | MIT Press   | 2009           | 95.00  | 20    | C001      | Juan Pérez    | juan@email.com   | Calle Principal 123 | 2024-01-18  | 1              | Procesando   | Tarjeta       |

### Problemas y Anomalías Identificadas

Este dataset presenta las tres anomalías clásicas que la normalización busca resolver: anomalías de inserción, actualización y eliminación:

1. **Redundancia de datos**: Información del libro se repite en cada pedido
2. **Campos multivaluados**: Autores y Géneros contienen múltiples valores separados por comas
3. **Anomalía de inserción**: No se puede registrar un libro nuevo sin un pedido
4. **Anomalía de actualización**: Si cambia el precio de un libro, hay que actualizar múltiples filas
5. **Anomalía de eliminación**: Si se elimina el único pedido de un libro, se pierde toda su información

### Directrices

1. Los **atributos de diferentes entidades no deben mezclarse en la misma relación**
2. Diseñe un **esquema que no sufra anomalías**. Una de las **maneras de evitar errores es representar un hecho solo una vez** en la base de datos.
3. Las relaciones deben diseñarse para que sus **tuplas tengan el mínimo número de valores NULL**. Los **atributos que frecuentemente son NULL se pueden colocar en relaciones separadas**.