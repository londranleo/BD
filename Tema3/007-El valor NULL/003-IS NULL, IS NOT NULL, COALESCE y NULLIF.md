## 7.3. IS NULL, IS NOT NULL, COALESCE y NULLIF

Son herramientas para trabajos con valores `NULL`. cada una tiene una función diferente.

`IS NULL` comprueba si un valor es `NULL`. 

```SQL
SELECT *
FROM empleados
WHERE salario IS NULL;
```

Devuelve los empleados cuyo salario no tiene valor registrado. 
