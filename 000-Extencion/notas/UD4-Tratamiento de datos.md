
# UD4 · Tratamiento de datos

> Cómo **modificar** una base de datos (INSERT, UPDATE, DELETE), mantener la **coherencia** entre tablas y controlar **transacciones** y **concurrencia**.

## Índice

1. Inserción, supresión y modificación
2. Integridad referencial
3. Subconsultas y composiciones en edición
4. Transacciones
5. Bloqueos y concurrencia

---

## 1. Inserción, supresión y modificación

|Operación|Acción|
|---|---|
|`INSERT`|Añadir información nueva|
|`UPDATE`|Cambiar información|
|`DELETE`|Quitar filas concretas|
|`TRUNCATE`|Vaciar la tabla entera|

### 1.1 INSERT

La instruccion `INSERT` se utiliza para anadir nuevos registro a una tabla.

Se puede insertar Una fila, varias filas o copiar desde otra tabla (`INSERT SELECT`).

La forma mas Habitual de usar `INSERT` consiste en Indicar siempre la tabla, las columnas y los valores correspodientes.

```sql
-- Una fila
INSERT INTO clientes (nombre, email, ciudad)
VALUES ('Ana López', 'ana@email.com', 'Madrid');

-- Varias filas
INSERT INTO clientes (nombre, email, ciudad)
VALUES ('Luis García', 'luis@email.com', 'Valencia'),
       ('Marta Ruiz', 'marta@email.com', 'Sevilla');

-- INSERT SELECT (copiar de otra tabla)
INSERT INTO clientes_historico (id_cliente, nombre, email, ciudad)
SELECT id_cliente, nombre, email, ciudad
FROM clientes
WHERE activo = false;
```

`INSERT SELECT` se usa en: migraciones, copias históricas, procesos ETL.

La bases de datos aplican restricciones, PK, FK y transacciones para rechazar insercciones que no cumplan las reglas definidas. 

Si una inserccion imcumple una restriccion, el sistema rechaza la operacion.

### 1.2 UPDATE

La sentencia `UPDATE` se utiliza para modificar datos que ya existen en una tabla. Esta operacion aparece en casos como actualizar.

```sql
--Sixtaxis
UPDATE tabla
SET columna = nuevo_valor
WHERE condicion;
```


- **WHERE** dice qué filas cambian. **Sin WHERE → se actualizan TODAS.**
- Antes de un cambio masivo: comprobar con `SELECT` las filas afectadas.

```sql
UPDATE productos
SET precio = precio * 1.10
WHERE categoria = 'Fruta' AND nombre = 'Manzana';

-- Comprobación previa
SELECT * FROM productos
WHERE categoria = 'Fruta' AND nombre = 'Manzana';
```

- ❌ Error típico: `UPDATE productos SET precio = precio * 1.10;` (sin WHERE).
- Se pueden usar subconsultas para calcular el valor o elegir filas.

### 1.3 DELETE y TRUNCATE

Eliminar datos es una operacion delicada, ya que pueden afectra a la coherecia de toda la BD. En SQL existen dos instrucciones para borrar infromacion `DELETE` Y `TRUNCATE`.

La sentecia `DELETE` permite iliminar registros concretos de una tabla. 
Se utiliza con una condicion `WHERE` para indicar que filas deben borrarse.

```sql
DELETE FROM clientes 
WHERE id_cliente = 15;
```

Esta instrucion borra el cliente cuyo identificador es 15, siempre las restricciones de la BD lo permita. 

Es posible eliminar varios registros aplicando una condicion.

```sql
DELETE FROM clientes 
WHERE activo = false; 
```

Esta elimina los clientes marcados como inactivos. conviene comprobar la seleccion con una consulta `SELECT`.

Por lo contrario, `TRUNCATE` vacia una tabla completa con un comportamiento que depende del SGBD.

```sql
TRUNCATE TABLE clientes_temporales;   
```

