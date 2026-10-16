# UD3 · Realización de consultas

> Cómo **extraer información** con SQL: SELECT/WHERE/ORDER BY, operadores, resumen y agrupación, JOINs, subconsultas, operadores de conjunto y **optimización**.

## Índice
1. Proyección, selección y ordenación
2. Operadores (comparación, lógicos, especiales)
3. Consultas de resumen
4. Consolidación de registros (DISTINCT, ventana)
5. Composiciones internas
6. Composiciones externas
7. Subconsultas
8. Combinación de múltiples selecciones
9. Optimización de consultas

## Esquema de referencia (para los ejemplos)
```
clientes(id_cliente, nombre, ciudad)
  Ana-Madrid · Luis-Barcelona · Marta-Madrid · Carlos-Valencia
pedidos(id_pedido, id_cliente, fecha, importe)
  Ana 2026-01-15 120,50 · Luis 2026-01-18 75,00
  Ana 2026-02-02  45,30 · Marta 2026-02-10 200,00   (Carlos no tiene pedidos)
```

---

## 1. Proyección, selección y ordenación

| Acción | Cláusula | Qué hace |
|---|---|---|
| **Proyección** | `SELECT` | Elige las **columnas** a mostrar |
| **Selección** | `WHERE` | Filtra las **filas** que cumplen la condición |
| **Ordenación** | `ORDER BY` | Ordena el resultado |

💡 Una buena consulta pide **solo las columnas y filas necesarias**.

### 1.1 SELECT: proyección, alias, expresiones y DISTINCT
```sql
SELECT nombre, correo FROM clientes;                       -- columnas concretas
SELECT nombre AS Nombre_Cliente FROM clientes;             -- alias
SELECT producto, precio * cantidad AS total
FROM lineas_pedido;                                        -- expresión calculada
SELECT DISTINCT ciudad FROM clientes;                      -- sin repetidos
```
- **Alias (`AS`):** nombre temporal que solo cambia cómo aparece en el resultado, no el nombre real de la columna.
- **Expresiones calculadas:** cálculos con los valores de las columnas.
- **`DISTINCT`:** elimina duplicados **del resultado**, no de la tabla.
- `DISTINCT ciudad` → 3 filas: Madrid, Barcelona, Valencia.

### 1.2 WHERE: condiciones simples y compuestas
```sql
SELECT nombre, edad FROM clientes WHERE edad >= 18;
SELECT nombre, ciudad, edad FROM clientes
WHERE ciudad = 'Madrid' AND (edad < 18 OR edad > 65);
```
| Tipo | Herramienta | Ejemplo |
|---|---|---|
| Simple | Comparación (`=`, `>`) | `edad = 18` |
| Compuesta | Lógicos (`AND`, `OR`) | `edad = 18 AND ciudad = 'Madrid'` |

- `AND`: se cumplen **todas**. `OR`: se cumple **al menos una**.
- Al mezclar operadores → **paréntesis**.

### 1.3 ORDER BY
```sql
SELECT nombre, apellido FROM alumnos ORDER BY apellido ASC;
SELECT nombre, salario FROM empleados ORDER BY salario DESC;
SELECT nombre, provincia, apellido FROM alumnos
ORDER BY provincia ASC, apellido ASC;      -- varias columnas
```
| Orden | Textos | Números |
|---|---|---|
| Ascendente (`ASC`, **por defecto**) | A → Z | Menor → mayor |
| Descendente (`DESC`) | Z → A | Mayor → menor |

- Varias columnas = resolver empates (provincia y luego apellido).
- **NULL:** su posición (principio o final) **varía según el SGBD**; algunos permiten indicarlo expresamente.

---

## 2. Operadores

### 2.1 Comparación
| Operador | Uso |
|---|---|
| `=` | Coincidencia exacta |
| `<>` | Distinto |
| `<`, `>`, `<=`, `>=` | Menor / mayor (o igual) |
| `BETWEEN` | Dentro de un **intervalo** (normalmente incluye los dos extremos) |
| `IN` | Dentro de una **lista** de valores |
| `LIKE` | Patrón de texto |

```sql
WHERE edad BETWEEN 18 AND 65
WHERE ciudad IN ('Madrid', 'Barcelona', 'Valencia')
WHERE nombre LIKE 'Mar%'      -- Marta, Marcos, Marina
```
- Comodines de `LIKE`: **`%`** = cualquier cadena · **`_`** = un único carácter.

### 2.2 Lógicos
| Operador | Significado | Ejemplo |
|---|---|---|
| `AND` | Todas las reglas ciertas | `edad >= 18 AND ciudad = 'Madrid'` |
| `OR` | Al menos una cierta | `ciudad = 'Madrid' OR ciudad = 'Valencia'` |
| `NOT` | Invierte/descarta una regla | `NOT activo` |

