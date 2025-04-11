## BASE DE DATOS

```postgresql
-- Eliminar la base de datos si ya existe y crear una nueva

DROP DATABASE IF EXISTS techzone;
CREATE DATABASE techzone;

-- INGRESAR A LA BASE DE DATOS

\c techzone

-- TABLA CATEGORIA

CREATE TABLE categorias (
    categoria_id SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE,
    descripcion TEXT,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- TABLA PROVEDORES

CREATE TABLE provedores (
    provedor_id SERIAL PRIMARY KEY,
    nombre_empresa VARCHAR(100) NOT NULL,
    contacto_principal VARCHAR(100) NOT NULL,
    ruc VARCHAR(11) UNIQUE NOT NULL,
    telefono VARCHAR(15) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    direccion TEXT NOT NULL,
    ciudad VARCHAR(50) NOT NULL,
    fecha_registro DATE DEFAULT CURRENT_DATE
);

-- TABLA PRODUCTOS

CREATE TABLE productos (
    producto_id SERIAL PRIMARY KEY,
    sku VARCHAR(20) UNIQUE NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    precio NUMERIC(12,2) NOT NULL CHECK (precio > 0),
    costo NUMERIC(10,2) NOT NULL,
    stock INT NOT NULL CHECK (stock >= 0) DEFAULT 0,
    stock_minimo INT NOT NULL DEFAULT 5,
    categoria_id INT NOT NULL REFERENCES categorias(categoria_id),
    provedor_id INT NOT NULL REFERENCES provedores(provedor_id),
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- TABLA CLIENTES

CREATE TABLE clientes (
    cliente_id SERIAL PRIMARY KEY,
    dni VARCHAR(8) UNIQUE NOT NULL,
    nombre VARCHAR(50) NOT NULL,
    apellido VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    telefono VARCHAR(15) NOT NULL,
    direccion TEXT,
    fecha_registro DATE DEFAULT CURRENT_DATE
);

-- TABLA VENTAS

CREATE TABLE ventas (
    venta_id SERIAL PRIMARY KEY,
    codigo_venta VARCHAR(20) UNIQUE NOT NULL,
    cliente_id INT NOT NULL REFERENCES clientes(cliente_id),
    fecha_hora TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    subtotal NUMERIC(12,2) NOT NULL DEFAULT 0,
    impuestos NUMERIC(10,2) NOT NULL DEFAULT 0,
    descuento NUMERIC(10,2) NOT NULL DEFAULT 0,
    total NUMERIC(12,2) NOT NULL DEFAULT 0,
    estado VARCHAR(20) NOT NULL DEFAULT 'COMPLETADA',
    metodo_pago VARCHAR(30) NOT NULL
);

-- TABLA DETALLE DE VENTAS

CREATE TABLE detalle_ventas (
    detalle_id SERIAL PRIMARY KEY,
    venta_id INT NOT NULL REFERENCES ventas(venta_id) ON DELETE CASCADE,
    producto_id INT NOT NULL REFERENCES productos(producto_id),
    cantidad INT NOT NULL CHECK (cantidad > 0),
    precio_unitario NUMERIC(10,2) NOT NULL CHECK (precio_unitario > 0),
    subtotal NUMERIC(12,2) GENERATED ALWAYS AS (cantidad * precio_unitario) STORED
);

```



## 1. Inserción de categorias

```postgresql
INSERT INTO categorias (nombre, descripcion) VALUES 
('Laptops', 'Computadoras portátiles de diferentes marcas y caracteristicas'),
('Teléfonos', 'Smartphones y teléfonos móviles'),
('Componentes', 'Componentes para computadoras como RAM, discos duros, etc.'),
('Accesorios', 'Accesorios para computadoras y dispositivos móviles'),
('Periféricos', 'Teclados, ratones, pantallas y otros accesorios'),
('Software', 'Programas y sistemas operativos'),
('Redes', 'Equipos de red como routers y switches de varios tipos'),
('Almacenamiento', 'Discos duros, SSD y unidades externas'),
('Impresión', 'Impresoras, fotocopiadoras y consumibles'),
('Audio', 'Auriculares, altavoces y equipos de sonido'),
('Gaming', 'Productos especializados para videojuegos'),
('Tablets', 'Tablets y dispositivos familiares'),
('Cámaras', 'Cámaras de fotografía y de video'),
('Seguridad', 'Productos de seguridad informática para navegar seguro'),
('Servidores', 'Equipos y accesorios para servidores');
```

