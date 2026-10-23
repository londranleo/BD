## 4.1. Superclave, clave candidata y clave primaria

- **Superclave**: cualquier combinación de datos que identifica un registro de forma única, aunque tenga más columnas de las necesarias. Ejemplo: nombre completo + teléfono.
- **Clave candidata**: la superclave reducida a lo mínimo necesario. Ejemplo: número de pasaporte. Una tabla puede tener varias.
- **Clave primaria**: la clave candidata elegida. Solo hay una.

Proceso: superclaves → claves candidatas → clave primaria.

Cuando una clave primaria se coloca en otra tabla para conectarlas, se llama **clave foránea**.

> **Recuerda:** la clave primaria siempre es única y nunca puede estar vacía.

| Clave | Qué es | Ejemplo |
|---|---|---|
| **Superclave** | Combinación identificadora | Nombre y teléfono |
| **Clave candidata** | Combinación mínima | El pasaporte |
| **Clave primaria** | Identificador elegido | El documento oficial |