- Lo de **paréntesis** se evalúa primero. `AND` suele tener **prioridad sobre `OR`** → usar paréntesis para expresar la intención.

### 2.3 Especiales
| Operador | Aplicación | Ejemplo |
|---|---|---|
| `IS NULL` / `IS NOT NULL` | Localizar valores ausentes / presentes | Clientes sin teléfono |
| `EXISTS` | Comprobar si una subconsulta devuelve datos | Clientes con compras |
| `ANY` / `ALL` | Comparar un valor con una lista (subconsulta) | Productos más caros que otros |

- ⚠️ **No usar `= NULL`** para buscar nulos: usar `IS NULL`.
```sql
SELECT nombre FROM clientes WHERE telefono IS NULL;

SELECT nombre FROM clientes c
WHERE EXISTS (SELECT 1 FROM pedidos p WHERE p.id_cliente = c.id_cliente);
-- → Ana, Luis, Marta (Carlos queda fuera: no tiene pedidos)

WHERE precio > ANY (SELECT precio FROM productos WHERE categoria = 'Libros')
-- cumple con AL MENOS UNO de los precios
WHERE precio > ALL (SELECT precio FROM productos WHERE categoria = 'Libros')
-- cumple con TODOS los precios
```
- `ANY`/`ALL` se combinan con `>`, `<`, `>=`, `<=`.

---

## 3. Consultas de resumen

Calculan métricas globales (totales, medias, recuentos) en vez de listar filas.

### 3.1 Funciones de agregación
| Función | Acción | Ejemplo |
|---|---|---|
| `COUNT` | Cuenta filas | Contar clientes |
| `SUM` | Suma | Calcular ingresos |
| `AVG` | Media | Edad media |
| `MAX` | Mayor | Producto más caro |
| `MIN` | Menor | Artículo más barato |

```sql
SELECT COUNT(*) AS total_clientes FROM clientes;
SELECT SUM(importe) AS ventas_totales FROM pedidos;
SELECT AVG(salario) AS salario_medio FROM empleados;
```
**Con NULL:**
- `COUNT(*)` cuenta **todas las filas** (aunque haya nulos).
- `COUNT(columna)`, `SUM`, `AVG`, `MAX`, `MIN` **ignoran los NULL**.

### 3.2 GROUP BY
- Agrupa las filas que comparten el mismo valor en una o varias columnas; se usa con funciones de agregación.
```sql
SELECT ciudad, COUNT(*) AS total_clientes FROM clientes GROUP BY ciudad;
-- Madrid (2), Barcelona (1), Valencia (1)

SELECT departamento, COUNT(*) AS total_empleados, AVG(salario) AS salario_medio
FROM empleados GROUP BY departamento;
```

### 3.3 HAVING
| Condición | Momento | Qué evalúa |
|---|---|---|
| `WHERE` | **Antes** de agrupar | Filas individuales |
| `HAVING` | **Después** de agrupar | Grupos completos |

```sql
SELECT curso, COUNT(*) AS total_estudiantes
FROM matriculas
GROUP BY curso
HAVING COUNT(*) > 20;      -- solo cursos con más de 20 inscritos
```

---

## 4. Consolidación de registros

**Consolidar:** transformar datos individuales en un resultado resumido, agrupado o clasificado (con `DISTINCT`, `GROUP BY`, agregación y funciones de ventana).

### 4.1 DISTINCT
```sql
SELECT DISTINCT id_cliente FROM pedidos;   -- cada cliente que ha comprado, una vez
```
- Solo elimina duplicados **en el resultado**, no hace cálculos ni modifica datos.

| Búsqueda | Resultado |
|---|---|
| Normal | Muestra repetidos (Madrid, Madrid) |
| `DISTINCT` | Valores únicos (Madrid) |

### 4.2 Agrupación y consolidación
```sql
SELECT mes, SUM(importe) AS ventas_totales FROM ventas GROUP BY mes;
SELECT ciudad, COUNT(*) AS total_clientes FROM clientes
GROUP BY ciudad HAVING COUNT(*) > 10;
```
| Acción | Propósito | Ejemplo |
|---|---|---|
| Selección | Filtrar filas | Ventas de hoy |
| Proyección | Elegir columnas | Mostrar nombres |
| Agrupación | Reunir filas por valores comunes | Sumar por mes |

