## 4.3. Claves primarias artificiales (surrogate keys): autoincremento y UUID

Usar datos reales como identificador puede causar errores si esos datos cambian. Por eso se usa una **clave artificial** en dos casos:
- cuando no hay una clave natural estable,
- o cuando se quiere evitar que los cambios en los datos afecten a las relaciones.

La genera la base de datos o la aplicación y se mantiene estable aunque cambien los datos reales.

- **Autoincremento**: números enteros seguidos (1, 2, 3). Es sencillo y útil en bases centralizadas.
- **UUID**: cadena larga y aleatoria de letras y números. Reduce mucho las colisiones y es útil en sistemas distribuidos.

| Tipo | Funcionamiento | Ejemplo |
|---|---|---|
| **Autoincremento** | Cuenta secuencial | 1, 2, 3, 4 |
| **UUID** | Cadena aleatoria única | 550e8400-e29b-41d4-a716-446655440000 |

Al no tener significado de negocio, la clave artificial se puede cambiar de tipo con menor impacto sobre las claves foráneas que una clave natural.

>  **Recuerda:** las claves artificiales mantienen estable la identificación aunque cambien los datos reales.