# UD5 · Programación de bases de datos

> Meter **lógica dentro del servidor**: variables, funciones, procedimientos almacenados, estructuras de control, **triggers**, **eventos**, **excepciones** y **cursores**.

## Índice
1. SQL procedimental
2. Variables
3. Funciones
4. Estructuras de control de flujo
5. Procedimientos almacenados y funciones de usuario
6. Eventos y disparadores
7. Excepciones
8. Cursores

> ⚠️ Los ejemplos siguen el estilo **MySQL/MariaDB**. La sintaxis cambia según el SGBD. En MySQL/MariaDB, al crear varias rutinas desde un cliente estándar puede hacer falta `DELIMITER //` antes del bloque y `DELIMITER ;` al final.

---

## 1. Introducción. Lenguaje de programación

- **SQL declarativo:** indicas **qué** datos quieres.
- **SQL procedimental:** indicas también **qué pasos** debe seguir el sistema (variables, control de flujo, rutinas almacenadas).

| SGBD | Lenguaje procedimental | Qué permite |
|---|---|---|
| Oracle | **PL/SQL** | Procedimientos, funciones, **paquetes** y triggers |
| SQL Server | **T-SQL** | Procedimientos, funciones, triggers y control de errores con `TRY..CATCH` |
| PostgreSQL | **PL/pgSQL** | Funciones, procedimientos y triggers |
| MySQL/MariaDB | SQL/PSM o extensiones propias | Procedimientos, funciones, triggers, cursores y handlers |

### Elementos de la programación de BD
| Elemento | Función |
|---|---|
| Variables | Guardan valores temporales durante la ejecución |
| Funciones | Reciben datos, los procesan y devuelven un resultado |
| Estructuras de control | Decisiones y repeticiones |
| Procedimientos almacenados | Agrupan instrucciones para tareas completas |
| Disparadores | Acciones automáticas ante cambios en tablas |
| Eventos | Tareas programadas en un momento o intervalo |
| Excepciones | Gestionan errores durante la ejecución |
| Cursores | Recorren resultados fila por fila |

### 1.1 Extensiones procedimentales
Permiten escribir programas dentro del SGBD como: **bloques anónimos, procedimientos, funciones, disparadores o eventos.**
```sql
CREATE PROCEDURE contar_clientes(OUT total_clientes INT)
BEGIN
  SELECT COUNT(*) INTO total_clientes FROM clientes;
END;
```
→ Cuenta los clientes y devuelve el resultado en un parámetro de **salida**.

### 1.2 Bloques anónimos y estructura
- **Bloque anónimo:** instrucciones que se ejecutan **una vez** y **no se guardan** (pruebas, tareas puntuales, administración).
- En PL/SQL y PL/pgSQL se distinguen zonas de declaraciones, instrucciones y excepciones. En MySQL/MariaDB se escriben dentro de procedimientos, funciones o triggers.
```sql
-- PL/pgSQL
DO $$
DECLARE
  v_total_clientes INT;
BEGIN
  SELECT COUNT(*) INTO v_total_clientes FROM clientes;
  RAISE NOTICE 'Total de clientes: %', v_total_clientes;
END $$;
```
| Parte del bloque | Función |
|---|---|
| Declaración de variables | Datos temporales que se usarán |
| Instrucciones ejecutables | Consultas, cálculos, INSERT/UPDATE/DELETE |
| Estructuras de control | Condiciones y bucles |
| Gestión de errores | Qué hacer si aparece una excepción |

### 1.3 Ventajas de programar en el servidor
| Ventaja | Explicación |
|---|---|
| **Menos tráfico de red** | El servidor procesa y devuelve solo el resultado |
| **Control de acceso** | La app ejecuta procedimientos sin conocer los detalles de las tablas |
| **Encapsulación** | Operaciones complejas agrupadas en rutinas reutilizables |
| **Reutilización** | Varias aplicaciones usan el mismo procedimiento |
| **Control centralizado** | Las reglas de datos están en la BD, no duplicadas en cada programa |