Esto elimina todos los registros de la tabla cleinte_temporales. No permite indicar una condion `WHERE`, 

|Característica|DELETE|TRUNCATE|
|---|---|---|
|Alcance|Filas concretas (con condición)|Toda la tabla, **sin WHERE**|
|Borrado|Fila por fila|Todo de golpe|
|Recuperación|Se puede deshacer si la transacción no está confirmada|Depende del SGBD y del contexto transaccional|

- No decir que TRUNCATE "nunca" se deshace: en unos SGBD forma parte de la transacción, en otros provoca **confirmación implícita** o tiene restricciones. → Consultar la documentación del SGBD.
- Hay que respetar claves foráneas: no se borra un cliente con pedidos (salvo acción referencial).

**Borrado relacionado controlado (transacción):**

```sql
BEGIN;

DELETE FROM pedidos 
WHERE id_cliente = 15;  -- primero los hijos

DELETE FROM clientes 
WHERE id_cliente = 15;  -- luego el padre
COMMIT;  -- si falla: ROLLBACK
```

**Alternativa: borrado en cascada** (solo si la FK se definió así):

```sql
FOREIGN KEY (id_cliente)
REFERENCES clientes(id_cliente)
ON DELETE CASCADE
```

Revisarlo antes: puede borrar muchos registros dependientes.

---

## 2. Integridad referencial

Los datos de un sistema se conectan entre si para tener sentido, Si se elimina o modifica, pueden quedar registros sin refencia lo que incoherecias.

**Qué es:** regla de las BD relacionales que mantiene la coherencia entre **claves primarias** y **claves foráneas**. Evita **registros huérfanos**.

- **Clave primaria (PK):** identifica de forma única cada registro.
- **Clave foránea (FK):** campo de otra tabla que apunta a esa PK.

```sql
--Ejmeplo de relaciones entre tablas
CREATE TABLE clientes (
  id_cliente INT PRIMARY KEY,
  nombre VARCHAR(100)
);

CREATE TABLE pedidos (
  id_pedido INT PRIMARY KEY,
  fecha DATE,
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente)
);
```

La tabla pedidos solo puede registrar pedidos asociados a `clientes` existentes. Si se inserta un `pedidos` con un `id_cliente` que no existe en la tabla clientes, la base de datos rechaza la operaciion.

Añadir, modificar o eliminar datos afecta directamente a estas relaciones, hay que comprobar si otros datos dependen de ella.

Si una operacion incumple estas reglas, el SGBD detiene la operacion para proteger la coherecia de la imformacion. 

### 2.1 Vocabulario

|Término|Significado|Ejemplo|
|---|---|---|
|Tabla padre / principal|Contiene el registro de referencia|clientes|
|Tabla hija / dependiente|Registros que dependen del padre|pedidos|
|Clave primaria|Identifica el registro principal|id_cliente|
|Clave foránea|Enlaza hijo con padre|pedidos.id_cliente|

>La integridad referencial evita modificar o borrar datos si eso deja
>información incompleta.

### 2.2 Qué comprueba cada operación

- **INSERT:** que la FK exista en la tabla padre.

```sql
INSERT INTO pedidos (id_pedido, fecha, id_cliente)VALUES (101, '2026-04-28', 999);  -- falla si el cliente 999 no existe
```

- **UPDATE:** cambios en PK/FK → hay que decidir qué pasa con los hijos.
- **DELETE:** si hay hijos → se impide el borrado o se aplica una acción referencial.
- Las acciones en cascada **no son automáticas**: solo si se definen (`ON DELETE CASCADE`, `ON UPDATE CASCADE`).

|Acción referencial|Comportamiento|
|---|---|
|`RESTRICT`|Impide borrar/actualizar el padre si hay hijos|
|`NO ACTION`|Similar a RESTRICT; se comprueba al final de la operación o transacción (según SGBD)|
|`CASCADE`|Propaga el borrado o la actualización a los hijos|
|`SET NULL`|Pone NULL en la FK de los hijos (si la columna lo permite)|
|`SET DEFAULT`|Pone el valor por defecto en la FK (si está definido)|

