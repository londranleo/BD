## 6.3. Ventajas e inconvenientes: lectura, escritura y mantenimiento

- **Ventaja**: rendimiento en lectura. Se encuentra la información sin leer toda la tabla.
- **Inconveniente**: coste en escritura. Cada vez que se añade o modifica un dato, también hay que actualizar el índice, lo que exige más mantenimiento.

Los índices no sustituyen a un buen diseño de tablas, claves, consultas y relaciones.

>los índices aceleran las búsquedas, pero ralentizan el guardado de datos.

| Aspecto | Ventaja | Inconveniente |
|---|---|---|
| Lectura | Menos lecturas en consultas compatibles | Ocupan espacio extra |
| Escritura | Información organizada | Más tiempo de guardado |