```sql
CALL registrar_venta(15, 3, 2);
-- internamente: comprobar cliente, validar producto, descontar stock, registrar pedido, guardar auditoría
```
⚠️ **Con equilibrio:** no toda la lógica va a la BD.
- BD: protege la integridad y automatiza operaciones cercanas a los datos.
- Aplicación: interfaz, experiencia de usuario y lógica propia del servicio.
- Demasiados procedimientos/funciones/triggers **sin documentar** → mantenimiento difícil.

---

## 2. Variables

Se leen y modifican con **`SET`** y **`SHOW`** (`SHOW` consulta, `SET` modifica).

### 2.1 Variables de usuario
- Creadas por el usuario en una **sesión**; sirven mientras la conexión siga abierta (pruebas, consultas encadenadas, recoger valores de parámetros `OUT`).
```sql
SET @id_cliente = 10;
SELECT * FROM clientes WHERE id_cliente = @id_cliente;

SELECT COUNT(*) INTO @total_clientes FROM clientes;
SELECT @total_clientes;
```
- Prefijo **`@`**.

### 2.2 Variables del sistema
| Tipo | Alcance | Ejemplo |
|---|---|---|
| **Global** | Servidor completo o conexiones nuevas | Nº máximo de conexiones |
| **De sesión** | Solo la conexión actual | Modo autocommit de la sesión |
| **Solo lectura** | No se puede modificar en ejecución | Versión del servidor |
| **Modificable** | Se puede cambiar con permisos adecuados | Zona horaria o modo SQL de la sesión |

```sql
SHOW VARIABLES LIKE 'autocommit';
SELECT @@session.autocommit;
SELECT @@global.max_connections;

SET SESSION autocommit = 0;          -- solo esta conexión
SET GLOBAL max_connections = 200;    -- requiere permisos
```
- `@@session` = valor de la sesión · `@@global` = valor global.
- Un `SET GLOBAL` puede **no mantenerse tras reiniciar** si no se guarda en el archivo de configuración.

| Característica | De usuario | Del sistema |
|---|---|---|
| Creador | El usuario | El programa |
| Duración | Solo en sesión | Depende del tipo (global, sesión, modificable, persistente, solo lectura) |

### 2.3 Variables locales (`DECLARE`)
- Solo existen **dentro del bloque** donde se declaran (procedimientos, funciones, triggers) y desaparecen al terminar.
- Se declaran con `DECLARE`, normalmente **al inicio del bloque**, indicando el **tipo**.

| Característica | Variable de usuario | Variable local |
|---|---|---|
| Ejemplo | `@total_clientes` | `v_total` |
| Declaración | `SET` o asignación directa | `DECLARE` |
| Ámbito | Sesión actual | Bloque procedimental |
| Duración | Hasta cerrar la conexión | Hasta que finaliza el bloque |
| Uso | Consultas temporales o pruebas | Procedimientos, funciones, triggers y cursores |

---

## 3. Funciones

Reciben datos, los procesan y **devuelven un resultado**. Pueden ser **predefinidas** (del SGBD) o **de usuario**.

### 3.1 Funciones escalares predefinidas
| Tipo | Uso | Ejemplo |
|---|---|---|
| Texto | Modificar o analizar cadenas | `UPPER(nombre)`, `LOWER(email)`, `CONCAT(nombre, ' ', apellidos)` |
| Numérica | Cálculos | `ROUND(precio, 2)`, `ABS(saldo)`, `CEIL(total)` |
| Fecha | Fechas y horas | `YEAR(fecha)`, `MONTH(fecha)`, `CURRENT_DATE` |
| Conversión | Cambiar el tipo | `CAST(total AS CHAR)`, `CONVERT(valor, DECIMAL)` |

- La sintaxis varía: el año de una fecha → `YEAR(fecha)` (MySQL/MariaDB) · `EXTRACT(YEAR FROM fecha)` (PostgreSQL) · `DATEPART(YEAR, fecha)` (SQL Server).

