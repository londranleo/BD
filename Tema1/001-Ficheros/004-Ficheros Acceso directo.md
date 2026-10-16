# 1.3 Fichero de acceso directo 

Archivo organizado de modo que se pueda saltar directo a la posición sin recorrer el archivo.

Su eficacia depende del **método de direccionamiento** y de cómo se gestionen **colisiones** o posiciones ocupadas.

Los ficheros indexados utilizan una tabla auxiliar para localizar los datos sin recorrer todo el archivo. Permiten acceder a los datos cuando se conoce la clave o posición. 

## Criterios de elección 

Elegir el archivo adecuado depende del tamaño de proyecto, rapidez de acceso y también seguridad, integridad, acceso simultaneo y necesidad de relacionar los datos. 