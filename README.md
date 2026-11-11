# tem1_2mom_lozano-martin
Pregunta 1. Diseño e implementación del modelo de datos
Construya el modelo de almacenamiento en Apache Hive para el dataset proporcionado.
Actividades:
●	Identificar todos los campos del dataset.
●	Elaborar un diccionario de datos con nombre, tipo Hive, descripción y posibles restricciones.
●	Crear una tabla analítica con tipos de datos apropiados.
●	Documentar el procedimiento de carga.
Entregables: diccionario de datos, DDL, instrucciones de carga y evidencia de registros importados.
CREATE DATABASE IF NOT EXISTS superstore_db;
USE superstore_db;
CREATE TABLE superstore (
row_id INT,
order_id STRING,
order_date DATE,
ship_date DATE,
ship_mode STRING,
customer_id STRING,
customer_name STRING,
segment STRING,
country STRING,
city STRING,
state STRING,
postal_code STRING,
region STRING,
product_id STRING,
category STRING,
sub_category STRING,
product_name STRING,
sales DECIMAL(12,4),
quantity INT,
discount DECIMAL(6,4),
profit DECIMAL(12,4)
 )
 STORED AS ORC;
DESCRIBE superstore;
USE superstore_db;
LOAD DATA INPATH
'/user/hive/warehouse/superstore/Sample_Superstore_clean.csv' 
INTO TABLE superstore_raw;
SELECT COUNT(*) FROM superstore_raw;
SELECT * FROM superstore_raw LIMIT 10;

INSERT OVERWRITE TABLE superstore
SELECT
CAST(row_id AS INT),
order_id,
TO_DATE(FROM_UNIXTIME(UNIX_TIMESTAMP(order_date, 'M/d/yyyy'))),
TO_DATE(FROM_UNIXTIME(UNIX_TIMESTAMP(ship_date, 'M/d/yyyy'))),
ship_mode,
customer_id,
customer_name,
segment,
country,
city,
state,
postal_code,
|region,
product_id,
category,
sub_category,
product_name,
CAST(sales AS DECIMAL(12,4)),
CAST(quantity AS INT)
 CAST(discount AS DECIMAL(6,4)),
CAST(profit AS DECIMAL(12,4)
FROM superstore_raw;

SELECT
row_id,
order_id,
order_date,
ship_date,
sales,
quantity,
discount,
profit
FROM superstore
LIMIT 10;

DESCRIBE FORMATTED superstore;

 
 
 
 
 
 


Pregunta 2. Auditoría de calidad e integridad de datos
Realice un análisis de calidad sobre el dataset.
Determine:
●	Cantidad total de registros.
●	Cantidad de identificadores de fila duplicados.
●	Cantidad de órdenes únicas y clientes únicos.
●	Campos que contienen valores NULL.
●	Registros con fechas inválidas o inconsistentes.
●	Registros con ventas iguales o inferiores a cero.
●	Registros con descuentos fuera del intervalo esperado.
●	Registros donde la fecha de envío sea anterior a la fecha de pedido.
Construya una consulta que permita obtener un resumen de calidad de datos y proponga criterios de tratamiento para las inconsistencias identificadas.
USE superstore_db;

SELECT
COUNT(*) AS total_registros,
COUNT(DISTINCT row_id) AS identificadores_unicos,
COUNT(*) - COUNT(DISTINCT row_id) AS registros_duplicados,
COUNT(DISTINCT order_id) AS ordenes_unicas,
COUNT(DISTINCT customer_id) AS clientes_unicos
FROM superstore;

SELECT
row_id,
COUNT(*) AS repeticiones
FROM superstore
GROUP BY row_id
HAVING COUNT(*) > 1;

BLOQUE II. PROCESAMIENTO Y CONSULTAS ANALÍTICAS
Pregunta 3. Análisis de evolución comercial por región
Determine la evolución de las ventas y ganancias por región durante los años disponibles.
Debe obtener:
●	Ventas totales por región y año.
●	Ganancia total por región y año.
●	Margen de rentabilidad.
●	Variación porcentual respecto al año anterior.
●	Identificación de la región con mejor y peor desempeño comercial.
Utilice CTE y funciones de ventana para resolver el problema.
Pregunta 4. Segmentación avanzada de clientes mediante RFM
Construya un análisis RFM (Recency, Frequency, Monetary) para identificar los diferentes perfiles de clientes.
Determine:
●	Recency: días desde la última compra de cada cliente, tomando como referencia la fecha máxima del dataset.
●	Frequency: cantidad de órdenes únicas realizadas.
●	Monetary: importe total de ventas acumuladas.
●	Clasificación de clientes mediante puntuaciones RFM.
●	Identificación de clientes de alto valor, recurrentes y en riesgo de abandono.
Defina y justifique los criterios utilizados para segmentar los clientes.
Pregunta 5. Ranking de productos según rentabilidad
Identifique los productos de mayor y menor desempeño económico.
Determine:
●	Los cinco productos con mayor ganancia acumulada por categoría.
●	Los cinco productos con mayor volumen de ventas.
●	Los cinco productos con menor rentabilidad.
●	El margen de ganancia individual de cada producto.
●	La posición de cada producto dentro de su categoría.
Utilice RANK(), DENSE_RANK() y funciones de agregación acumulativa.
Pregunta 6. Análisis del impacto de los descuentos
Analice la relación entre los descuentos comerciales y la rentabilidad.
Clasifique los registros en los siguientes rangos:
●	Sin descuento: 0.
●	Descuento bajo: mayor a 0 y hasta 20%.
●	Descuento medio: mayor a 20% y hasta 50%.
●	Descuento alto: mayor a 50%.
Calcule:
●	Ventas y ganancias por rango.
●	Margen de rentabilidad.
●	Cantidad de registros con pérdidas.
●	Porcentaje de registros no rentables.
●	Descuento promedio.
●	Categorías y subcategorías más afectadas.
Finalmente, identifique si los descuentos elevados están asociados con una mayor proporción de operaciones no rentables.
BLOQUE III. ANALÍTICA AVANZADA
Pregunta 7. Análisis de tendencias temporales
Construya un análisis de evolución mensual de ventas y ganancias.
Debe incluir:
●	Ventas y ganancias mensuales.
●	Variación porcentual respecto al mes anterior.
●	Comparación con el mismo mes del año anterior.
●	Promedio móvil de tres meses.
●	Identificación de los tres meses con mayor y menor volumen de ventas.
Utilice LAG(), funciones de ventana y agregaciones temporales.
Pregunta 8. Análisis Pareto de ventas y rentabilidad
Aplique la regla 80/20 para identificar la concentración comercial.
Realice dos análisis independientes:
a) Determine qué cantidad de productos representa el 80% de las ventas acumuladas.
b) Determine qué cantidad de productos representa el 80% de las ganancias acumuladas.
Para ambos análisis:
●	Ordene los productos de mayor a menor contribución.
●	Calcule el porcentaje de participación individual.
●	Identifique los productos que concentran la mayor contribución económica.
Compare ambos resultados y explique las diferencias encontradas.