### 3.2 Agregación vs. ventana
| Función | Uso |
|---|---|
| `COUNT()` | Cuenta filas o valores no nulos |
| `SUM()` | Suma |
| `AVG()` | Media |
| `MIN()` / `MAX()` | Mínimo / máximo |

```sql
SELECT id_cliente, SUM(total) AS total_comprado
FROM pedidos GROUP BY id_cliente;                       -- agregación (reduce filas)

SELECT id_cliente, fecha_pedido, total,
       SUM(total) OVER (PARTITION BY id_cliente) AS total_cliente
FROM pedidos;                                           -- ventana (mantiene cada fila)
```
| Herramienta | Función |
|---|---|
| Agregación | Un resultado a partir de varias filas; suele reducir el nº de resultados |
| Ventana | Valores sobre un conjunto relacionado **manteniendo las filas individuales** |

### 3.3 Funciones de usuario
Rutina almacenada que recibe **parámetros**, ejecuta una lógica y **devuelve un resultado**.
- **Escalar:** un único valor.
- **De tabla:** un conjunto de filas y columnas.

```sql
CREATE FUNCTION calcular_precio_iva(p_precio DECIMAL(10,2))
RETURNS DECIMAL(10,2)
DETERMINISTIC
RETURN p_precio * 1.21;

SELECT nombre, precio, calcular_precio_iva(precio) AS precio_con_iva FROM productos;
```
```sql
-- Con CASE (función con estructura condicional)
CREATE FUNCTION obtener_descuento(p_categoria VARCHAR(20))
RETURNS DECIMAL(4,2) DETERMINISTIC
BEGIN
  DECLARE v_descuento DECIMAL(4,2);
  CASE p_categoria
    WHEN 'Oro'   THEN SET v_descuento = 0.20;
    WHEN 'Plata' THEN SET v_descuento = 0.10;
    ELSE              SET v_descuento = 0.00;
  END CASE;
  RETURN v_descuento;
END;
```
```sql
-- Función de tabla (PostgreSQL)
CREATE FUNCTION pedidos_cliente(p_id_cliente INT)
RETURNS TABLE(id_pedido INT, fecha_pedido DATE, total NUMERIC)
AS $$
BEGIN
  RETURN QUERY
  SELECT p.id_pedido, p.fecha_pedido, p.total
  FROM pedidos p WHERE p.id_cliente = p_id_cliente;
END;
$$ LANGUAGE plpgsql;
```
| Característica | Función escalar | Función de tabla |
|---|---|---|
| Resultado | Un único valor | Varias filas y columnas |
| Ejemplo | Calcular IVA | Devolver los pedidos de un cliente |
| Uso habitual | Expresiones, cálculos, columnas calculadas | Consultas reutilizables |
| Retorno | `DECIMAL`, `INT`, `VARCHAR`, `DATE` | Tabla o conjunto de resultados |

💡 Una función de usuario debe devolver **siempre** un resultado definido.

---

## 4. Estructuras de control de flujo

Definen qué instrucciones se ejecutan, en qué orden y bajo qué condiciones (dentro de procedimientos, funciones, triggers y bloques).

### 4.1 Condicionales
| Comando | Uso |
|---|---|
| `IF-THEN-ELSE` | Decide entre dos (o más) caminos |
| `CASE` | Evalúa múltiples escenarios (en consultas o en bloques) |