## 2. Inserción de provedores

```postgresql
INSERT INTO provedores (
    nombre_empresa, 
    contacto_principal, 
    ruc, 
    telefono, 
    email, 
    direccion, 
    ciudad
) VALUES
('Tech Suppliers Inc', 'Contacto TS', '11111111111', '5551234567', 'info@techsupplier.com', '1 Tech Ave', 'Silicon Valley'),
('Global Electronics', 'Contacto GE', '22222222222', '81312345678', 'info@gec.org', '2 Circuit Rd', 'Tokyo'),
('Digital Components', 'Contacto DC', '33333333333', '82298765432', 'info@digitalcomp.com', '3 Binary Blvd', 'Seoul'),
('Mobile World', 'Contacto MW', '44444444444', '867558765432', 'orders@mobileworld.com', '4 Smart St', 'Shenzhen'),
('PC Parts Co', 'Contacto PC', '55555555555', '5129876543', 'sales@pcparts.com', '5 Processor Ln', 'Austin'),
('Software Solutions', 'Contacto SS', '66666666666', '80678945612', 'contact@softsol.com', '6 Code Ave', 'Bangalore'),
('Network Gear', 'Contacto NG', '77777777777', '6176543210', 'sales@networkgear.com', '7 Packet Dr', 'Boston'),
('Storage Masters', 'Contacto SM', '88888888888', '4081237890', 'info@storagem.com', '8 Disk Way', 'San Jose'),
('Print Tech', 'Contacto PT', '99999999999', '5034567890', 'orders@printtech.com', '9 Ink St', 'Portland'),
('Audio Experts', 'Contacto AE', '10101010101', '2137894561', 'sales@audioexp.com', '10 Sound Ave', 'Los Angeles'),
('Gaming Pro', 'Contacto GP', '12121212121', '2066549873', 'info@gamingpro.com', '11 Game Rd', 'Seattle'),
('Tablet World', 'Contacto TW', '13131313131', '88621234567', 'sales@tabletworld.com', '12 Screen St', 'Taipei'),
('Camera Central', 'Contacto CC', '14141414141', '2127891234', 'contact@cameracentral.com', '13 Lens Ave', 'New York'),
('Secure Systems', 'Contacto SS', '15151515151', '2024567891', 'sales@securesys.com', '14 Lock Dr', 'Washington'),
('Server Solutions', 'Contacto SS', '16161616161', '3129876543', 'info@serversol.com', '15 Data Ctr Blvd', 'Chicago');

```

## 3. Inserción de productos

