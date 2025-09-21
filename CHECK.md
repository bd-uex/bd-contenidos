# CHECK (SQL)
La restricción **CHECK** fue introducida en **SQL-92** (SQL2), que fue publicada en 1992 como una extensión significativa del estándar SQL original.

## ¿Para qué sirve CHECK?

La restricción CHECK permite **definir condiciones lógicas** que deben cumplir los valores de una o más columnas en una tabla. Es una forma de implementar **reglas de negocio** a nivel de base de datos.

## Sintaxis básica:

```sql
-- En la definición de columna
CREATE TABLE empleado (
    edad INTEGER CHECK (edad >= 18 AND edad <= 65),
    salario DECIMAL(10,2) CHECK (salario > 0)
);

-- Como restricción de tabla
CREATE TABLE producto (
    precio_venta DECIMAL(8,2),
    precio_costo DECIMAL(8,2),
    CONSTRAINT chk_precios CHECK (precio_venta > precio_costo)
);
```

## Casos de uso comunes:

**Validación de rangos:**

```sql
nota INTEGER CHECK (nota BETWEEN 0 AND 10)
```

**Valores específicos:**

```sql
sexo CHAR(1) CHECK (sexo IN ('M', 'F'))
```

**Reglas de negocio:**

```sql
CHECK (fecha_fin > fecha_inicio)
```

**Formato de datos:**

```sql
email VARCHAR(100) CHECK (email LIKE '%@%.%')
```

## Ventajas:

- **Integridad de datos** garantizada a nivel de DBMS
- **Centralización** de reglas de negocio
- **Prevención** de datos inconsistentes
- **Independiente** de la aplicación cliente

La implementación y sintaxis específica puede variar entre diferentes sistemas de gestión de bases de datos (MySQL, PostgreSQL, Oracle, SQL Server, etc.).