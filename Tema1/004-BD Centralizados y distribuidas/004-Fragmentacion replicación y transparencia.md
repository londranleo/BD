# 4.3 Fragmentación, replicación y transparencia

Fragmentación significa dividir una tabla en trozos para guárdalos en distintos nodo. Hay tres formas: 

- Horizontal (por filas): por ejemplo, los cliente europeos en un servidor y los americanos en otro.
- Vertical (por columnas): por ejemplo, el nombre y el DNI en un nodo, y el teléfono y las preferencias en otro.
- Mixta: combina las dos, por ejemplo cuentas repartidas por regiones.

Se hace repartir el trabajo, ir mas rápido, administrar mejor los datos y tenerlos cerca de quien los usa.

**Replicación** significa guardar **copias** de los datos (completas o parciales) en varios nodos. Si uno falla, otro responde. Eso sí, las copias tienen que **sincronizarse** para que no queden datos distintos.

**Transparencia de distribución** significa que el usuario **no nota nada**: ve los datos como si estuvieran en un solo sitio, aunque por dentro estén repartidos.

>Si los datos personales están repartidos, incluso en servidores de otros países, hay que seguir cumpliendo la ley: dónde están, quién accede, cómo se protegen y cómo se copian.