### 4.3 Funciones de ventana (WINDOW functions)
- Calculan **sin ocultar las filas**: añaden una columna calculada y mantienen cada fila visible (a diferencia de `GROUP BY`).
- **`OVER`:** define sobre qué conjunto de filas actúa la función.
- **`PARTITION BY`:** divide en grupos (dentro de `OVER`); el cálculo se reinicia en cada partición.

| Función | Qué hace |
|---|---|
| `ROW_NUMBER` | Número consecutivo a cada fila |
| `RANK` | Misma posición en empates y **deja saltos** en la numeración |

```sql
SELECT alumno, aula, nota,
       RANK() OVER (PARTITION BY aula ORDER BY nota DESC) AS posicion
FROM calificaciones;
```
Ejemplo de empate (notas 9, 9, 8): `ROW_NUMBER` → 1, 2, 3 · `RANK` → 1, 1, 3.

---

## 5. Composiciones internas

### 5.1 INNER JOIN
- Une filas de dos tablas **solo si hay coincidencia** en la condición `ON`. Sin pareja → no aparece.
```sql
SELECT clientes.nombre, pedidos.fecha_pedido, pedidos.importe
FROM clientes
INNER JOIN pedidos ON clientes.id_cliente = pedidos.id_cliente;
```
→ 4 filas: Ana (120,50), Luis (75,00), Ana (45,30), Marta (200,00). **Carlos no aparece.**
- Estructura: tabla inicial + `INNER JOIN` + segunda tabla + `ON` condición.

### 5.2 Varias tablas (encadenar JOINs)
```sql
SELECT c.nombre, p.fecha_pedido, lp.producto, lp.cantidad
FROM clientes c
INNER JOIN pedidos p        ON c.id_cliente = p.id_cliente
INNER JOIN lineas_pedido lp ON p.id_pedido  = lp.id_pedido;
```
- Cada par de tablas necesita su condición de relación.
- **Legibilidad:** sangrías, saltos de línea y alias claros (`c`, `p`).
- ⚠️ Una condición mal escrita puede **multiplicar filas** o dar resultados incorrectos.

### 5.3 Self join (tabla consigo misma)
- La misma tabla se usa **dos veces con alias distintos** (ej. empleados y sus jefes).
```sql
SELECT e.nombre AS empleado, j.nombre AS jefe
FROM empleados e
INNER JOIN empleados j ON e.id_jefe = j.id_empleado;
```
💡 Siempre hay que dar alias **diferentes** a cada referencia. Alternativa: subconsulta (depende del caso, el SGBD y el plan de ejecución).

---

## 6. Composiciones externas

Conservan registros **aunque no tengan pareja**; los datos que faltan se rellenan con **NULL**.

| Tipo | Conserva | Sin coincidencia |
|---|---|---|
| `LEFT JOIN` | Tabla **izquierda** | NULL en las columnas de la derecha |
| `RIGHT JOIN` | Tabla **derecha** | NULL en las columnas de la izquierda |
| `FULL OUTER JOIN` | **Ambas** tablas | NULL en los datos sin pareja |
| `CROSS JOIN` | Producto cartesiano (todas las combinaciones) | — |

| Tipo de unión | Parejas | Datos sueltos |
|---|---|---|
| Interna | Sí | No |
| Externa | Sí | Sí |

### 6.1 LEFT y RIGHT JOIN
```sql
SELECT c.nombre, p.importe
FROM clientes c
LEFT JOIN pedidos p ON c.id_cliente = p.id_cliente;   -- incluye a Carlos (importe NULL)
```
- `LEFT JOIN` es más habitual: se lee de izquierda a derecha. Muchos `RIGHT JOIN` se reescriben cambiando el orden de las tablas.

### 6.2 FULL OUTER JOIN
```sql
SELECT c.nombre, p.importe
FROM clientes c
FULL OUTER JOIN pedidos p ON c.id_cliente = p.id_cliente;
```
- Muestra clientes con pedidos, clientes sin pedidos y pedidos sin cliente (si existieran).
- No todos los SGBD lo soportan → **emulación** (ej. MySQL): `LEFT JOIN` + `UNION` + `RIGHT JOIN`.
```sql
SELECT c.nombre, p.importe FROM clientes c
LEFT JOIN pedidos p ON c.id_cliente = p.id_cliente
UNION
SELECT c.nombre, p.importe FROM clientes c
RIGHT JOIN pedidos p ON c.id_cliente = p.id_cliente;
```
(`UNION` evita los duplicados entre las dos partes.)

### 6.3 CROSS JOIN
```sql
SELECT c.color, t.talla FROM colores c CROSS JOIN tallas t;   -- 3 colores × 4 tallas = 12
```
- **Sin condición `ON`.** El volumen crece de forma **multiplicativa** (100 × 100 = 10.000).
- Aumenta mucho el coste → usar solo para evaluar todas las combinaciones; filtrar o limitar los conjuntos de entrada.

