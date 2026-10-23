# 3. Tipos de datos

Usar tipos de datos adecuados aporta varias ventajas, La information es coherente y hay menos errores. Se ocupa menos espacio, así que las consultas, ordenación y filtros son mas rápidos. 

# 3.1 Tips de datos numéricos 

Las tablas de datos se conectan mediante claves de identification que suelen usar numerous para identificar y relacionar registros. 

- `Enteros`: Cantidades completas sin fracciones como la edad de una persona el numero de unidades en stock o el identificador de un registro. 
- `Decimales`:  Sirven para guardar valores exactos con fracciones como el precio de un producto.
- `Coma flotante`: cantidad variable de decimales, adecuada para mediciones científicas. Puede introducir pequeñas aproximaciones, así que **no** es buena opción para importes monetarios.

La capacidad de estos números se configura con dos propiedades el `rango` y `precision`. 

El `rango` marca los limites del numero y la `precision` define cuantos dígitos exactos se puede guardar. 
