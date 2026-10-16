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