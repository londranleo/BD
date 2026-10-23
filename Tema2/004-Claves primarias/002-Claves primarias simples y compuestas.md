# 4.2. Claves primarias simples y compuestas

- **Clave simple**: usa un solo dato. Ejemplo: número de socio.
- **Clave compuesta**: combina varios valores cuando uno solo no basta. Ejemplos: fila + butaca de un cine; identificador de alumno + identificador de asignatura en una tabla de matrículas.

La elección forma parte del diseño y se define con **DDL** al crear la tabla. La clave compuesta complica las referencias de las claves foráneas, así que conviene reservarla para casos en que  ningún atributo aislado identifica el registro de forma univoca.