---

## 7. Subconsultas

**Subconsulta:** consulta dentro de otra instrucción SQL. Sirve para calcular un valor, filtrar registros o construir una **tabla derivada**. Puede ir en `WHERE`, `FROM` y `SELECT`.

### 7.1 En WHERE: escalares, de fila y de tabla
| Tipo | Devuelve | Ejemplo |
|---|---|---|
| **Escalar** | Un único valor | `precio > (SELECT AVG(precio) FROM productos)` |
| **De tabla** | Varios valores (con `IN`, `ANY`, `ALL`) | `id_cliente IN (SELECT id_cliente FROM pedidos)` |

```sql
SELECT nombre, precio FROM productos
WHERE precio > (SELECT AVG(precio) FROM productos);

SELECT nombre FROM clientes
WHERE id_cliente IN (SELECT id_cliente FROM pedidos);
```
- `ANY`: al menos un valor · `ALL`: todos los valores.

### 7.2 Correlacionadas vs. no correlacionadas
| Característica | No correlacionada | Correlacionada |
|---|---|---|
| Independencia | Totalmente autónoma | Depende de la consulta exterior |
| Operación | Se ejecuta **una vez** | Se ejecuta **fila por fila** |
| Rendimiento | Suele ser más eficiente | Mayor coste si hay muchas filas |

```sql
-- Correlacionada: empleados con salario > media de SU departamento
SELECT e.nombre, e.salario, e.departamento
FROM empleados e
WHERE e.salario > (
  SELECT AVG(e2.salario) FROM empleados e2
  WHERE e2.departamento = e.departamento
);
```

### 7.3 En FROM y SELECT: tablas derivadas y CTE
- **Subconsulta en FROM = tabla derivada** → el **alias es obligatorio**.
```sql
SELECT resumen.ciudad, resumen.total_clientes
FROM (
  SELECT ciudad, COUNT(*) AS total_clientes
  FROM clientes GROUP BY ciudad
) AS resumen
WHERE resumen.total_clientes > 10;
```
- **CTE (expresión de tabla común):** consulta auxiliar con `WITH` al inicio, reutilizable en la principal; hace más legibles las consultas largas.
```sql
WITH resumen AS (
  SELECT ciudad, COUNT(*) AS total_clientes
  FROM clientes GROUP BY ciudad
)
SELECT ciudad, total_clientes FROM resumen WHERE total_clientes > 10;
```
- **Subconsulta en SELECT:** calcula un valor para cada registro.
```sql
SELECT c.nombre,
  (SELECT SUM(p.importe) FROM pedidos p
   WHERE p.id_cliente = c.id_cliente) AS gasto_total
FROM clientes c;
```

---

## 8. Combinación de múltiples selecciones

**Operadores de conjunto:** **apilan filas** (los JOIN cruzan columnas). No sustituyen a los JOIN.
- Requisito: mismo **número de columnas** y **tipos compatibles** en la misma posición.
- `ORDER BY` **una sola vez, al final** del conjunto.
- Disponibilidad y sintaxis **varían según el SGBD**.

| Operador | Función | Alternativa |
|---|---|---|
| `UNION` | Junta resultados y **elimina duplicados** | — |
| `UNION ALL` | Junta resultados **conservando duplicados** (menor coste: no revisa duplicados) | — |
| `INTERSECT` | Solo los elementos **comunes** a ambas consultas | `INNER JOIN` |
| `EXCEPT` | Los de la primera consulta que **no** están en la segunda | `NOT IN` / `NOT EXISTS` |

```sql
SELECT nombre, email FROM clientes
UNION
SELECT nombre, email FROM proveedores
ORDER BY nombre ASC;                      -- orden al final

SELECT id_cliente FROM compras_online
INTERSECT
SELECT id_cliente FROM compras_tienda;    -- compran en ambos sitios

SELECT id_cliente FROM compras_online
EXCEPT
SELECT id_cliente FROM compras_tienda;    -- solo online
```
- `INTERSECT` compara **resultados completos de consultas**; `INNER JOIN` combina tablas mediante una condición de relación.
- `NOT EXISTS` suele ser **más seguro** que `NOT IN` si la subconsulta puede devolver NULL.

**Casos de uso:**
- `UNION` → lista única de contactos (clientes + proveedores).
- `UNION ALL` → consolidar históricos cuando no hay duplicados o se quieren conservar.
- `INTERSECT` → usuarios que aparecen en dos campañas.
- `EXCEPT` → clientes que compraron el año pasado pero no este.

