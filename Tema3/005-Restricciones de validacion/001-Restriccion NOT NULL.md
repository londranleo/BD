## 5.1. Restricción NOT NULL

Cuando guardamos Información en tablas, a veces dejamos espacios en blanco, Esa casilla vacía representa un valor `NULL`. 

La restricción `NOT NULL` obliga a que una columna siempre tenga un valor. no admite `NULL`.

Un ejemplo es el correo electrónico al registrase en una pagina. 

Al crear la tabla con DDL se decide qué campos son obligatorios. Ejemplo: nombre y correo sean obligatorios, teléfono secundario opcional.

```sql
CREATE TABLE alumnos ( 
id INT PRIMARY KEY, 
nombre VARCHAR(50) NOT NULL,
email VARCHAR(100) 
);
```