```sql
CREATE PROCEDURE comprobar_stock(IN p_id_producto INT)
BEGIN
  DECLARE v_stock INT;
  SELECT stock INTO v_stock FROM productos WHERE id_producto = p_id_producto;
  IF v_stock > 0 THEN
    SELECT 'Producto disponible' AS mensaje;
  ELSE
    SELECT 'Producto sin stock' AS mensaje;
  END IF;
END;
```
```sql
-- CASE dentro de una consulta (con operando)
SELECT nombre, categoria,
  CASE categoria
    WHEN 'Oro'   THEN '20% de descuento'
    WHEN 'Plata' THEN '10% de descuento'
    ELSE 'Sin descuento'
  END AS descuento_aplicable
FROM clientes;
```
```sql
-- CASE sin operando: rangos (recargo según días de retraso)
CREATE FUNCTION calcular_recargo(p_dias_retraso INT)
RETURNS DECIMAL(5,2) DETERMINISTIC
BEGIN
  DECLARE v_recargo DECIMAL(5,2);
  CASE
    WHEN p_dias_retraso <= 0  THEN SET v_recargo = 0.00;
    WHEN p_dias_retraso <= 7  THEN SET v_recargo = 5.00;
    WHEN p_dias_retraso <= 30 THEN SET v_recargo = 15.00;
    ELSE                           SET v_recargo = 30.00;
  END CASE;
  RETURN v_recargo;
END;
```
- **CASE con operando** → compara una categoría exacta. **CASE sin operando** → condiciones por rangos.

### 4.2 Bucles
| Comando | Función |
|---|---|
| `WHILE` | Repite **mientras** la condición sea verdadera |
| `LOOP` | Repite hasta que una condición interna lo detiene (con `LEAVE`) |
| `REPEAT` | Repite hasta que se cumple una condición de salida |
| `LEAVE` | **Sale** del bucle |
| `ITERATE` | **Salta** a la siguiente iteración |
| `FOR` | Disponible en algunos SGBD, con sintaxis propia |

```sql
-- WHILE
CREATE PROCEDURE contar_hasta_cinco()
BEGIN
  DECLARE v_contador INT DEFAULT 1;
  WHILE v_contador <= 5 DO
    SELECT v_contador AS numero;
    SET v_contador = v_contador + 1;
  END WHILE;
END;

-- LOOP con LEAVE
mi_bucle: LOOP
  IF v_contador > 5 THEN LEAVE mi_bucle; END IF;
  SELECT v_contador AS numero;
  SET v_contador = v_contador + 1;
END LOOP;

-- ITERATE (mostrar solo impares del 1 al 10)
mi_bucle: LOOP
  SET v_contador = v_contador + 1;
  IF v_contador > 10 THEN LEAVE mi_bucle; END IF;
  IF MOD(v_contador, 2) = 0 THEN ITERATE mi_bucle; END IF;
  SELECT v_contador AS numero_impar;
END LOOP;
```
- MySQL/MariaDB usa `WHILE`, `LOOP`, `REPEAT`; PostgreSQL y Oracle ofrecen variantes propias para `FOR` y otros bucles.
💡 Usar bucles con criterio y **preferir operaciones por conjuntos** cuando basten.

### 4.3 Criterios de uso: legibilidad, coste y anidamiento
- ❌ Exceso de anidamiento (IF dentro de IF dentro de IF…) → difícil de leer.
- ✅ Alternativa: **validar primero los casos de error** y dejar al final el caso correcto, con `ELSEIF`:
```sql
IF v_cliente_activo = FALSE THEN SET v_resultado = 'Cliente inactivo';
ELSEIF v_stock <= 0          THEN SET v_resultado = 'Sin stock';
ELSEIF v_pago_valido = FALSE THEN SET v_resultado = 'Pago no válido';
ELSE                              SET v_resultado = 'Pedido aceptado';
END IF;
```
| Criterio | Motivo |
|---|---|
| Nombres descriptivos de variables | Identificar su propósito sin releer el código |
| Evitar anidamiento excesivo | Menos errores, lógica más fácil de seguir |
| Comentar solo lo necesario | Explica la intención sin sobrecargar |
| Controlar **siempre la condición de salida** de los bucles | Evita bucles infinitos |
| Preferir operaciones por conjuntos | Evita procesar fila por fila |
| Dividir rutinas extensas | Probar, modificar y reutilizar cada parte |

---

## 5. Procedimientos almacenados y funciones de usuario

### 5.1 Diferencias
- **Procedimiento almacenado:** rutina guardada en el SGBD que agrupa instrucciones SQL y lógica procedimental para ejecutar una **tarea completa** (ej. registrar un cliente).
- **Función de usuario:** procesa datos y **devuelve un resultado** que se integra en consultas.

