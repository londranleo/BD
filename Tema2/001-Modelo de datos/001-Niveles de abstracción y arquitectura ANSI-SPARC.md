# 1.1. Concepto de modelo de datos: niveles de abstracción y arquitectura ANSI/SPARC

Un modelo de datos es una representación organizada de la información. Permite interpretar y mantener la estructura de la base de datos con claridad. Para ello se emplea la arquitectura ANSI/SPARC. 

Este sistema divide los datos en tres niveles distintos. Así, cada usuario trabaja con una vista adaptada, sin depender de cómo se almacenan los datos.

| Nivel | Qué representa |
|---|---|
| **Interno** | Cómo se guardan los datos físicamente (disco) |
| **Conceptual** | Qué información existe y cómo se estructura |
| **Externo** | Lo que ve cada usuario/aplicación (aquí están las **vistas**) |

El modelo relacional organiza la información en tablas formadas por filas y columnas. Para identificar cada fila de forma unívoca se usa la **clave primaria**, y para conectar tablas, la **clave foránea**.

| Clave | Función | Ejemplo |
|---|---|---|
| Primaria | Identifica un registro único en su tabla | Identificador de cliente generado por el sistema |
| Foránea | Conecta una tabla con otra | Código de departamento en la ficha de un empleado |
- **DDL**: crea y modifica tablas, vistas, índices y restricciones.
- **DCL**: concede y revoca permisos sobre usuarios, roles y objetos.