Las restricción `CHECK` obliga a que los valores de una columna cumpla una **condición**. Si la condición no se compre, el SGBD rechaza la fila.

Ejemplos: 
- Evita precios negativos.
- validar rangos o estados permitidos.
- Relacionar columnas de la misma fila.

```SQL
CREATE TABLE alumnos (
    id INT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    edad INT CHECK (edad >= 18),
    nota DECIMAL(4,2),
    CONSTRAINT ck_nota CHECK (nota BETWEEN 0 AND 10)
);
```

Los SGBD pueden aplicarla de forma distinta según la versión, el motor o la configuración. Por eso no basta con escribirla: hay que comprobar que funciona.