| Tipo de unión | Qué hace con los datos |
|---|---|
| Composición interna | Solo guarda las coincidencias exactas |
| Composición externa | Mantiene completa la lista de referencia |

---

## 9. Optimización de consultas

- El **optimizador** del SGBD evalúa el **coste** de cada ruta y elige la de menor coste estimado → ese es el **plan de ejecución**.

### 9.1 Plan de ejecución y EXPLAIN
- Plan de ejecución: la ruta que el optimizador considera más adecuada (según estadísticas, índices y reglas internas). **No siempre es la de menor coste real.**
- `EXPLAIN` delante de la consulta → muestra el plan **estimado**: si hace escaneo completo, búsqueda por índice, unión entre tablas o agrupación, y cuántas filas revisará.
- `EXPLAIN ANALYZE` (en algunos SGBD): **ejecuta** la consulta y muestra tiempos y filas reales.

| Elemento | Significado |
|---|---|
| Tipo de acceso | Cómo lee la tabla el sistema |
| Filas estimadas | Cantidad de datos que se van a revisar |

### 9.2 Estrategias
| Estrategia | Propósito | Ejemplo |
|---|---|---|
| **Crear índices** | Reducir lecturas en búsquedas frecuentes | Índice en el documento de identidad |
| **Reescribir consultas** | Pedir los datos de forma directa | Buscar entre dos fechas |
| **Evitar funciones en WHERE** | Que el sistema pueda usar el índice | No aplicar fórmulas a la columna filtrada |

- ⚠️ No indexar **todas** las columnas: cada índice ocupa espacio y se actualiza en cada INSERT/UPDATE/DELETE.
```sql
-- ❌ Dificulta el uso del índice
WHERE YEAR(fecha_compra) = 2023
-- ✅ Permite aprovecharlo (y evita problemas si hay fecha y hora)
WHERE fecha_compra >= '2023-01-01' AND fecha_compra < '2024-01-01'
```

### 9.3 Estadísticas, coste y hints
- **Coste:** recursos estimados para devolver los resultados.
- **Estadísticas:** resúmenes internos sobre distribución y volumen de datos. Hay que **actualizarlas periódicamente** (los datos cambian); si no, el sistema puede elegir planes poco ajustados.
- **Hint:** instrucción manual en la consulta para **orientar (o forzar, según SGBD)** al optimizador a usar una estrategia concreta.
  - Herramienta **avanzada**: usar con prudencia, puede **empeorar** el rendimiento si cambian los datos o la estructura.

---

## Glosario rápido
| Término | Definición |
|---|---|
| Alias | Nombre temporal para una tabla o columna (`AS`) |
| ANY / ALL | Comparan con varios valores: al menos uno / todos |
| Composición interna / externa | Solo coincidencias / conserva una o ambas tablas, rellenando con NULL |
| Consulta de resumen | Calcula resultados de un conjunto global |
| CTE | Consulta auxiliar con `WITH` |
| EXPLAIN | Muestra el plan de ejecución previsto |
| Función de ventana | Calcula sobre filas relacionadas sin colapsar el resultado |
| GROUP BY / HAVING | Agrupa filas / filtra grupos ya formados |
| Hint | Sugerencia manual al optimizador |
| Optimizador | Componente que elige la ruta de ejecución eficiente |
| PARTITION BY | Divide en particiones dentro de una función de ventana |
| Self join | Tabla unida consigo misma con dos alias |
| Subconsulta (correlacionada) | Consulta anidada (se evalúa una vez por cada fila externa) |

---

## ✅ Chuleta final
- Orden de lectura: `SELECT` (columnas) → `FROM` → `WHERE` (filas) → `GROUP BY` → `HAVING` (grupos) → `ORDER BY`.
- `WHERE` filtra **antes** de agrupar; `HAVING` **después**.
- Nulos: `IS NULL` (nunca `= NULL`); `COUNT(*)` cuenta todo, el resto de agregados ignora NULL.
- `INNER` = solo coincidencias · `LEFT` / `RIGHT` = conserva un lado · `FULL OUTER` = ambos · `CROSS` = todas las combinaciones.
- Self join → alias distintos. Subconsulta en `FROM` → alias obligatorio.
- `UNION` (sin duplicados) vs. `UNION ALL` (más rápido). `INTERSECT` = comunes · `EXCEPT` = solo en la primera.
- Ventana: `OVER (PARTITION BY ... ORDER BY ...)`; `RANK` deja saltos en los empates.
- Optimizar: `EXPLAIN`, índices adecuados, **sin funciones sobre la columna del WHERE**, estadísticas al día; hints solo con prudencia.