| Característica | Procedimiento almacenado | Función de usuario |
|---|---|---|
| Propósito | Ejecutar una tarea o proceso completo | Calcular y devolver un resultado |
| Resultado | Puede devolver datos con `OUT`, consultas o efectos sobre tablas | Debe devolver un valor o conjunto definido |
| Uso habitual | Automatizar operaciones complejas | Reutilizar cálculos en consultas |
| Ejecución | Normalmente con **`CALL`** | Dentro de `SELECT`, `WHERE` u otras expresiones |
| Ejemplo | Registrar una venta | Calcular el precio con IVA |

### 5.2 Crear, modificar y eliminar procedimientos: parámetros
| Parámetro | Qué hace |
|---|---|
| **`IN`** | **Entrada**: envía un dato al procedimiento; no se modifica fuera de la rutina |
| **`OUT`** | **Salida**: devuelve un valor calculado al exterior |
| **`INOUT`** | **Ambos**: recibe un valor inicial y devuelve ese mismo parámetro modificado |

```sql
-- IN
CREATE PROCEDURE buscar_cliente(IN p_id_cliente INT)
BEGIN
  SELECT * FROM clientes WHERE id_cliente = p_id_cliente;
END;

-- IN + OUT
CREATE PROCEDURE contar_pedidos_cliente(IN p_id_cliente INT, OUT p_total_pedidos INT)
BEGIN
  SELECT COUNT(*) INTO p_total_pedidos
  FROM pedidos WHERE id_cliente = p_id_cliente;
END;

-- INOUT
CREATE PROCEDURE aplicar_incremento(INOUT p_valor DECIMAL(10,2))
BEGIN
  SET p_valor = p_valor * 1.10;
END;
```
| Acción | Propósito |
|---|---|
| Crear (`CREATE PROCEDURE`) | Guardar un procedimiento nuevo |
| Modificar (`ALTER PROCEDURE`, en algunos SGBD) | Cambiar uno existente |
| Eliminar (`DROP`) | Borrar rutinas que ya no se necesitan |

- En otros SGBD lo habitual es **eliminar y volver a crear** la rutina.

### 5.3 Llamada a procedimientos y funciones
```sql
CALL buscar_cliente(10);                              -- procedimiento con IN

CALL contar_pedidos_cliente(10, @total);              -- procedimiento con OUT
SELECT @total AS total_pedidos;                       -- recoger el resultado

CALL registrar_venta(15, 3, 2, @resultado);           -- desde una aplicación (conector)

SELECT calcular_iva(100) AS precio_con_iva;           -- función escalar
SELECT nombre, calcular_iva(precio) AS precio_final FROM productos;
```
| Elemento | Forma de ejecución | Ejemplo |
|---|---|---|
| Procedimiento | `CALL nombre(...)` | `CALL buscar_cliente(10);` |
| Procedimiento con salida | `CALL ...` + variable de usuario | `CALL contar_pedidos_cliente(10, @total);` |
| Función escalar | Dentro de `SELECT` u otra expresión | `SELECT calcular_iva(precio)` |

---

## 6. Eventos y disparadores

Dos mecanismos para ejecutar acciones **automáticas**:
- **Disparador (trigger):** se activa por una **operación sobre una tabla**.
- **Evento programado:** se activa por una **planificación temporal**.

### 6.1 Disparadores (triggers)
- Rutina almacenada que se ejecuta automáticamente ante `INSERT`, `UPDATE` o `DELETE` sobre una tabla.
- Usos: validar datos, **auditorías**, historiales de cambios, reglas automáticas.
- Ejemplo: al cambiar el salario, guardar salario anterior, nuevo, fecha y usuario.