```postgresql
INSERT INTO productos (
    sku, 
    nombre, 
    descripcion, 
    precio, 
    costo, 
    stock, 
    stock_minimo, 
    categoria_id, 
    provedor_id
) VALUES
('LPELX1', 'Laptop Elite X1', 'Laptop ultradelgada con procesador i7 y 16GB RAM', 1299.99, 1000.00, 15, 5, 1, 1),
('SPGS23', 'Smartphone Galaxy S23', 'Último modelo de smartphone con cámara de 108MP', 999.99, 750.00, 25, 10, 2, 4),
('SSD1TBN', 'SSD 1TB NVMe', 'Disco sólido de alta velocidad 1TB', 89.99, 60.00, 50, 20, 3, 3),
('TECRGB', 'Teclado mecánico RGB', 'Teclado gaming con switches mecánicos y retroiluminación RGB', 79.99, 50.00, 30, 15, 4, 5),
('WIN11P', 'Windows 11 Pro', 'Licencia de Windows 11 Professional', 199.99, 120.00, 100, 50, 5, 6),
('RTWIFI6', 'Router WiFi 6', 'Router de última generación con WiFi 6', 149.99, 100.00, 20, 10, 6, 7),
('MON4K27', 'Monitor 27" 4K', 'Monitor UHD 4K con panel IPS', 349.99, 250.00, 18, 8, 4, 1),
('AURICNC', 'Auriculares inalámbricos', 'Auriculares con cancelación de ruido y Bluetooth', 129.99, 80.00, 35, 15, 7, 10),
('CONSGPRO', 'Consola Gaming Pro', 'Consola de última generación con 1TB SSD', 499.99, 350.00, 12, 6, 8, 11),
('TABPRO10', 'Tablet Pro 10.5"', 'Tablet con lápiz digital y pantalla 2K', 329.99, 220.00, 22, 10, 9, 12),
('CAMDSLR', 'Cámara DSLR Profesional', 'Cámara réflex con lente 24-70mm', 899.99, 600.00, 8, 4, 10, 13),
('FWALL', 'Firewall Empresarial', 'Dispositivo de seguridad para redes empresariales', 799.99, 500.00, 5, 3, 11, 14),
('SRV1U', 'Servidor Rack 1U', 'Servidor empresarial con 32GB RAM y 2TB SSD', 2499.99, 1800.00, 4, 2, 12, 15),
('MSERG', 'Mouse ergonómico', 'Mouse inalámbrico con diseño ergonómico', 39.99, 25.00, 45, 20, 4, 5),
('IMPLAS', 'Impresora láser', 'Impresora láser de alta velocidad', 199.99, 130.00, 10, 5, 13, 9);
```

## 4. Inserción de clientes

```postgresql
INSERT INTO clientes (
    dni,
    nombre, 
    apellido, 
    email, 
    telefono, 
    direccion
) VALUES
('12345678', 'Nilson', 'Pérez', 'nilson.perez@gmail.com', '5551234567', 'Calle Principal 123, Ciudad'),
('23456789', 'Bayter', 'Gómez', 'bayter.gomez@gmail.com', '5552345678', 'Avenida Central 456, Ciudad'),
('34567890', 'Yesid', 'López', 'yesid.lopez@gmail.com', '5553456789', 'Boulevard Norte 789, Ciudad'),
('45678901', 'Eduardo', 'Martínez', 'eduardo.martinez@gmail.com', '5554567890', 'Calle Sur 321, Ciudad'),
('56789012', 'Luis', 'Talero', 'luis.talero@gmail.com', '5555678901', 'Avenida Este 654, Ciudad'),
('67890123', 'Laura', 'Hernández', 'laura.hernandez@gmail.com', '5556789012', 'Avenida Oeste 987, Ciudad'),
('78901234', 'Pedro', 'Garcia', 'pedro.garcia@gmail.com', '5557890123', 'Calle Secundaria 159, Ciudad'),
('89012345', 'Sofía', 'Diaz', 'sofia.diaz@gmail.com', '5558901234', 'Avenida Principal 753, Ciudad'),
('90123456', 'Jorge', 'Sanchez', 'jorge.sanchez@gmail.com', '5559012345', 'Calle Central 951, Ciudad'),
('01234567', 'Marta', 'Fernández', 'marta.fernandez@gmail.com', '5550123456', 'Calle Norte 357, Ciudad'),
('11223344', 'David', 'Corzo', 'david.corzo@gmail.com', '5551237890', 'Avenida Sur 852, Ciudad'),
('22334455', 'Elena', 'Álvarez', 'elena.alvarez@gmail.com', '5552348901', 'Centro Este 456, Ciudad'),
('33445566', 'Pablo', 'Romero', 'pablo.romero@gmail.com', '5553459012', 'Calle Oeste 753, Ciudad'),
('44556677', 'Stiven', 'Torres', 'stiven.torres@gmail.com', '5554560123', 'Avenida Norte 159, Ciudad'),
('55667788', 'Raúl', 'Jiménez', 'raul.jimenez@gmail.com', '5555671234', 'Avenida del Sur 357, Ciudad');
```

## 5. Inserción de venta

