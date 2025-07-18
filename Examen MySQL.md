# Examen MySQL

### Tablas

```sql
DROP DATABASE sakilacampus;

CREATE DATABASE sakilacampus;

USE sakilacampus;

CREATE TABLE IF NOT EXISTS pais (
	id_pais SMALLINT UNSIGNED,
    PRIMARY KEY (id_pais),
    nombre VARCHAR(50),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS ciudad (
	id_ciudad SMALLINT UNSIGNED,
    PRIMARY KEY (id_ciudad),
    nombre VARCHAR(50),
    id_pais SMALLINT UNSIGNED,
    CONSTRAINT FK_id_pais_ciudad FOREIGN KEY (id_pais) REFERENCES pais(id_pais),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS direccion (
	id_direccion SMALLINT UNSIGNED,
    PRIMARY KEY (id_direccion),
    direccion VARCHAR(50),
    direccion2 VARCHAR(50),
    distrito VARCHAR(20),
    id_ciudad SMALLINT UNSIGNED,
    CONSTRAINT FK_id_ciudad_direccion FOREIGN KEY (id_ciudad) REFERENCES ciudad(id_ciudad),
    codigo_postal VARCHAR(10),
    telefono VARCHAR(20),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS idioma (
	id_idioma TINYINT UNSIGNED,
    PRIMARY KEY (id_idioma),
    nombre CHAR(20),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS pelicula (
	id_pelicula SMALLINT UNSIGNED,
    PRIMARY KEY (id_pelicula),
    titulo VARCHAR(255),
    descripcion TEXT,
    anyo_lanzamiento YEAR,
    id_idioma TINYINT UNSIGNED,
    CONSTRAINT FK_id_idioma_pelicula FOREIGN KEY (id_idioma) REFERENCES idioma(id_idioma),
    id_idioma_original TINYINT UNSIGNED,
    CONSTRAINT FK_id_idioma_original_pelicula FOREIGN KEY (id_idioma_original) REFERENCES idioma(id_idioma),
    duracion_alquiler TINYINT UNSIGNED,
    rental_rate DECIMAL(4,2),
    duracion SMALLINT UNSIGNED,
    replacement_cost DECIMAL(5,2),
    clasificacion ENUM('G','PG','PG-13','R','NC-17'),
    caracteristicas_especiales SET('Trailers','Commentaries','Deleted Scenes','Behind the Scenes'),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS categoria (
	id_categoria TINYINT UNSIGNED,
    PRIMARY KEY (id_categoria),
    nombre VARCHAR(25),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS pelicula_categoria (
	id_pelicula SMALLINT UNSIGNED,
    id_categoria TINYINT UNSIGNED,
    PRIMARY KEY (id_pelicula,id_categoria),
    CONSTRAINT FK_id_pelicula_pelicula_categoria FOREIGN KEY (id_pelicula) REFERENCES pelicula(id_pelicula),
    CONSTRAINT FK_id_categoria_pelicula_categoria FOREIGN KEY (id_categoria) REFERENCES categoria(id_categoria),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS actor (
	id_actor SMALLINT UNSIGNED,
    PRIMARY KEY (id_actor),
    nombre VARCHAR(45),
    apellidos VARCHAR(45),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS pelicula_actor (
	id_actor SMALLINT UNSIGNED,
    id_pelicula SMALLINT UNSIGNED,
    PRIMARY KEY (id_actor,id_pelicula),
    CONSTRAINT FK_id_actor_pelicula_actor FOREIGN KEY (id_actor) REFERENCES actor(id_actor),
    CONSTRAINT FK_id_pelicula_pelicula_actor FOREIGN KEY (id_pelicula) REFERENCES pelicula(id_pelicula),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS almacen (
	id_almacen TINYINT UNSIGNED,
    PRIMARY KEY (id_almacen),
    id_direccion SMALLINT UNSIGNED,
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS empleado (
	id_empleado TINYINT UNSIGNED,
    PRIMARY KEY (id_empleado),
    nombre VARCHAR(45),
    apellidos VARCHAR(45),
    id_direccion SMALLINT UNSIGNED,
    CONSTRAINT FK_id_direccion_empleado FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion),
    imagen BLOB,
    email VARCHAR(50),
    id_almacen TINYINT UNSIGNED,
    CONSTRAINT FK_id_almacen_empleado FOREIGN KEY (id_almacen) REFERENCES almacen(id_almacen),
    activo TINYINT(1),
    username VARCHAR(16),
    password VARCHAR(40),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS almacen_empleado_jefe (
	id_almacen TINYINT UNSIGNED,
    id_empleado TINYINT UNSIGNED,
    PRIMARY KEY (id_almacen,id_empleado),
    CONSTRAINT FK_id_almacen_empleado_almacen FOREIGN KEY (id_almacen) REFERENCES almacen(id_almacen),
    CONSTRAINT FK_id_empleado_empleado_almacen FOREIGN KEY (id_empleado) REFERENCES empleado(id_empleado),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS cliente (
	id_cliente SMALLINT UNSIGNED,
    PRIMARY KEY (id_cliente),
    id_almacen TINYINT UNSIGNED,
    CONSTRAINT FK_id_almacen_cliente FOREIGN KEY (id_almacen) REFERENCES almacen(id_almacen),
    nombre VARCHAR(45),
    apellidos VARCHAR(45),
    email VARCHAR(50),
    id_direccion SMALLINT UNSIGNED,
    CONSTRAINT FK_id_direccion_cliente FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion),
    activo TINYINT(1),
    fecha_creacion DATETIME,
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS inventario (
	id_inventario MEDIUMINT UNSIGNED,
    PRIMARY KEY (id_inventario),
    id_pelicula SMALLINT UNSIGNED,
    CONSTRAINT FK_id_pelicula_inventario FOREIGN KEY (id_pelicula) REFERENCES pelicula(id_pelicula),
    id_almacen TINYINT UNSIGNED,
    CONSTRAINT FK_id_almacen_inventario FOREIGN KEY (id_almacen) REFERENCES almacen(id_almacen),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS alquiler (
	id_alquiler INT,
    PRIMARY KEY (id_alquiler),
    fecha_alquiler DATETIME,
    id_inventario MEDIUMINT UNSIGNED,
    CONSTRAINT FK_id_inventario_alquiler FOREIGN KEY (id_inventario) REFERENCES inventario(id_inventario),
    id_cliente SMALLINT UNSIGNED,
    CONSTRAINT FK_id_cliente_alquiler FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente),
    fecha_devolucion DATETIME,
    id_empleado TINYINT UNSIGNED,
    CONSTRAINT FK_id_empleado_alquiler FOREIGN KEY (id_empleado) REFERENCES empleado(id_empleado),
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS pago (
	id_pago SMALLINT UNSIGNED,
    PRIMARY KEY (id_pago),
    id_cliente SMALLINT UNSIGNED,
    CONSTRAINT FK_id_cliente_pago FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente),
    id_empleado TINYINT UNSIGNED,
    CONSTRAINT FK_id_empleado_pago FOREIGN KEY (id_empleado) REFERENCES empleado(id_empleado),
    id_alquiler INT,
    CONSTRAINT FK_id_alquiler_pago FOREIGN KEY (id_alquiler) REFERENCES alquiler(id_alquiler),
    total DECIMAL(5,2),
    fecha_pago DATETIME,
    ultima_actualizacion TIMESTAMP
) ENGINE = INNODB;

SHOW TABLES;
```