| Característica | Opción | Funcionamiento |
|---|---|---|
| Momento | **`BEFORE`** | Justo **antes** de guardar (comprobar o modificar datos) |
| Momento | **`AFTER`** | Justo **después** de completar la operación correctamente (auditorías, acciones posteriores) |
| Alcance | **`FOR EACH ROW`** | Una vez **por cada fila** afectada (3 filas → 3 ejecuciones) |
| Alcance | **`FOR EACH STATEMENT`** | **Una vez** por toda la operación (en SGBD que lo soporten) |

- MySQL/MariaDB: solo `FOR EACH ROW`. Los triggers de sentencia dependen de otros SGBD (PostgreSQL, Oracle).
💡 Úsalos con control para no dificultar el mantenimiento.

### 6.2 Creación y `OLD` / `NEW`
- **`OLD`**: valores **anteriores** de la fila. **`NEW`**: valores **nuevos**.
- Son **referencias**, no tablas temporales reales. Su disponibilidad depende de la operación (INSERT, UPDATE o DELETE).
```sql
CREATE TRIGGER auditoria_salario
AFTER UPDATE ON empleados
FOR EACH ROW
BEGIN
  IF OLD.salario <> NEW.salario THEN
    INSERT INTO auditoria_salarios
      (id_empleado, salario_anterior, salario_nuevo, fecha_cambio)
    VALUES
      (OLD.id_empleado, OLD.salario, NEW.salario, CURRENT_TIMESTAMP);
  END IF;
END;
```
- Si el campo admite **NULL**, usar comparación segura: `NOT (OLD.salario <=> NEW.salario)` en MySQL/MariaDB, o `IS DISTINCT FROM` en PostgreSQL.

### 6.3 Eventos programados (event scheduler)
- Tareas que se ejecutan en un momento concreto o de forma **periódica**; no dependen de una operación sobre una tabla.
- En MySQL/MariaDB hay que **activar el planificador** (con permisos):
```sql
SET GLOBAL event_scheduler = ON;
```
```sql
-- Periódico: limpiar carritos abandonados cada semana
CREATE EVENT limpiar_carritos_abandonados
ON SCHEDULE EVERY 1 WEEK
DO
  DELETE FROM carritos
  WHERE fecha_ultima_modificacion < NOW() - INTERVAL 30 DAY;

-- Una fecha concreta: cerrar una promoción
CREATE EVENT cerrar_promocion
ON SCHEDULE AT '2026-05-01 00:00:00'
DO
  UPDATE promociones SET activa = FALSE WHERE id_promocion = 10;
```

| | Trigger | Evento |
|---|---|---|
| Se activa por | Operación sobre una tabla (INSERT/UPDATE/DELETE) | Tiempo (fecha o periodicidad) |

---

## 7. Excepciones

Errores en rutinas: claves duplicadas, violaciones de restricciones, datos inválidos, consultas sin resultados. Se gestionan con **manejadores** (`DECLARE HANDLER` en MySQL/MariaDB): permiten registrar el fallo, revertir una transacción, finalizar el bloque o continuar de forma controlada.

### 7.1 Excepción y `DECLARE HANDLER`
- Una **excepción no gestionada** interrumpe la rutina y puede propagarse a la aplicación → anticipar los fallos previsibles.
- **`CONTINUE HANDLER`**: tras gestionar el error, **continúa** la rutina. **`EXIT HANDLER`**: **sale** del bloque.

| Concepto | Definición | Comportamiento típico |
|---|---|---|
| Procedimiento | Rutina almacenada que ejecuta instrucciones | Pasos reutilizables, se invoca con `CALL` |
| Excepción | Condición de error o aviso durante la ejecución | Se puede capturar con `DECLARE HANDLER` |
| `DECLARE HANDLER` | Define cómo responder ante una condición | Continuar, salir o registrar el error |