```postgresql
INSERT INTO ventas (
    codigo_venta,
    cliente_id,
    fecha_hora,
    subtotal,
    impuestos,
    descuento,
    total,
    metodo_pago,
    estado
) VALUES
('VTA-20230115-001', 1, '2023-01-15 10:30:00', 1101.69, 198.30, 0.00, 1299.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230116-002', 2, '2023-01-16 14:15:00', 915.24, 164.74, 0.00, 1079.98, 'TARJETA', 'COMPLETADA'),
('VTA-20230117-003', 3, '2023-01-17 16:45:00', 364.39, 65.59, 0.00, 429.98, 'TRANSFERENCIA', 'COMPLETADA'),
('VTA-20230205-004', 4, '2023-02-05 11:20:00', 296.60, 53.39, 0.00, 349.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230210-005', 5, '2023-02-10 09:30:00', 1525.41, 274.57, 0.00, 1799.98, 'TARJETA', 'COMPLETADA'),
('VTA-20230215-006', 6, '2023-02-15 13:45:00', 423.72, 76.27, 0.00, 499.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230301-007', 7, '2023-03-01 15:10:00', 110.16, 19.83, 0.00, 129.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230310-008', 8, '2023-03-10 10:00:00', 76.26, 13.73, 0.00, 89.99, 'TARJETA', 'COMPLETADA'),
('VTA-20230315-009', 9, '2023-03-15 12:30:00', 677.96, 122.03, 0.00, 799.99, 'TRANSFERENCIA', 'COMPLETADA'),
('VTA-20230405-010', 10, '2023-04-05 14:20:00', 279.65, 50.34, 0.00, 329.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230410-011', 11, '2023-04-10 16:00:00', 169.48, 30.51, 0.00, 199.99, 'TARJETA', 'COMPLETADA'),
('VTA-20230415-012', 12, '2023-04-15 10:45:00', 33.89, 6.10, 0.00, 39.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230501-013', 13, '2023-05-01 11:30:00', 2118.64, 381.35, 0.00, 2499.99, 'TRANSFERENCIA', 'COMPLETADA'),
('VTA-20230510-014', 14, '2023-05-10 13:15:00', 127.11, 22.88, 0.00, 149.99, 'EFECTIVO', 'COMPLETADA'),
('VTA-20230515-015', 15, '2023-05-15 15:00:00', 67.79, 12.20, 0.00, 79.99, 'TARJETA', 'COMPLETADA');

```

## 6. Inserción detalles de ventas

```postgresql
INSERT INTO detalle_ventas (
    venta_id,
    producto_id,
    cantidad,
    precio_unitario
) VALUES
(1, 1, 1, 1299.99),
(2, 2, 1, 999.99),
(2, 3, 1, 89.99),
(3, 4, 1, 79.99),
(3, 7, 1, 349.99),
(4, 7, 1, 349.99),
(5, 1, 1, 1299.99),
(5, 9, 1, 499.99),  
(7, 8, 1, 129.99),
(8, 3, 1, 89.99),
(9, 12, 1, 799.99),
(10, 10, 1, 329.99),
(11, 15, 1, 199.99),
(12, 14, 1, 39.99),
(13, 13, 1, 2499.99),
(14, 6, 1, 149.99),
(15, 4, 1, 79.99);
```

```

```

```

```

## CONSULTAS

1. Listar los productos con stock menor a 5 unidades. 

   ```postgresql
   SELECT 
       p.sku AS codigo,
       p.nombre AS producto,
       p.stock AS unidades,
       p.stock_minimo AS minimo_requerido,
       pr.nombre_empresa AS proveedor,
       CASE 
           WHEN p.stock = 0 THEN 'AGOTADO'
           WHEN p.stock < 3 THEN 'CRÍTICO'
           ELSE 'BAJO'
       END AS estado
   FROM productos p
   JOIN provedores pr ON p.provedor_id = pr.provedor_id
   WHERE p.stock < 5
   ORDER BY p.stock ASC;
   ```

   