```sql
-- Cascada
FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente) ON DELETE CASCADE
-- Restricción
FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente) ON DELETE RESTRICT
```

- CASCADE: al borrar un cliente se borran sus pedidos (¡con precaución!).
- RESTRICT: no se puede borrar un cliente mientras tenga pedidos.

### 2.3 Violaciones de integridad referencial

Ocurre cuando una operación intenta romper la relación entre dos tablas → el SGBD la **bloquea**.

Casos habituales:

1. **INSERT** en la hija con FK inexistente (ej. cliente 50).
2. **DELETE** del padre que aún tiene hijos (ej. cliente 10).

**Estrategias para resolverlo:**

1. Comprobar con `SELECT * FROM pedidos WHERE id_cliente = 10;`
2. Borrar primero los hijos y luego el padre, **preferiblemente en transacción**.
3. Definir acciones referenciales adecuadas (`CASCADE`, `RESTRICT`, `SET NULL`).
4. Evitar modificar PK que ya se usan en otras tablas (salvo regla referencial clara).

|Operación|Causa del error|Solución|
|---|---|---|
|INSERT|FK que no existe en la tabla principal|Insertar antes el principal o corregir la FK|
|UPDATE|Se cambia PK/FK y se rompe la relación|Revisar la relación; `ON UPDATE CASCADE` si procede|
|DELETE|Borrar padre con hijos|Borrar hijos primero, usar transacción o `ON DELETE CASCADE`|
|DELETE|Conservar hijos sin borrar todo|`ON DELETE SET NULL` (si la FK admite nulos)|

💡 Agrupar operaciones relacionadas en **transacciones** para que un fallo no deje la información a medias.

---

## 3. Subconsultas y composiciones en sentencias de edición

- **Subconsulta:** consulta dentro de otra instrucción SQL.
- Se usa en `UPDATE`, `DELETE` e `INSERT INTO ... SELECT` para elegir con precisión las filas.
- Las **composiciones (JOIN)** en edición existen, pero **la sintaxis cambia según el SGBD** (MySQL, PostgreSQL, SQL Server, Oracle).
- ✅ Regla de oro: **probar la condición con un `SELECT`** antes de modificar, borrar o copiar.

### 3.1 UPDATE con subconsulta

**En WHERE** (elegir filas):

```sql
UPDATE empleados
SET salario = salario * 1.05
WHERE id_departamento IN (
  SELECT id_departamento
  FROM departamentos
  WHERE nombre = 'Ventas'
);
```

Comprobación previa: el mismo `WHERE ... IN (...)` pero con `SELECT * FROM empleados`.

**En SET** (calcular el valor):

```sql
UPDATE productos
SET precio = (
  SELECT precio_recomendado
  FROM catalogo_precios
  WHERE catalogo_precios.id_producto = productos.id_producto
)
WHERE id_producto IN (SELECT id_producto FROM catalogo_precios);
```

→ Sincroniza precios con otra tabla sin actualizar a mano.

### 3.2 DELETE con subconsulta y con JOIN

```sql
-- Subconsulta
DELETE FROM pedidos
WHERE id_cliente IN (
  SELECT id_cliente FROM clientes WHERE activo = false
);
```

Comprobar antes con `SELECT * FROM pedidos WHERE id_cliente IN (...)`.

```sql
-- MySQL (JOIN)
DELETE pedidos
FROM pedidos
INNER JOIN clientes ON pedidos.id_cliente = clientes.id_cliente
WHERE clientes.activo = false;

-- PostgreSQL (USING)
DELETE FROM pedidos
USING clientes
WHERE pedidos.id_cliente = clientes.id_cliente
  AND clientes.activo = false;
```

