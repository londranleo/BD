## 6.1. Concepto de índice: estructura, tipos y funcionamiento

Es una estructura que permite localizar los datos de forma mas rápida sin tener que recorrer todo el registro de una tabla. 

- **B-Tree**: Es un tipo de indice que organiza los datos en forma de arbol, manteniéndolos ordenados para facilitar la búsquedas.
- **Hash**: asigna una ubicación directa al dato. Sirve para coincidencias exactas.
- **Full-Text**: Es un diseño para buscar palabras o expresiones dentro de textos largos.

| Tipo | Funcionamiento | Uso recomendado |
|---|---|---|
| **B-Tree** | Organiza los datos en ramas | Buscar rangos de valores |
| **Hash** | Asigna una ubicación directa | Buscar coincidencias exactas |
| **Full-Text** | Cataloga palabras de un texto | Buscar términos en textos largos |