2. Calcular ventas totales de un mes específico. 

   ```postgresql
   SELECT 
       v.codigo_venta,
       TO_CHAR(v.fecha_hora, 'DD/MM/YYYY HH24:MI') AS fecha_venta,
       c.nombre || ' ' || c.apellido AS cliente,
       COUNT(dv.producto_id) AS productos_comprados,
       v.total AS monto_total,
       v.metodo_pago
   FROM ventas v
   JOIN clientes c ON v.cliente_id = c.cliente_id
   JOIN detalle_ventas dv ON v.venta_id = dv.venta_id
   WHERE v.fecha_hora >= CURRENT_DATE - INTERVAL '3 months'
   GROUP BY v.codigo_venta, v.fecha_hora, c.nombre, c.apellido, v.total, v.metodo_pago
   ORDER BY v.fecha_hora DESC;
   ```

   

3. Obtener el cliente con más compras realizadas. 

   ```postgresql
   SELECT 
       c.dni,
       c.nombre || ' ' || c.apellido AS cliente,
       COUNT(v.venta_id) AS compras_realizadas,
       SUM(v.total) AS total_gastado,
       ROUND(SUM(v.total)/COUNT(v.venta_id), 2) AS ticket_promedio,
       MAX(v.fecha_hora) AS ultima_compra
   FROM clientes c
   JOIN ventas v ON c.cliente_id = v.cliente_id
   GROUP BY c.dni, c.nombre, c.apellido
   ORDER BY total_gastado DESC
   LIMIT 5;
   ```

   

4. Listar los 5 productos más vendidos. 

   ```postgresql
   SELECT 
       cat.nombre AS categoria,
       p.nombre AS producto,
       SUM(dv.cantidad) AS unidades_vendidas,
       SUM(dv.subtotal) AS ingresos_generados,
       ROUND(SUM(dv.subtotal)/SUM(dv.cantidad), 2) AS precio_promedio
   FROM detalle_ventas dv
   JOIN productos p ON dv.producto_id = p.producto_id
   JOIN categorias cat ON p.categoria_id = cat.categoria_id
   GROUP BY cat.nombre, p.nombre
   ORDER BY unidades_vendidas DESC
   LIMIT 10;
   ```

   

5. Consultar ventas realizadas en un rango de fechas de tres Días y un Mes. 

   ```postgresql
   SELECT 
       TO_CHAR(v.fecha_hora, 'YYYY-MM') AS mes,
       COUNT(v.venta_id) AS cantidad_ventas,
       SUM(v.total) AS ventas_totales,
       LAG(COUNT(v.venta_id), 1) OVER (ORDER BY TO_CHAR(v.fecha_hora, 'YYYY-MM')) AS ventas_mes_anterior,
       ROUND(
           (COUNT(v.venta_id) - LAG(COUNT(v.venta_id), 1) OVER (ORDER BY TO_CHAR(v.fecha_hora, 'YYYY-MM'))) / 
           LAG(COUNT(v.venta_id), 1) OVER (ORDER BY TO_CHAR(v.fecha_hora, 'YYYY-MM')) * 100, 
       2) AS crecimiento_porcentual
   FROM ventas v
   WHERE v.fecha_hora >= CURRENT_DATE - INTERVAL '6 months'
   GROUP BY TO_CHAR(v.fecha_hora, 'YYYY-MM')
   ORDER BY mes;
   ```

   

