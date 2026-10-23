## 5.2. Restricción UNIQUE

La restricción `UNIQUE` evita que otros datos se repitan. 

como el correo de una persona. Si se intenta guardar un correo duplicado, el sistema bloquea la entrada.

En muchos SGBD, UNIQUE permite varios NULL, porque NULL no se compara como un dato normal. Depende del SGBD.

```sql
CREATE TABLE alumnos (
    id INT PRIMARY KEY,
    dni VARCHAR(9) UNIQUE,
    email VARCHAR(100),
    CONSTRAINT uq_email UNIQUE (email)
);
```