### Inserts

```sql
-- Le quite el id del empleado en jefe a la tabla de 'almacen' para que no ocurriera algun error a la hora de crear la tabla

SET AUTOCOMMIT=0;
INSERT INTO almacen VALUES (1,1,'2006-02-15 04:57:12'),
(2,2,'2006-02-15 04:57:12');
COMMIT;

-- Cree una tabla para relacionar el almacen y el empleado en jefe de cada almacen

SET AUTOCOMMIT=0;
INSERT INTO almacen_empleado_jefe VALUES (2,2,'2006-02-15 04:57:12'),(1,5,'2006-02-15 04:57:12');
COMMIT;
```





### Consultas

```sql
-- CONSULTA 1

SELECT c.id_cliente AS ID_Cliente, c.nombre AS Cliente, c.apellidos AS Apellido, COUNT(a.id_alquiler) AS Cantidad
FROM cliente c
JOIN alquiler a ON c.id_cliente = a.id_cliente
WHERE a.fecha_alquiler >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
GROUP BY c.id_cliente
ORDER BY Cantidad DESC
LIMIT 1;

-- CONSULTA 2

SELECT p.titulo AS Pelicula, COUNT(a.id_alquiler) AS Total
FROM alquiler a
JOIN inventario i ON a.id_inventario = i.id_inventario
JOIN pelicula p ON i.id_pelicula = p.id_pelicula
WHERE a.fecha_alquiler >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY p.id_pelicula
ORDER BY Total DESC
LIMIT 5;

-- CONSULTA 3

SELECT cat.nombre AS Categoria, COUNT(a.id_alquiler) AS Total, SUM(pago.total) AS Ingresos
FROM alquiler a
JOIN pago pago ON a.id_alquiler = pago.id_alquiler
JOIN inventario i ON a.id_inventario = i.id_inventario
JOIN pelicula p ON i.id_pelicula = p.id_pelicula
JOIN pelicula_categoria pc ON p.id_pelicula = pc.id_pelicula
JOIN categoria cat ON pc.id_categoria = cat.id_categoria
GROUP BY cat.id_categoria;

-- CONSULTA 4



-- CONSULTA 5



-- CONSULTA 6

SELECT ciu.nombre AS Ciudad, COUNT(DISTINCT c.id_cliente) AS Clientes
FROM cliente c
JOIN direccion d ON c.id_direccion = d.id_direccion
JOIN ciudad ciu ON d.id_ciudad = ciu.id_ciudad
JOIN alquiler a ON c.id_cliente = a.id_cliente
WHERE a.fecha_alquiler >= DATE_SUB(CURDATE(), INTERVAL 3 MONTH)
GROUP BY ciu.id_ciudad
ORDER BY Clientes DESC
LIMIT 3;

-- CONSULTA 7



-- CONSULTA 8

SELECT AVG(DATEDIFF(fecha_devolucion, fecha_alquiler)) AS promedio_dias
FROM alquiler
WHERE fecha_devolucion IS NOT NULL;

-- CONSULTA 9

SELECT e.id_empleado AS ID_Empleado, e.nombre AS Empleado, e.apellidos AS Apellido, COUNT(a.id_alquiler) AS Alquileres
FROM alquiler a
JOIN empleado e ON a.id_empleado = e.id_empleado
JOIN inventario i ON a.id_inventario = i.id_inventario
JOIN pelicula p ON i.id_pelicula = p.id_pelicula
JOIN pelicula_categoria pc ON p.id_pelicula = pc.id_pelicula
JOIN ategoria cat ON pc.id_categoria = cat.id_categoria
WHERE cat.nombre = 'Acción'
GROUP BY e.id_empleado
ORDER BY Alquileres DESC
LIMIT 5;

-- CONSULTA 10

SELECT c.id_cliente AS ID_Cliente, c.nombre AS Cliente, c.apellidos AS Apellido, COUNT(a.id_alquiler) AS Alquileres, MAX(a.fecha_alquiler) AS Ultima_Vez, MIN(a.fecha_alquiler) AS Primera_Vez
FROM cliente c
JOIN alquiler a ON c.id_cliente = a.id_cliente
GROUP BY c.id_cliente
ORDER BY Alquileres DESC
LIMIT 10;

-- CONSULTA 11

SELECT i.id_idioma AS ID_Idioma, i.nombre AS Idioma, AVG(p.duracion_alquiler) AS Promedio
FROM pelicula p
JOIN idioma i ON p.id_idioma = i.id_idioma
GROUP BY i.id_idioma, i.nombre;


-- CONSULTA 12

SELECT p.id_pelicula AS ID_Pelicula, p.titulo AS Pelicula, p.duracion AS Duracion, COUNT(a.id_alquiler) AS Alquileres
FROM pelicula p
JOIN inventario i ON p.id_pelicula = i.id_pelicula
JOIN alquiler a ON i.id_inventario = a.id_inventario
WHERE a.fecha_alquiler >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY p.id_pelicula, p.titulo, p.duracion
ORDER BY p.duracion DESC
LIMIT 5;

-- CONSULTA 13

SELECT c.id_cliente AS ID_Cliente, c.nombre AS Cliente, c.apellidos AS Apellido, COUNT(*) AS Total_Comedia
FROM cliente c
JOIN alquiler a ON c.id_cliente = a.id_cliente
JOIN inventario i ON a.id_inventario = i.id_inventario
JOIN pelicula_categoria pc ON i.id_pelicula = pc.id_pelicula
JOIN categoria cat ON pc.id_categoria = cat.id_categoria
WHERE cat.nombre = 'Comedia'
GROUP BY c.id_cliente, c.nombre, c.apellidos
ORDER BY Total_Comedia DESC;

-- CONSULTA 14



-- CONSULTA 15



-- CONSULTA 16

SELECT a.id_almacen AS ID_Almacen, SUM(p.total) AS ingresos
FROM pago p
JOIN alquiler alq ON p.id_alquiler = alq.id_alquiler
JOIN inventario i ON alq.id_inventario = i.id_inventario
JOIN almacen a ON i.id_almacen = a.id_almacen
WHERE p.fecha_pago >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
GROUP BY a.id_almacen;

-- CONSULTA 17

SELECT c.id_cliente AS ID_Cliente, c.nombre AS Cliente, c.apellidos AS Apellido, p.total AS Monto
FROM pago p
JOIN cliente c ON p.id_cliente = c.id_cliente
WHERE p.fecha_pago >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
ORDER BY p.total DESC
LIMIT 1;

-- CONSULTA 18



-- CONSULTA 19

SELECT idi.id_idioma AS ID_Idioma, idi.nombre AS Idioma, COUNT(*) AS total_alquileres
FROM alquiler a
JOIN inventario i ON a.id_inventario = i.id_inventario
JOIN pelicula p ON i.id_pelicula = p.id_pelicula
JOIN idioma idi ON p.id_idioma = idi.id_idioma
WHERE a.fecha_alquiler >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH)
GROUP BY idi.id_idioma, idi.nombre;

-- CONSULTA 20


```

