# Problema 01 - Gestión de Inventario y Demanda

## 1. Descripción del Problema

Una fábrica produce 3 tipos de productos con un inventario inicial. Cada producto tiene un costo de producción y una demanda semanal estimada. Se requiere diseñar un programa que:

- Calcule el total de ingresos esperados semanalmente.
- Determine si el inventario es suficiente para cubrir la demanda.
- Si no es suficiente, calcule la cantidad adicional que debe producirse para evitar pérdidas.
- Desarrolle el diagrama de flujo de la solución del problema.
- Desarrolle el diagrama UML de clases de la solución del problema.
- Genere el código SQL de la base de datos.
- Genere el código PHP de la solución integral del problema utilizando la arquitectura MVC.

## 2. Solución Propuesta

- **Ingresos esperados**: Se calculan multiplicando la demanda semanal por el precio de venta de cada producto.
- **Inventario suficiente**: Se verifica comparando el inventario inicial con la demanda semanal.
- **Cantidad adicional**: Si el inventario no es suficiente, se calcula como Demanda - Inventario.
- **Sistema en PHP**: Se implementa usando el patrón MVC para separar la lógica de negocio (Modelo), la presentación (Vista) y el control de flujo (Controlador).
- **Base de datos**: Inicialmente en MySQL, migrada a PostgreSQL para aprovechar sus funcionalidades.
- **Convenciones de nomenclatura**: Uso de prefijos pk* para llaves primarias y fk* para llaves foráneas.

## 3. Optimizaciones Implementadas

- **Documentación coherente**: Se ha implementado documentación PHPDoc completa en todos los archivos para mejorar la comprensión y mantenimiento del código.
- **Eliminación de código no utilizado**: Se han eliminado métodos innecesarios en los modelos y controladores para mantener el código limpio y enfocado.
- **Uso de funciones PL/pgSQL**: Se priorizan las funciones nativas de PostgreSQL para mejorar el rendimiento de operaciones complejas de cálculo.
- **Organización por categorías**: Los métodos del controlador se han organizado por funcionalidades (Productos, Inventario, Demanda, Cálculos).
- **Simplificación de modelos**: Se han simplificado los modelos para mantener solo la funcionalidad esencial que resuelve el problema planteado.

## 4. Diagrama de Flujo

```mermaid
flowchart TD
    A[Inicio] --> B[Definir productos]
    B --> C{Inventario ≥ Demanda?}
    C -- Sí --> D[Inventario es suficiente]
    C -- No --> E[Calcular cantidad adicional]
    E --> F[Mostrar cantidad adicional a producir]
    D --> G[Mostrar resultados]
    F --> G
    G --> H[Fin]
```

## 5. Diagrama UML de Clases

```mermaid
classDiagram
    class Producto {
        -String nombre
        -double costoProduccion
        -double precioVenta
        +obtener_todos() array
        +obtener_por_id(int pk_productos) array
    }

    class Inventario {
        +registrar(int fk_producto, int cantidad) string
        +obtener_todos() array
        +obtener_por_producto(int fk_producto) array
        +actualizar(int fk_producto, int cantidad) string
        +verificar_disponibilidad(int fk_producto, int cantidad) boolean
    }

    class Demanda {
        +registrar(int fk_producto, int cantidad) string
        +obtener_todos() array
        +obtener_por_producto(int fk_producto) array
        +actualizar(int fk_producto, int cantidad) string
        +calcular_ingresos_esperados() array
    }

    class Fabrica {
        +verificar_inventario() array
        +calcular_produccion_adicional() array
    }

    Producto "1" -- "1" Inventario : contiene >
    Producto "1" -- "1" Demanda : tiene >
    Fabrica ..> Inventario : utiliza >
    Fabrica ..> Demanda : utiliza >
```

## 6. Diagrama UML del Patrón MVC (PHP)

```mermaid
classDiagram
    class Conexion {
        +static conectar(): PDO
    }

    class Modelo {
        +static conectar(): PDO
    }

    class PostgreSQLModelo {
        +static calcular_ingresos_esperados(): float
        +static verificar_inventario_suficiente(): array
        +static calcular_produccion_adicional(): array
    }

    class Paginas {
        +static enlaces_paginas_modelo(string enlace): string
    }

    class Controlador {
        +static pagina(): void
        +static enlaces_paginas_controlador(): void

        %% Métodos para Producto %%
        +static listar_productos_controlador(): array

        %% Métodos para Inventario %%
        +static actualizar_inventario_controlador(): void
        +static mostrar_inventario_controlador(): array

        %% Métodos para Demanda %%
        +static registrar_demanda_controlador(): void
        +static calcular_ingresos_esperados_controlador(): array
        +static mostrar_demanda_controlador(): array

        %% Métodos para Cálculos %%
        +static verificar_inventario_suficiente_controlador(): array
        +static calcular_produccion_adicional_controlador(): array
        +static mostrar_resultados_fabricacion_controlador(): array
    }

    class Alertas {
        +static mostrar_alerta(string respuesta, array mensajes, string redireccion): void
    }

    class ProductoModelo {
        +static obtener_todos(): array
        +static obtener_por_id(int pk_productos): array|null
    }

    class InventarioModelo {
        +static registrar(int fk_producto, int cantidad): string
        +static obtener_todos(): array
        +static obtener_por_producto(int fk_producto): array|null
        +static actualizar(int fk_producto, int cantidad): string
        +static verificar_disponibilidad(int fk_producto, int cantidad): boolean
    }

    class DemandaModelo {
        +static registrar(int fk_producto, int cantidad): string
        +static obtener_todos(): array
        +static obtener_por_producto(int fk_producto): array|null
        +static actualizar(int fk_producto, int cantidad): string
        +static calcular_ingresos_esperados(): array
    }

    class FabricaModelo {
        +static verificar_inventario(): array
        +static calcular_produccion_adicional(): array
    }

    Conexion <|-- Modelo
    Conexion <|-- Paginas
    Modelo <|-- PostgreSQLModelo
    Modelo <|-- ProductoModelo
    Modelo <|-- InventarioModelo
    Modelo <|-- DemandaModelo
    Modelo <|-- FabricaModelo

    Controlador --> Modelo : Usa >
    Controlador --> Paginas : Usa >
    Controlador --> Alertas : Usa >
    Controlador --> ProductoModelo : Usa >
    Controlador --> InventarioModelo : Usa >
    Controlador --> DemandaModelo : Usa >
    Controlador --> FabricaModelo : Usa >
    FabricaModelo --> PostgreSQLModelo : Usa >
```