```sql
-- CONTINUE HANDLER: registra el error y sigue
CREATE PROCEDURE insertar_cliente(IN p_id_cliente INT, IN p_nombre VARCHAR(100))
BEGIN
  DECLARE CONTINUE HANDLER FOR SQLEXCEPTION
  BEGIN
    INSERT INTO log_errores(mensaje, fecha_error)
    VALUES ('Error al insertar cliente', NOW());
  END;
  INSERT INTO clientes(id_cliente, nombre) VALUES (p_id_cliente, p_nombre);
END;

-- EXIT HANDLER con transacción: ROLLBACK + registro
CREATE PROCEDURE registrar_venta(IN p_id_cliente INT, IN p_id_producto INT, IN p_cantidad INT)
BEGIN
  DECLARE EXIT HANDLER FOR SQLEXCEPTION
  BEGIN
    ROLLBACK;
    INSERT INTO log_errores(mensaje, fecha_error)
    VALUES ('Error al registrar la venta', NOW());
  END;
  START TRANSACTION;
  INSERT INTO pedidos(id_cliente, fecha) VALUES (p_id_cliente, NOW());
  UPDATE productos SET stock = stock - p_cantidad WHERE id_producto = p_id_producto;
  COMMIT;
END;
```

### 7.2 Tipos de condiciones
| Aviso | Significado | Acción recomendada |
|---|---|---|
| **`SQLEXCEPTION`** | **Error grave** (clave duplicada, violar una FK, tipo de dato incorrecto) | Detener, revertir, registrar o continuar de forma controlada, según el caso |
| **`SQLWARNING`** | **Advertencia** de un suceso inusual (el sistema puede continuar) | Registrar, revisar y continuar si procede |
| **`NOT FOUND`** | **No quedan datos** (consulta o cursor sin más filas) | Terminar la búsqueda |

- Cursores y disparadores generan estos avisos al terminar una lectura o detectar problemas.
💡 Capturar una condición permite decidir cómo actuar ante errores, advertencias o fin de datos.

### 7.3 Propagación, registro y recuperación
- **Propagación de errores:** un fallo inicial provoca fallos en otras partes (ej. un procedimiento llama a otras rutinas y el error se propaga a ellas).
- Causas: datos inválidos, violaciones de restricciones, errores de lógica, problemas de conexión, incidencias en una transacción.
- **Estrategias:**
  - Registrar el fallo automáticamente.
  - Revertir cambios (`ROLLBACK`).
  - Avisar a la aplicación.
  - Continuar **solo si no se compromete la integridad** de los datos.

---

## 8. Cursores

**Cursor:** mecanismo para recorrer **secuencialmente**, **fila a fila**, el resultado de una consulta (obtener una fila, procesarla y avanzar). Se usa dentro de procedimientos o bloques procedimentales.

⚠️ **Con precaución:** normalmente conviene **operar por conjuntos** (`UPDATE`, `INSERT INTO ... SELECT`, `JOIN`, agregaciones) en vez de recorrer fila a fila. Antes de usar un cursor, comprobar si se resuelve con SQL declarativo.

### 8.1 + 8.2 Ciclo de vida: DECLARE → OPEN → FETCH → CLOSE
| Fase | Comando | Acción | Ejemplo |
|---|---|---|---|
| 1. Declaración | `DECLARE` | Le da un nombre y lo vincula a una consulta (`SELECT`) | `DECLARE cursor_empleados CURSOR FOR SELECT ...` |
| 2. Apertura | `OPEN` | Prepara el conjunto de resultados | `OPEN cursor_empleados` |
| 3. Lectura | `FETCH` | Recupera la siguiente fila y avanza | `FETCH cursor_empleados INTO ...` |
| 4. Cierre | `CLOSE` | **Libera recursos** | `CLOSE cursor_empleados` |

- Se hace `FETCH` hasta que un **handler `NOT FOUND`** indica que no quedan filas → salida ordenada del bucle.
💡 **Siempre ejecutar `CLOSE`** al terminar el recorrido.

### 8.3 Implícitos y explícitos
| Tipo | Cómo funciona |
|---|---|
| **Implícito** | Lo crea y maneja el sistema **automáticamente** |
| **Explícito** | Lo programas tú: hay que abrirlo, recorrerlo y cerrarlo **manualmente** (más control, pero **suelen ser menos eficientes** con grandes volúmenes porque procesan fila a fila) |

