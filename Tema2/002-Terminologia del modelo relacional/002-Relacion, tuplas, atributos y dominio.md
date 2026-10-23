# 2.1 Relacion, tuplas, atributos y dominio
La relación (tabla) está formada por filas y columnas:

- Cada línea horizontal es una **tupla** (fila): un registro único.
- Cada línea vertical es un **atributo** (columna): una característica.
- Cada columna acepta un único formato o conjunto de valores: el **dominio** (tipo de dato). Indica si el dato es texto, número o fecha, y también qué valores son válidos.

Las claves sirven para identificar y relacionar:
- La **clave primaria** es un identificador irrepetible para cada fila.
- Las **claves foráneas** referencian claves primarias o únicas de otras tablas. Así se enlaza la información sin duplicarla.

Las vistas son tablas virtuales que permiten consultar sin modificar las tablas base. Se crean con **DDL** y sus permisos se gestionan con **DCL**.