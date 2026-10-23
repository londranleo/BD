Un índice simple es un indice creado sobre una sola columna de una tabla.

Un índice compuesto es un índice creado sobre dos o mas columnas de una tablas.

En un índice compuesto **el orden de las columnas importa**. Suele ir primero la de mayor **selectividad**, si es el filtro principal de las consultas.
- **Selectividad**: capacidad de los datos para descartar opciones y localizar resultados únicos. Ejemplo: un teléfono es mucho más selectivo que un país.
- El orden ideal depende de cómo se escriban las consultas: no es lo mismo buscar por teléfono, por ciudad o por las dos.