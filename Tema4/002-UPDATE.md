### 1.2 UPDATE

La sentencia `UPDATE` se utiliza para modificar datos que ya existen en una tabla. Esta operacion aparece en casos como actualizar.

```sql
--Sixtaxis
UPDATE tabla
SET columna = nuevo_valor
WHERE condicion;
```

`WHERE` indica qué registros cambian. Sin`WHERE` se actualizan todas filas de la tabla.

Antes de un cambio masivo, comprobar con `SELECT` las filas afectadas.

```sql
UPDATE productos
SET precio = precio * 1.10
WHERE categoria = 'Fruta' AND nombre = 'Manzana';

-- Comprobación previa
SELECT * 
FROM productos
WHERE categoria = 'Fruta' AND nombre = 'Manzana';
```

```sql
--Error típico
UPDATE productos 
SET precio = precio * 1.10;
``` 

En este caso, al no exisistir `WHERE` se actualizarian todos los productos de la tabla, no solo las manzanas. 

Tambien pueden utilizarse subconsultas para calcular el nuevo valor o para seleccinar los registros que se van modificar. 

```sql 
UPDATE empleados
SET salario = salario * 1.05
WHERE id_departamento IN(
	SELECT id_departamento
	FROM departaments
	WHERE nombre = 'Ventas'
	);
```

Aqui se actualuza el salario de los emppleados que pertenecen al departamento de Ventas, usando una subconsulta para localozar primero el identificador del departamento. 

