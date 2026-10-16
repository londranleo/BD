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