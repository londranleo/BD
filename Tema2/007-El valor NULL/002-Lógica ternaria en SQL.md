## 7.2. Lógica ternaria en SQL

Es un sistema lógico que utiliza tres valores posibles en lugar en lugar de los dos habituales de la lógica booleana.

Los tres valores lógicos de SQL `TRUE` la condición se cumple, `FALSE` la condición no se cumple y `UNKNOWN` No se puede desterminar si la condición es verdadera o falsa porque interviene un `NULL`.

Por ejemplo, si tenemos una tabla con una columna `edad` cuyo valor es `NULL`, no podemos saber si la edad es mayor que 18.

```SQL
SELECT *
FROM empleados
WHERE edad > 18;
```

Si la edad es `NULL`, la expresión `edad > 18` devuelve `UNKNOWN`. Por eso, esa fila no se selecciona con `WHERE`.