- Misma lógica, distinta sintaxis → revisar las particularidades del SGBD.
- 💡 En DELETE incluir condición si no se quiere borrar toda la tabla, y comprobar antes con SELECT.
- Según SGBD y nivel de aislamiento, un borrado puede **bloquear** temporalmente las filas hasta que acabe la transacción.

### 3.3 INSERT con SELECT (ETL y migraciones)

- `INSERT ... VALUES` → valores escritos a mano.
- `INSERT ... SELECT` → datos obtenidos de una consulta.
- Útil para: copiar entre tablas, crear históricos, preparar informes, migraciones (cambio de programa y traslado del historial).
- En traslados masivos → usar **transacciones** (se completa todo o se deshace).

**ETL:**

|Fase|Qué hace|
|---|---|
|Extracción|Obtener datos de las tablas origen|
|Transformación|Adaptar, filtrar o calcular los datos|
|Carga|Insertar en la tabla destino|

```sql
-- Con transformación (unir nombre y apellidos)
INSERT INTO clientes_historico (id_cliente, nombre_completo, email)
SELECT id_cliente, CONCAT(nombre, ' ', apellidos), email
FROM clientes
WHERE activo = false;

-- Copia de respaldo antes de limpiar
INSERT INTO pedidos_historico (id_pedido, id_cliente, fecha, total)
SELECT id_pedido, id_cliente, fecha, total
FROM pedidos
WHERE fecha < '2024-01-01';

-- Tras comprobar la copia, se borra el original
DELETE FROM pedidos WHERE fecha < '2024-01-01';
```

💡 `INSERT ... SELECT` copia en una sola instrucción, pero en migraciones grandes hay que combinarlo con comprobaciones y transacciones.

---

## 4. Transacciones

**Transacción:** conjunto de operaciones tratado como **una única unidad lógica** → se confirman todas o se deshacen todas.

- Ejemplo: transferencia bancaria (restar en una cuenta + sumar en otra).
- Un conjunto de sentencias agrupadas = **guion / script de transacción**.
- Ayudan a la integridad, consistencia y fiabilidad (ej. registrar pedido + descontar stock + generar factura).

### 4.1 Propiedades ACID

|Propiedad|Significado|Ejemplo|
|---|---|---|
|**A**tomicidad|Se ejecuta completa o no se ejecuta nada|No restar dinero sin sumarlo en otra cuenta|
|**C**onsistencia|Respeta las reglas de la BD (PK, FK, restricciones, tipos)|No registrar pedido de cliente inexistente|
|**A**islamiento|Las transacciones simultáneas no interfieren incorrectamente|Dos usuarios no sobrescriben el mismo dato sin control|
|**D**urabilidad|Lo confirmado (COMMIT) se conserva aunque haya un fallo después|Una venta confirmada sigue tras reiniciar|

- Consistencia: pasa de un estado válido a otro estado válido.
- Aislamiento: según el nivel configurado, se ven o no cambios de otras transacciones sin confirmar.

```sql
BEGIN;
UPDATE cuentas SET saldo = saldo - 100 WHERE id_cuenta = 1;
UPDATE cuentas SET saldo = saldo + 100 WHERE id_cuenta = 2;
COMMIT;   -- si una falla → ROLLBACK
```

### 4.2 Control de transacciones

|Comando|Función|Efecto|
|---|---|---|
|`BEGIN` / `START TRANSACTION`|Iniciar la transacción|Abre el bloque|
|`COMMIT`|Guardar los cambios|Los hace persistentes|
|`ROLLBACK`|Deshacer la transacción|Revierte cambios no confirmados|
|`SAVEPOINT`|Crear un punto de control|Permite volver a un punto concreto|

```sql
BEGIN;
UPDATE productos SET stock = stock - 1 WHERE id_producto = 20;
INSERT INTO pedidos (id_cliente, fecha, total)
VALUES (999, CURRENT_DATE, 49.99);   -- cliente 999 no existe → falla
ROLLBACK;                             -- deshace también el UPDATE del stock
```

**SAVEPOINT** (procesos largos):

