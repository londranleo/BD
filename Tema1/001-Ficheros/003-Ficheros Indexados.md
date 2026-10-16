# 1.2 Ficheros Indexados

Es un archivo, acompanado de una estructura(Indice) que acelera la localización de los datos.

El indice no sustituye a los datos, es una guía para encontrarlos antes (Como el indice de un libro).

```txt
guardar el id de cada cliente y la posicion de su registro
```

Búsqueda mas rápida tiene un coste, ocupa espacio adicional y debe actualizarse en cada vez que insertan modifican o eliminan. Si se queda desactualizado la busqueda puede devolver una posicion incooretas o perder registros 