- Alternativa: **conjuntos de resultados** (consultas directas sobre bloques enteros) → menos operaciones sobre la BD.

**Ejemplo completo:**
```sql
CREATE PROCEDURE listar_clientes_activos()
BEGIN
  DECLARE v_id INT;
  DECLARE v_nombre VARCHAR(100);
  DECLARE fin INT DEFAULT 0;
  DECLARE cur_clientes CURSOR FOR
    SELECT id_cliente, nombre FROM clientes WHERE activo = 1;
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET fin = 1;

  OPEN cur_clientes;
  bucle: LOOP
    FETCH cur_clientes INTO v_id, v_nombre;
    IF fin = 1 THEN
      LEAVE bucle;
    END IF;
    -- procesamiento de cada fila
  END LOOP;
  CLOSE cur_clientes;
END;
```
- El `CONTINUE HANDLER FOR NOT FOUND` evita que el procedimiento acabe con error cuando `FETCH` ya no encuentra filas: activa la variable de control que provoca la salida con `LEAVE`.

---

## Glosario rápido
| Término | Definición |
|---|---|
| Ámbito de sesión | Duración de un valor/configuración asociada a una conexión |
| Anidamiento | Colocar una estructura de control dentro de otra repetidamente |
| Bloque anónimo | Instrucciones que se ejecutan de inmediato y no se guardan |
| Cursor | Recorre fila a fila el resultado de una consulta (`DECLARE`, `OPEN`, `FETCH`, `CLOSE`) |
| `DECLARE` | Declara variables locales, cursores o manejadores |
| Disparador (trigger) | Rutina automática ante INSERT/UPDATE/DELETE (BEFORE/AFTER; FOR EACH ROW/STATEMENT) |
| Evento programado | Tarea automática en un momento exacto o periódica |
| Excepción | Error o aviso que altera la ejecución normal (se gestiona con manejadores) |
| Función escalar / de tabla | Devuelve un valor / un conjunto de filas |
| Función de usuario | Rutina del programador que recibe parámetros y devuelve un resultado |
| Función de ventana | Calcula sobre filas relacionadas sin agruparlas en una sola |
| `IN`, `OUT`, `INOUT` | Modos de paso de parámetros |
| `OLD` / `NEW` | Valores de la fila antes / después de la operación (en triggers) |
| Procedimiento almacenado | Rutina del SGBD que agrupa SQL y lógica para una tarea reutilizable |
| Propagación de errores | Un fallo inicial provoca fallos en otras partes |
| `SQLEXCEPTION` / `SQLWARNING` / `NOT FOUND` | Condiciones que captura un handler: errores / avisos / sin más filas |
| Variable | Espacio de memoria para datos temporales |

---

## ✅ Chuleta final
- Procedimiento = **hace** una tarea (`CALL`) · Función = **devuelve** un valor (se usa en `SELECT`).
- Parámetros: `IN` entra · `OUT` sale · `INOUT` entra y sale.
- Variables: `@usuario` (sesión) · `DECLARE` local (bloque) · del sistema (`SET` / `SHOW`, `@@session`, `@@global`).
- Control de flujo: `IF` / `CASE` · bucles `WHILE`, `LOOP`, `REPEAT` · `LEAVE` = salir, `ITERATE` = siguiente vuelta · **siempre una condición de salida**.
- Trigger: `BEFORE`/`AFTER` + `FOR EACH ROW`; usa `OLD` y `NEW`. Evento = por tiempo (activar `event_scheduler`).
- Handlers: `CONTINUE` sigue · `EXIT` sale · condiciones: `SQLEXCEPTION`, `SQLWARNING`, `NOT FOUND`.
- Cursor: **DECLARE → OPEN → FETCH → CLOSE** (+ handler `NOT FOUND`). Mejor operar por conjuntos.
- La sintaxis cambia según el SGBD: PL/SQL (Oracle), T-SQL (SQL Server), PL/pgSQL (PostgreSQL), SQL/PSM (MySQL/MariaDB).
