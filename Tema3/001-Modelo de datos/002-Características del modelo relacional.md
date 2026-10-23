# 1.2. Características del modelo relacional: tablas, tuplas y atributos

El modelo relacional organiza los datos mediante tablas formada por filas y columnas. 

Cada tabla tiene dos partes:
- **Atributos (columnas)** Indican que datos van a guardar.
- **Tuplas (Filas)** Contiene los datos reales de una persona, objeto o registro.

Cada fila usa un **clave primaria** para diferenciarse del resto. 

Otro elementos:
- Para relacionar tablas distintas se usan  **claves foráneas**.
- Para mostrar un resumen o proteger datos sensibles se crean **vistas**.

Esta estructura se define y administra mediante instrucciones SQL. Se usa el lenguaje **DDL**  define la estructura y el **DCL** gestiona los permisos.

Así se separan la estructura, las consultas y la seguridad.

| Elemento común | Nombre técnico | Qué representa |
|---|---|---|
| Columna | Atributo | El tipo de información |
| Fila | Tupla | Registro almacenado |
| Identificador | Clave primaria | Código único del registro |