6. Identificar clientes que no han comprado en los últimos 6 meses.

   ```postgresql
   
   ```

   

   ## PROCEDIMIENTOS Y FUNCIONES

   ° Un procedimiento almacenado para registrar una venta.

   ```postgresql
   CREATE OR REPLACE PROCEDURE registrar_venta(
       p_cliente_id INT,
       p_productos JSONB,
       p_metodo_pago VARCHAR(30),
       p_descuento NUMERIC(10,2) DEFAULT 0,
       OUT p_resultado TEXT,
       OUT p_codigo_venta VARCHAR(20)
   )
   LANGUAGE plpgsql
   AS $$
   DECLARE
       v_cliente_existe BOOLEAN;
       v_producto RECORD;
       v_stock_disponible INT;
       v_precio_actual NUMERIC(10,2);
       v_subtotal NUMERIC(12,2) := 0;
       v_impuestos NUMERIC(10,2);
       v_total NUMERIC(12,2);
       v_venta_id INT;
       v_items_sin_stock TEXT[];
   BEGIN
    
       SELECT EXISTS(SELECT 1 FROM clientes WHERE cliente_id = p_cliente_id) INTO v_cliente_existe;
       
       IF NOT v_cliente_existe THEN
           p_resultado := 'Error: El cliente no está registrado en el sistema';
           RETURN;
       END IF;
       
      
       IF p_metodo_pago NOT IN ('EFECTIVO', 'TARJETA', 'TRANSFERENCIA') THEN
           p_resultado := 'Error: Método de pago no válido';
           RETURN;
       END IF;
       
       
       IF p_descuento < 0 THEN
           p_resultado := 'Error: El descuento no puede ser negativo';
           RETURN;
       END IF;
       
   
       p_codigo_venta := 'V-' || TO_CHAR(CURRENT_DATE, 'YYYYMMDD') || '-' || LPAD(NEXTVAL('ventas_venta_id_seq'::REGCLASS)::TEXT, 4, '0');
       
      
       BEGIN
        
           FOR v_producto IN SELECT * FROM JSONB_ARRAY_ELEMENTS(p_productos) AS prod
           LOOP
             
               SELECT stock, precio INTO v_stock_disponible, v_precio_actual
               FROM productos 
               WHERE producto_id = (v_producto->>'producto_id')::INT;
               
          
               IF v_stock_disponible < (v_producto->>'cantidad')::INT THEN
                   v_items_sin_stock := ARRAY_APPEND(v_items_sin_stock, 
                       (SELECT nombre FROM productos WHERE producto_id = (v_producto->>'producto_id')::INT));
               END IF;
               
              
               v_subtotal := v_subtotal + (v_precio_actual * (v_producto->>'cantidad')::INT);
           END LOOP;
           
         
           IF ARRAY_LENGTH(v_items_sin_stock, 1) > 0 THEN
               p_resultado := 'Error: Stock insuficiente para: ' || ARRAY_TO_STRING(v_items_sin_stock, ', ');
               RETURN;
           END IF;
           
        
           v_impuestos := v_subtotal * 0.18;
           
       
           IF p_descuento > (v_subtotal * 0.2) THEN
               p_descuento := v_subtotal * 0.2;
               p_resultado := 'Advertencia: Descuento ajustado al máximo permitido (20%)';
           END IF;
           
          
           v_total := v_subtotal + v_impuestos - p_descuento;
           
          
           INSERT INTO ventas (
               codigo_venta,
               cliente_id,
               fecha_hora,
               subtotal,
               impuestos,
               descuento,
               total,
               metodo_pago,
               estado
           ) VALUES (
               p_codigo_venta,
               p_cliente_id,
               CURRENT_TIMESTAMP,
               v_subtotal,
               v_impuestos,
               p_descuento,
               v_total,
               p_metodo_pago,
               'COMPLETADA'
           ) RETURNING venta_id INTO v_venta_id;
           
           
           FOR v_producto IN SELECT * FROM JSONB_ARRAY_ELEMENTS(p_productos) AS prod
           LOOP
              
               SELECT precio INTO v_precio_actual
               FROM productos 
               WHERE producto_id = (v_producto->>'producto_id')::INT;
               
               
               INSERT INTO detalle_ventas (
                   venta_id,
                   producto_id,
                   cantidad,
                   precio_unitario
               ) VALUES (
                   v_venta_id,
                   (v_producto->>'producto_id')::INT,
                   (v_producto->>'cantidad')::INT,
                   v_precio_actual
               );
               
              
               UPDATE productos 
               SET stock = stock - (v_producto->>'cantidad')::INT,
                   fecha_actualizacion = CURRENT_TIMESTAMP
               WHERE producto_id = (v_producto->>'producto_id')::INT;
           END LOOP;
           
           
           COMMIT;
           
           
           IF p_resultado IS NULL OR p_resultado = '' THEN
               p_resultado := 'Venta registrada exitosamente. Código: ' || p_codigo_venta;
           END IF;
           
       EXCEPTION
           WHEN OTHERS THEN
             
               ROLLBACK;
               p_resultado := 'Error al procesar la venta: ' || SQLERRM;
               p_codigo_venta := NULL;
       END;
   END;
   $$;
   ```

   ° Validar que el cliente exista.

   ```postgresql
   CREATE OR REPLACE FUNCTION obtener_clientes_inactivos(
       p_meses_inactividad INT DEFAULT 6,
       p_limite INT DEFAULT NULL
   )
   RETURNS TABLE (
       cliente_id INT,
       dni VARCHAR(8),
       nombre_completo TEXT,
       email VARCHAR(100),
       telefono VARCHAR(15),
       ultima_compra TIMESTAMP,
       meses_inactivo NUMERIC(10,2),
       estado TEXT
   ) AS $$
   BEGIN
       RETURN QUERY
       SELECT 
           c.cliente_id,
           c.dni,
           c.nombre || ' ' || c.apellido AS nombre_completo,
           c.email,
           c.telefono,
           MAX(v.fecha_hora) AS ultima_compra,
           EXTRACT(MONTH FROM AGE(CURRENT_DATE, MAX(v.fecha_hora))) AS meses_inactivo,
           CASE
               WHEN MAX(v.fecha_hora) IS NULL THEN 'Nunca ha comprado'
               ELSE 'Inactivo'
           END AS estado
       FROM 
           clientes c
       LEFT JOIN 
           ventas v ON c.cliente_id = v.cliente_id
       GROUP BY 
           c.cliente_id, c.dni, c.nombre, c.apellido, c.email, c.telefono
       HAVING 
           MAX(v.fecha_hora) IS NULL 
           OR MAX(v.fecha_hora) < CURRENT_DATE - (p_meses_inactividad || ' months')::INTERVAL
       ORDER BY 
           meses_inactivo DESC NULLS FIRST
       LIMIT 
           CASE WHEN p_limite IS NULL THEN (SELECT COUNT(*) FROM clientes) ELSE p_limite END;
   END;
   $$ LANGUAGE plpgsql;
   ```

   ° Verificar que el stock sea suficiente antes de procesar la venta.

   ```postgresql
   CREATE OR REPLACE FUNCTION generar_reporte_stock_critico(
       p_nivel_alerta INT DEFAULT 1
   )
   RETURNS TABLE (
       sku VARCHAR(20),
       producto VARCHAR(100),
       categoria VARCHAR(50),
       stock_actual INT,
       stock_minimo INT,
       proveedor VARCHAR(100),
       ultima_venta DATE,
       estado_stock TEXT
   ) AS $$
   BEGIN
       RETURN QUERY
       SELECT 
           p.sku,
           p.nombre AS producto,
           cat.nombre AS categoria,
           p.stock AS stock_actual,
           p.stock_minimo,
           pr.nombre_empresa AS proveedor,
           (SELECT MAX(v.fecha_hora)::DATE 
            FROM detalle_ventas dv
            JOIN ventas v ON dv.venta_id = v.venta_id
            WHERE dv.producto_id = p.producto_id) AS ultima_venta,
           CASE
               WHEN p.stock = 0 THEN 'AGOTADO'
               WHEN p.stock < p.stock_minimo THEN 'CRÍTICO'
               WHEN p.stock <= (p.stock_minimo * 2) AND p_nivel_alerta >= 2 THEN 'BAJO'
               ELSE 'NORMAL'
           END AS estado_stock
       FROM 
           productos p
       JOIN 
           categorias cat ON p.categoria_id = cat.categoria_id
       JOIN 
           provedores pr ON p.provedor_id = pr.provedor_id
       WHERE 
           p.stock <= CASE
               WHEN p_nivel_alerta = 1 THEN p.stock_minimo
               WHEN p_nivel_alerta = 2 THEN p.stock_minimo * 2
               ELSE p.stock
           END
       ORDER BY 
           CASE
               WHEN p.stock = 0 THEN 1
               WHEN p.stock < p.stock_minimo THEN 2
               ELSE 3
           END,
           p.stock;
   END;
   $$ LANGUAGE plpgsql;
   ```

   ° Si no hay stock suficiente, Notificar por medio de un mensaje en consola usando RAISE.

   ```postgresql
   CREATE OR REPLACE PROCEDURE actualizar_precio_producto(
       p_producto_id INT,
       p_nuevo_precio NUMERIC(12,2),
       p_usuario VARCHAR(50) DEFAULT 'SISTEMA',
       OUT p_resultado TEXT
   )
   LANGUAGE plpgsql
   AS $$
   DECLARE
       v_precio_actual NUMERIC(12,2);
       v_producto_existe BOOLEAN;
   BEGIN
     
       SELECT EXISTS(SELECT 1 FROM productos WHERE producto_id = p_producto_id) INTO v_producto_existe;
       
       IF NOT v_producto_existe THEN
           p_resultado := 'Error: El producto no existe';
           RETURN;
       END IF;
       
    
       IF p_nuevo_precio <= 0 THEN
           p_resultado := 'Error: El precio debe ser mayor a cero';
           RETURN;
       END IF;
       
      
       SELECT precio INTO v_precio_actual FROM productos WHERE producto_id = p_producto_id;
       
      
       BEGIN
        
           INSERT INTO historico_precios (
               producto_id,
               precio_anterior,
               precio_nuevo,
               fecha_cambio,
               usuario_cambio
           ) VALUES (
               p_producto_id,
               v_precio_actual,
               p_nuevo_precio,
               CURRENT_TIMESTAMP,
               p_usuario
           );
           
          
           UPDATE productos 
           SET 
               precio = p_nuevo_precio,
               fecha_actualizacion = CURRENT_TIMESTAMP
           WHERE 
               producto_id = p_producto_id;
           
         n
           COMMIT;
           
           p_resultado := 'Precio actualizado correctamente de ' || v_precio_actual || ' a ' || p_nuevo_precio;
           
       EXCEPTION
           WHEN OTHERS THEN
           
               ROLLBACK;
               p_resultado := 'Error al actualizar precio: ' || SQLERRM;
       END;
   END;
   $$;
   ```

   ° Si hay stock, se realiza el registro de la venta.

   ```postgresql
   CREATE OR REPLACE FUNCTION analisis_ventas_periodo(
       p_fecha_inicio DATE,
       p_fecha_fin DATE,
       p_agrupamiento VARCHAR(10) DEFAULT 'DIA' -- DIA, SEMANA, MES
   )
   RETURNS TABLE (
       periodo TEXT,
       cantidad_ventas BIGINT,
       total_ventas NUMERIC(12,2),
       promedio_venta NUMERIC(12,2),
       clientes_unicos BIGINT,
       productos_vendidos BIGINT
   ) AS $$
   BEGIN
       RETURN QUERY
       SELECT
           CASE
               WHEN p_agrupamiento = 'DIA' THEN TO_CHAR(v.fecha_hora, 'YYYY-MM-DD')
               WHEN p_agrupamiento = 'SEMANA' THEN 'Semana ' || TO_CHAR(v.fecha_hora, 'YYYY-WW')
               WHEN p_agrupamiento = 'MES' THEN TO_CHAR(v.fecha_hora, 'YYYY-MM')
               ELSE TO_CHAR(v.fecha_hora, 'YYYY-MM-DD')
           END AS periodo,
           COUNT(DISTINCT v.venta_id) AS cantidad_ventas,
           SUM(v.total) AS total_ventas,
           ROUND(AVG(v.total), 2) AS promedio_venta,
           COUNT(DISTINCT v.cliente_id) AS clientes_unicos,
           SUM(dv.cantidad) AS productos_vendidos
       FROM
           ventas v
       JOIN
           detalle_ventas dv ON v.venta_id = dv.venta_id
       WHERE
           v.fecha_hora::DATE BETWEEN p_fecha_inicio AND p_fecha_fin
       GROUP BY
           CASE
               WHEN p_agrupamiento = 'DIA' THEN TO_CHAR(v.fecha_hora, 'YYYY-MM-DD')
               WHEN p_agrupamiento = 'SEMANA' THEN 'Semana ' || TO_CHAR(v.fecha_hora, 'YYYY-WW')
               WHEN p_agrupamiento = 'MES' THEN TO_CHAR(v.fecha_hora, 'YYYY-MM')
               ELSE TO_CHAR(v.fecha_hora, 'YYYY-MM-DD')
           END
       ORDER BY
           periodo;
   END;
   $$ LANGUAGE plpgsql;
   ```

   


README



