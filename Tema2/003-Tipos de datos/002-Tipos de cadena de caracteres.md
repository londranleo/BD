# 3.2. Tipos de cadena de caracteres: CHAR, VARCHAR y TEXT

Al estructura la information, cada columna de tablas necesita tener asignacion de tipo especifico.

- **CHAR**: tamaño exacto o fijo. Siempre usa los mismos caracteres y rellena el espacio que no usa. Ejemplos: códigos postales, códigos de país, abreviaturas.
- **VARCHAR**: tamaño variable. Solo usa el espacio necesario. Ejemplos: nombres, correos, títulos, direcciones breves.
- **TEXT**: contenidos muy extensos, como artículos enteros. No es la mejor opción para búsquedas frecuentes, salvo que se use un índice de texto completo.

El acceso a estos datos se puede limitar con vistas y permisos (DCL).

> Elige CHAR para tamaños fijos, VARCHAR para textos cortos y TEXT para textos muy largos.