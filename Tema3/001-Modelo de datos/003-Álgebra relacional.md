## 1.3. Álgebra relacional: selección, proyección, unión, diferencia y producto cartesiano

El álgebra relacional es un sistema de reglas para buscar información. Describe operaciones sobre tablas y es la base teórica de muchas consultas SQL.

- **Selección**: elige **filas** completas que cumplen una condición. Ejemplo: solo los clientes mayores de edad.
- **Proyección**: muestra solo ciertas **columnas**. Ejemplo: solo nombres y correos de una lista de usuarios.
- **Unión**: combina resultados compatibles y elimina duplicados. Las columnas deben ser equivalentes en número y tipo.
- **Diferencia**: muestra lo que existe en el primer grupo pero falta en el segundo.
- **Producto cartesiano**: combina cada fila de una tabla con todas las de otra. Consume muchos recursos, por eso suele usarse con condiciones.

Estas operaciones son la base para construir consultas SQL, que pueden guardarse como **vistas**.

| Lenguaje | Propósito |
|---|---|
| **DDL** | Crear y modificar tablas, vistas, índices y restricciones |
| **DCL** | Conceder y revocar permisos sobre usuarios, roles y objetos |