```sql
SAVEPOINT punto_control;
ROLLBACK TO SAVEPOINT punto_control;
```

→ Deshace solo lo posterior al punto, sin anular el resto.

### 4.3 Transacciones implícitas y explícitas

|Tipo|Quién controla|Detalle|
|---|---|---|
|**Implícita**|El SGBD|Modo **autocommit** (activado por defecto en muchos SGBD): cada INSERT/UPDATE/DELETE se confirma solo. Una vez confirmado, **no se puede deshacer** con ROLLBACK|
|**Explícita**|El programador|Define inicio (`BEGIN`) y final (`COMMIT`/`ROLLBACK`)|

- Explícitas: recomendadas cuando la operación tiene varios pasos (venta + stock + factura + movimiento contable).
- ⚠️ Algunas instrucciones DDL (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`) pueden comportarse de forma especial dentro de una transacción según el SGBD → consultar la documentación.

---

## 5. Políticas de bloqueo. Concurrencia

**Concurrencia:** varios usuarios o procesos acceden a la misma BD **a la vez** (ej. dos personas comprando la última entrada).

**Mecanismos del SGBD:**

- **Transacciones:** agrupan operaciones en una unidad.
- **Niveles de aislamiento:** qué puede ver una transacción respecto a otras.
- **Bloqueos:** controlan el acceso simultáneo a filas, tablas, etc.
- **MVCC (multiversión):** leer versiones confirmadas sin bloquear siempre a los lectores.

### 5.1 Problemas de concurrencia (anomalías)

|Problema|Qué ocurre|
|---|---|
|**Lectura sucia**|Se lee un dato modificado por otra transacción **sin COMMIT**; si hace ROLLBACK, el dato nunca existió|
|**Lectura no repetible**|Se lee el mismo registro 2 veces y da valores distintos (otra transacción lo modificó y confirmó entre medias)|
|**Lectura fantasma**|La misma consulta (misma condición) devuelve filas distintas (otra transacción insertó/eliminó filas)|
|**Escritura perdida**|Dos transacciones modifican el mismo dato y una sobrescribe el cambio de la otra|

- No siempre dan errores visibles, pero producen resultados incorrectos (ventas, reservas, informes, movimientos bancarios).
- Escritura perdida: grave en inventario, saldos, reservas, edición compartida.

```sql
-- Ejemplo de lectura sucia
-- Transacción A
BEGIN;
UPDATE productos SET stock = 0 WHERE id_producto = 10;   -- sin COMMIT
-- Transacción B
SELECT stock FROM productos WHERE id_producto = 10;      -- podría leer 0
-- Si A hace ROLLBACK, B leyó un valor que nunca fue definitivo
```

💡 Se previenen con reglas claras que organicen a los usuarios que acceden al mismo dato a la vez.

### 5.2 Niveles de aislamiento

|Nivel|Qué hace|Protección|Velocidad|
|---|---|---|---|
|`READ UNCOMMITTED`|Más permisivo; permite lecturas sucias (no apto para operaciones críticas)|Muy baja|Muy alta|
|`READ COMMITTED`|Solo lee datos confirmados; evita lecturas sucias, pero la misma consulta puede dar resultados distintos|Básica|Alta|
|`REPEATABLE READ`|Un registro leído no cambia al releerlo; según SGBD protege de algunas lecturas fantasma|Alta|Media|
|`SERIALIZABLE`|Más estricto; equivale a ejecutar las transacciones una tras otra; puede reducir rendimiento o aumentar conflictos|Muy alta|Baja|

- Valores **orientativos**: el rendimiento real depende del SGBD, las consultas, los índices y la carga.
- Se elige según equilibrio **seguridad ↔ rendimiento** (consulta estadística tolera variación; operación bancaria o reserva exige más control).

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN;
SELECT * FROM reservas WHERE id_evento = 10;
COMMIT;
```

(Sintaxis MySQL/MariaDB; en PostgreSQL `SET TRANSACTION` va **después** de `BEGIN`.)

💡 A mayor aislamiento → más anomalías evitadas, pero más bloqueos y menos rendimiento.

### 5.3 Tipos de bloqueo y deadlocks

**Por modo:**

|Tipo|Uso|Permiso para otros|
|---|---|---|
|Compartido|Lectura|Leer de forma simultánea|
|Exclusivo|Escritura (UPDATE, DELETE, algunos INSERT)|Impide escrituras incompatibles; puede limitar ciertas lecturas (según SGBD y aislamiento)|

**Por alcance:**

|Bloqueo|Efecto|
|---|---|
|De **fila**|Afecta solo a los registros implicados → **más concurrencia**|
|De **tabla**|Afecta a toda la tabla → puede **reducir el rendimiento**|

**Bloqueo explícito de filas:**

```sql
BEGIN;
SELECT * FROM productos
WHERE id_producto = 10
FOR UPDATE;                      -- bloquea las filas seleccionadas
UPDATE productos SET stock = stock - 1 WHERE id_producto = 10;
COMMIT;
```

(Sintaxis y comportamiento pueden variar según el SGBD.)

**Deadlock (bloqueo mutuo):** dos transacciones esperan indefinidamente porque cada una tiene un recurso que la otra necesita.

- A bloquea la fila 1 y necesita la 2.
- B bloquea la fila 2 y necesita la 1.
- El SGBD detecta el deadlock y **cancela una** transacción; la cancelada normalmente **debe repetirse**.

**Cómo reducir el riesgo:**

- Transacciones lo más cortas posible.
- Acceder siempre a tablas y filas en el **mismo orden**.
- Crear **índices** adecuados (evitan bloqueos más amplios de lo necesario).
- No dejar una transacción abierta mientras el usuario decide.
- Controlar errores y **reintentar** si el SGBD cancela por deadlock.
- Usar el nivel de aislamiento adecuado, sin aplicar siempre el más restrictivo.

---

## Glosario rápido

|Término|Definición|
|---|---|
|ACID|Propiedades de las transacciones: atomicidad, consistencia, aislamiento, durabilidad|
|Concurrencia|Varios usuarios/procesos accediendo a la BD a la vez|
|Deadlock|Bloqueo mutuo entre 2+ transacciones que se esperan indefinidamente|
|DML|Lenguaje de manipulación de datos (INSERT, UPDATE, DELETE) sin modificar la estructura de las tablas|
|Escritura perdida|Una transacción sobrescribe el cambio de otra|
|Integridad referencial|Relaciones entre tablas correctas y sin datos huérfanos|
|Lectura fantasma|Misma consulta devuelve filas distintas por inserciones/borrados ajenos|
|Lectura no repetible|Mismo registro leído dos veces con valores distintos|
|Lectura sucia|Leer datos sin confirmar que luego se deshacen|
|MVCC|Control de concurrencia multiversión: leer versiones confirmadas sin bloquear siempre a los lectores|
|Nivel de aislamiento|Cuánto ve una transacción de los cambios de otras (consistencia ↔ rendimiento)|
|Registro|Fila de una tabla con información de un elemento único|
|SAVEPOINT|Punto de control dentro de una transacción|
|Subconsulta|Consulta dentro de otra instrucción SQL|
|Transacción|Grupo de operaciones que se confirman o deshacen juntas|

---

## ✅ Chuleta final

- `UPDATE`/`DELETE` → **siempre WHERE** y comprobar antes con `SELECT`.
- `TRUNCATE` → todo, sin WHERE; su recuperación **depende del SGBD**.
- FK → el hijo solo apunta a padres que existen.
- `CASCADE` solo si se define; úsalo con cuidado.
- Varios pasos relacionados → **transacción** (`BEGIN` … `COMMIT`/`ROLLBACK`).
- Autocommit → lo confirmado ya no se deshace.
- Más aislamiento = más seguridad, menos rendimiento.
- Deadlock → transacciones cortas, mismo orden, reintentar.