## 7. Esquema de la Base de Datos (PostgreSQL)

```sql
-- Tabla productos
CREATE TABLE productos (
    pk_productos SERIAL PRIMARY KEY NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    costo_produccion DECIMAL(10,2) NOT NULL,
    precio_venta DECIMAL(10,2) NOT NULL,
    hora TIME NOT NULL,
    fecha DATE NOT NULL,
    estado SMALLINT NOT NULL
);

-- Tabla inventario
CREATE TABLE inventario (
    pk_inventario SERIAL PRIMARY KEY NOT NULL,
    fk_producto INTEGER NOT NULL REFERENCES productos(pk_productos),
    cantidad INTEGER NOT NULL,
    hora TIME NOT NULL,
    fecha DATE NOT NULL,
    estado SMALLINT NOT NULL
);

-- Tabla demanda
CREATE TABLE demanda (
    pk_demanda SERIAL PRIMARY KEY NOT NULL,
    fk_producto INTEGER NOT NULL REFERENCES productos(pk_productos),
    cantidad INTEGER NOT NULL,
    hora TIME NOT NULL,
    fecha DATE NOT NULL,
    estado SMALLINT NOT NULL
);

-- Tabla producción adicional
CREATE TABLE produccion_adicional (
    pk_produccion SERIAL PRIMARY KEY NOT NULL,
    fk_producto INTEGER NOT NULL REFERENCES productos(pk_productos),
    cantidad INTEGER NOT NULL,
    hora TIME NOT NULL,
    fecha DATE NOT NULL,
    estado SMALLINT NOT NULL
);

-- Función para verificar si el inventario es suficiente
CREATE OR REPLACE FUNCTION verificar_inventario_suficiente()
RETURNS TABLE (
    producto_id INTEGER,
    producto_nombre VARCHAR,
    inventario_actual INTEGER,
    demanda_semanal INTEGER,
    es_suficiente BOOLEAN
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        p.pk_productos AS producto_id,
        p.nombre AS producto_nombre,
        i.cantidad AS inventario_actual,
        d.cantidad AS demanda_semanal,
        (i.cantidad >= d.cantidad) AS es_suficiente
    FROM
        productos p
    JOIN
        inventario i ON p.pk_productos = i.fk_producto
    JOIN
        demanda d ON p.pk_productos = d.fk_producto
    WHERE
        p.estado = 1 AND i.estado = 1 AND d.estado = 1
    ORDER BY
        p.nombre;
END;
$$ LANGUAGE plpgsql;

-- Función para calcular la producción adicional necesaria
CREATE OR REPLACE FUNCTION calcular_produccion_adicional()
RETURNS TABLE (
    producto_id INTEGER,
    producto_nombre VARCHAR,
    produccion_necesaria INTEGER,
    costo_produccion DECIMAL
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        p.pk_productos AS producto_id,
        p.nombre AS producto_nombre,
        (d.cantidad - i.cantidad) AS produccion_necesaria,
        ((d.cantidad - i.cantidad) * p.costo_produccion) AS costo_produccion
    FROM
        productos p
    JOIN
        inventario i ON p.pk_productos = i.fk_producto
    JOIN
        demanda d ON p.pk_productos = d.fk_producto
    WHERE
        p.estado = 1 AND i.estado = 1 AND d.estado = 1
        AND d.cantidad > i.cantidad
    ORDER BY
        p.nombre;
END;
$$ LANGUAGE plpgsql;

-- Función para calcular ingresos esperados
CREATE OR REPLACE FUNCTION calcular_ingresos_esperados()
RETURNS DECIMAL AS $$
DECLARE
    total_ingresos DECIMAL;
BEGIN
    SELECT
        SUM(p.precio_venta * d.cantidad) INTO total_ingresos
    FROM
        productos p
    JOIN
        demanda d ON p.pk_productos = d.fk_producto
    WHERE
        p.estado = 1 AND d.estado = 1;

    RETURN total_ingresos;
END;
$$ LANGUAGE plpgsql;
```
