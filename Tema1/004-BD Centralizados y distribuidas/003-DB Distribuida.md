# 4.2 BD Distribuida: CAP y ACID

La información se reparte entre varios ordenadores (llamados nodos) conectados en red. Usa fragmentación y/o replicació. 

Puede seguir funcionando aunque falle un nodo y puede crecer añadiendo mas nodos, lo malo es mas copleja de matener.

### ACID y el teorema CAP 

para que una BD distribuida funcione bien, hay dos idead clave:

ACID son las 4 propiedad que hacen fiable las transacciones

 - Atomicidad: se hace todo o nada.
 - Consistencia: los datos siempre cumplen las reglas.
 - Aislamiento: las operaciones simultáneos no se estorban.
 - Durabilidad: lo guardado no se pierde.
 
 El teorema CAP deice que, si se cirta la ente servidodes no se pueden tenr las tres cosas a la vez y hay que elegir cual priorizar. 

- Consistencia: todo ven el mismo dato
- Disponibilidad: el sistema siempre responde. 
- Tolerancia a particiones: sigue funcionado aunque falle la red entre nodos. 

 >Un **banco** prioriza la consistencia (tu saldo tiene que ser exacto). Una **red social** prioriza la disponibilidad, y no pasa nada si un "me gusta" tarda unos segundos en verse.







