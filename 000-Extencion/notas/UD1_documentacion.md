# UD1 · Almacenamiento de la información

> **De qué va este tema:** cómo se guardaba la información antes (en **ficheros**), por qué aparecieron las **bases de datos**, qué programa las gestiona (el **SGBD**), cómo se reparten entre servidores, qué dice la **ley** sobre los datos personales y qué es el **Big Data**.

## Índice
1. Ficheros
2. Bases de datos
3. El SGBD (sistema gestor de bases de datos)
4. Bases de datos centralizadas y distribuidas
5. Protección de datos (la ley)
6. Big Data e inteligencia de negocios

---

## 1. Ficheros

Antes de las bases de datos, la información se guardaba directamente en **archivos**. Hoy se siguen usando para cosas sencillas, como configuraciones o registros de errores.

Hay tres tipos, y se diferencian en **cómo buscan** un dato:

### 1.1 Fichero plano
**Es un archivo de texto donde los datos van uno detrás de otro, línea a línea.**

- Ejemplos: `.txt`, `.csv`, `.log`, `.env`. También `.json` y `.xml`, aunque estos ya tienen su propia estructura.
- **Para qué se usa:** `.env` para configuración, `.log` para registrar errores y `.csv` para exportar una lista de usuarios.
- **Lo bueno:** es muy sencillo y se puede llevar a otro sistema sin necesitar ningún programa especial.
- **Lo malo:** para encontrar un dato hay que **leerlo desde el principio**. Con pocos datos no importa, pero con muchos es lento.

> 💡 Es como buscar una canción en una cinta de casete: tienes que pasar por todas las anteriores.

### 1.2 Fichero indexado
**Es un fichero con un índice que dice dónde está cada dato.**

- El índice no sustituye a los datos, solo es una **guía** para encontrarlos, igual que el índice de un libro.
- Ejemplo: el índice guarda el número de cada cliente y en qué posición está su ficha.
- **Lo bueno:** las búsquedas son mucho más rápidas.
- **Lo malo:** el índice **ocupa espacio** y hay que **actualizarlo** cada vez que se añade, cambia o borra un dato. Si se queda desactualizado, puede llevar a sitios equivocados o "perder" registros.

### 1.3 Fichero de acceso directo
**El sistema calcula la posición exacta del dato a partir de una clave y salta directamente a ella.**

- Usa una operación matemática sobre un identificador (por ejemplo, el código de cliente).
- No hace falta leer nada de lo anterior.
- Funciona bien si el método de cálculo es bueno y se resuelven las **colisiones**, es decir, cuando dos datos "caen" en la misma posición.

> 💡 Es como ir directamente a la taquilla número 25 porque sabes que tu llave es la 25.

### Cómo elegir
| Tipo | Ideal para | Velocidad |
|---|---|---|
| Plano | Pocos datos | Lenta |
| Indexado | Búsquedas frecuentes | Rápida |
| Acceso directo | Ir a un dato concreto al instante | Muy alta |

Además de la cantidad de datos y la rapidez, también hay que pensar en la seguridad, la integridad, si accederán varios usuarios a la vez y si hay que relacionar datos entre sí.

⚠️ Si un fichero guarda **datos personales**, también hay que protegerlo: controlar quién accede, recoger solo lo necesario y no guardarlo más tiempo del debido.

---

## 2. Bases de datos

### 2.1 Qué es una base de datos
**Es un conjunto de datos relacionados, guardados de forma organizada para poder consultarlos, modificarlos y protegerlos fácilmente.**

Nacieron para resolver los problemas de los ficheros: los datos repetidos, las búsquedas lentas y la falta de control.

No hay que confundir dos conceptos:
- La **base de datos** son los **datos** organizados.
- El **SGBD** es el **programa** que crea, consulta, modifica, protege y administra esa base de datos.

**¿Qué mejora respecto a los ficheros?**

| | Ficheros | Base de datos |
|---|---|---|
| Buscar | Lento y manual | Con consultas e índices |
| Datos repetidos | Muchos | Se reducen y controlan |
| Seguridad | Solo los permisos del sistema | Permisos, roles y auditoría |
| Datos correctos (integridad) | Lo controla la aplicación | Lo controla el SGBD con reglas |
| Varios usuarios a la vez | Da problemas | Está controlado |

💡 Si una base de datos guarda datos personales, tiene que aplicar medidas de seguridad para cumplir el **RGPD**.

### 2.2 Tipos según cómo organizan los datos (modelo)
- **Relacional:** usa **tablas** con filas y columnas. Es ideal cuando los datos tienen una estructura clara y muchas relaciones, como clientes, pedidos y facturas.
- **Documental:** guarda **documentos** que pueden tener estructuras distintas. Sirve para perfiles de usuario o catálogos de productos.
- **Clave-valor:** guarda una **etiqueta y su dato**. Es muy rápida, y se usa para sesiones, caché o ajustes.
- **Grafos:** guarda **puntos conectados entre sí**. Se usa cuando lo importante son las relaciones, como amistades, rutas o recomendaciones.
- **Columnas anchas:** se usa para cantidades enormes de datos repartidos, como registros de actividad o datos de sensores.
- **Orientadas a objetos:** guardan bloques de datos complejos, parecidos a los objetos de la programación.

No hay un modelo mejor que otro: depende de **cómo es la información** y de **cómo la vas a usar**.

### 2.3 Tipos según dónde están (ubicación)
- **Local:** en tu propio equipo.
- **Remota:** en otro servidor, al que accedes por red.
- **En la nube:** en servidores de un proveedor externo, por internet.
- **Híbrida:** una mezcla de equipo propio y nube.

Dónde esté la base de datos influye en el rendimiento, la disponibilidad, la administración y la seguridad. Por eso, al elegir conviene preguntarse **quién administra** los servidores, **dónde están** los datos, **qué permisos** hay y **qué garantías** de seguridad ofrece.

⚠️ La ley de protección de datos se aplica **esté donde esté** la base de datos.

🧠 Las grandes empresas reparten sus bases de datos por todo el mundo, así su web sigue funcionando aunque se estropee un equipo.

---

## 3. El SGBD (sistema gestor de bases de datos)

**El SGBD es el programa que crea, mantiene y controla el acceso a una base de datos.**

Ejemplos: MySQL, PostgreSQL, Oracle, MongoDB…

### 3.1 Qué hace (funciones)
1. **Almacenamiento:** guarda los datos de forma organizada y permanente.
2. **Recuperación:** devuelve los datos cuando se piden, y también permite restaurarlos tras un fallo o volver a una copia anterior.
3. **Concurrencia:** permite que **muchas personas trabajen a la vez** sin pisarse, gestionando accesos, bloqueos y transacciones.
4. **Integridad:** evita guardar datos imposibles, incorrectos o que rompan las relaciones entre tablas.
5. **Seguridad:** solo entra quien tiene permiso. Además hace copias de seguridad y registra quién hace qué.

También ejecuta **transacciones**: un grupo de operaciones que se hacen **todas** o **ninguna**.

💡 El SGBD ayuda a cumplir el RGPD y la LOPDGDD, pero no basta con instalarlo: también depende de cómo se configure y de cómo use los datos la empresa.

### 3.2 De qué está hecho (componentes)
- **Motor:** es el corazón. Ejecuta las consultas, gestiona las transacciones y guarda y busca los datos en el disco.
- **Diccionario de datos:** es el catálogo interno, que apunta qué tablas hay, qué campos tienen, de qué tipo son, cómo se relacionan, qué reglas tienen y quién tiene permisos. Son los **datos sobre los datos** (metadatos).
- **Interfaz de usuario:** es lo que permite usarlo, ya sea una herramienta gráfica (con botones y pantallas) o una consola SQL.

Muchos SGBD traen además un optimizador de consultas, un gestor de transacciones, copias de seguridad, recuperación ante fallos, conectores para lenguajes de programación y herramientas de administración.

### 3.3 Tipos de SGBD
- **Relacionales:** usan tablas conectadas y el lenguaje **SQL**. Se usan en tiendas online, facturación o bancos.
  - Ejemplos: MySQL, MariaDB, PostgreSQL, Oracle Database, SQL Server.
- **NoSQL:** son muy flexibles, para datos sin un formato fijo. Incluyen los modelos documental, clave-valor, de columnas anchas y de grafos, y se usan por ejemplo en redes sociales.
  - Ejemplos: MongoDB, Redis, Cassandra, Neo4j.
- **NewSQL:** combinan las tablas de lo relacional con la capacidad de repartirse entre muchos servidores. Sirven para sistemas globales con muchísimos usuarios a la vez.
  - Ejemplos: CockroachDB, TiDB, Google Spanner.
- **Orientados a objetos:** guardan datos y comportamiento juntos, como en la programación orientada a objetos. Se usan en ingeniería.

**Para elegir** hay que mirar el tipo de información, cuántos datos habrá, si hay muchas relaciones, cuántos usuarios simultáneos habrá, qué seguridad hace falta y qué rendimiento se espera.

---

## 4. Bases de datos centralizadas y distribuidas

### 4.1 Centralizada
**Toda la información está en un único servidor.**

- **Lo bueno:** se instala rápido, es fácil de mantener y todo se controla desde un solo sitio.
- **Lo malo:** si ese servidor falla, **se cae todo**, salvo que haya copias de seguridad o un servidor de respaldo.
- **Para qué se usa:** aplicaciones pequeñas o medianas, gestión interna de una empresa, prototipos o sistemas con pocos usuarios a la vez.
- **Para reducir riesgos:** copias de seguridad, vigilar el servidor (monitorización), probar que las copias se pueden restaurar y tener un servidor de respaldo.

### 4.2 Distribuida
**La información se reparte entre varios ordenadores (llamados nodos) conectados en red.**

- **Lo bueno:** puede **seguir funcionando aunque falle un nodo** y puede crecer añadiendo más nodos.
- **Lo malo:** es más compleja de mantener.

| | Centralizada | Distribuida |
|---|---|---|
| Dónde está | Un servidor | Varios nodos |
| Mantenimiento | Sencillo | Más complejo |
| Si algo falla | Se cae todo | Puede seguir funcionando |
| Crecer | Limitado | Fácil, añadiendo nodos |

#### ACID y el teorema CAP
Para que una base de datos repartida funcione bien, hay dos ideas clave:

**ACID** son las 4 propiedades que hacen fiables las transacciones:
- **A**tomicidad: se hace todo o nada.
- **C**onsistencia: los datos siempre cumplen las reglas.
- A**i**slamiento: las operaciones simultáneas no se estorban.
- **D**urabilidad: lo guardado no se pierde.

El **teorema CAP** dice que, si se corta la red entre servidores, **no se pueden tener las tres cosas a la vez** y hay que elegir cuál priorizar:
- **Consistencia:** todos ven el mismo dato.
- **Disponibilidad:** el sistema siempre responde.
- **Tolerancia a particiones:** sigue funcionando aunque falle la red entre nodos.

> 💡 Un **banco** prioriza la consistencia (tu saldo tiene que ser exacto). Una **red social** prioriza la disponibilidad, y no pasa nada si un "me gusta" tarda unos segundos en verse.

### 4.3 Cómo se reparten los datos

**Fragmentación** significa dividir una tabla en trozos para guardarlos en distintos nodos. Hay tres formas:
- **Horizontal (por filas):** por ejemplo, los clientes europeos en un servidor y los americanos en otro.
- **Vertical (por columnas):** por ejemplo, el nombre y el DNI en un nodo, y el teléfono y las preferencias en otro.
- **Mixta:** combina las dos, por ejemplo cuentas repartidas por regiones.

Se hace para repartir el trabajo, ir más rápido, administrar mejor los datos y tenerlos cerca de quien los usa.

**Replicación** significa guardar **copias** de los datos (completas o parciales) en varios nodos. Si uno falla, otro responde. Eso sí, las copias tienen que **sincronizarse** para que no queden datos distintos.

**Transparencia de distribución** significa que el usuario **no nota nada**: ve los datos como si estuvieran en un solo sitio, aunque por dentro estén repartidos.

⚠️ Si los datos personales están repartidos, incluso en servidores de otros países, hay que seguir cumpliendo la ley: dónde están, quién accede, cómo se protegen y cómo se copian.

---

## 5. Protección de datos (la ley)

Un **dato personal** es cualquier información que permite identificar a una persona, como su nombre, su DNI o su correo.

Hay dos normas que tienes que conocer:
- **RGPD:** el Reglamento (UE) **2016/679**, la norma **europea**.
- **LOPDGDD:** la Ley Orgánica **3/2018**, la norma **española**, que adapta y completa el RGPD.

Se aplican a **todo**: bases de datos relacionales y NoSQL, ficheros, copias de seguridad, registros y servicios en la nube. Incumplirlas supone **multas muy altas** y perder la confianza de los usuarios.

### 5.1 RGPD (Europa)
**Solo puedes tratar datos personales si tienes un motivo legal**, por ejemplo:
- El **consentimiento** de la persona.
- La ejecución de un **contrato**, como tramitar una compra o hacer una factura.
- Una **obligación legal**.
- Un **interés legítimo** bien justificado.

Cuando el motivo es el consentimiento, tiene que ser **libre, informado, específico e inequívoco**. Y **nunca** se guardan datos sin avisar antes a la persona.

**Derechos de las personas:**
- **Acceso:** ver qué datos suyos tienes.
- **Rectificación:** corregirlos si están mal.
- **Supresión ("derecho al olvido"):** que se borren cuando ya no haya motivo para guardarlos.
- **Limitación:** restringir el uso que se hace de ellos.
- **Oposición:** negarse a ciertos usos.
- **Portabilidad:** recibir sus datos en un formato ordenado para llevárselos a otro sitio.

### 5.2 LOPDGDD (España)
- Pide **solo los datos necesarios**. Por ejemplo, una tienda no tiene por qué preguntarte por tu salud.
- Protege el sistema con permisos por **roles**, contraseñas seguras, copias de seguridad, **cifrado** cuando haga falta y registro de accesos y cambios.
- El sistema tiene que permitir que la gente **ejerza sus derechos**: consultar, corregir o borrar sus datos.

### 5.3 Cómo se aplica al diseñar una base de datos
Desde el principio hay que seguir estos principios:
- **Minimización:** recoger solo lo necesario.
- **Limitación de finalidad:** usar los datos solo para lo que se dijo.
- Control de quién accede.
- Guardar los datos solo el tiempo necesario.
- **Privacidad desde el diseño y por defecto.**

**Técnicas para proteger los datos:**
- **Cifrado:** convierte la información en un código que solo puede leer quien tiene la clave. Se usa en copias de seguridad, comunicaciones y datos sensibles.
- **Hash con sal:** es la forma correcta de guardar **contraseñas**. ⚠️ Las contraseñas **no** se cifran de forma reversible.
- **Anonimización:** borra o transforma los datos que identifican a alguien, de modo que **ya es imposible** saber de quién son. Es útil para estadísticas.
  - Si combinando otros datos todavía se pudiera saber quién es, se llama **seudonimización**, no anonimización.
- **Auditoría:** es como un **libro de visitas automático** que apunta quién entró, qué hizo y cuándo. Estos registros también hay que protegerlos, porque contienen información sensible.

💡 La seguridad no depende solo del programa: depende también de cómo diseñas la base de datos, de cómo das los permisos y de cuánto tiempo guardas la información.

---

## 6. Big Data e inteligencia de negocios

### 6.1 Qué es el Big Data
**Big Data son las técnicas y tecnologías para guardar y analizar cantidades enormes de datos, muy variados y que llegan muy rápido.** Aparece cuando las herramientas normales ya no pueden con ellos.

Se define con las **5 V**:
1. **Volumen:** muchísimos datos.
2. **Velocidad:** se generan y se procesan muy rápido.
3. **Variedad:** vienen en muchos formatos, como texto, imágenes, audio o vídeo.
4. **Veracidad:** los datos tienen que ser fiables. Si están mal, incompletos o duplicados, las conclusiones también estarán mal.
5. **Valor:** sirven para tomar buenas decisiones. No todos los datos aportan algo: hay que analizarlos con un objetivo.

⚠️ Cruzar muchos datos puede revelar información personal aunque cada dato por separado parezca inofensivo. Por eso aquí la privacidad es todavía más importante.

### 6.2 Herramientas: Hadoop y Spark
Ambas usan **procesamiento distribuido**: dividen los datos y el trabajo en trozos y los reparten entre muchos ordenadores que trabajan **a la vez**. Así es más rápido y no se sobrecarga un solo servidor.

- **Hadoop:** plataforma **libre** para **guardar y procesar** grandes volúmenes de datos repartidos en muchos nodos.
- **Spark:** motor **muy rápido** porque trabaja **en memoria**. Es ideal para análisis interactivo, aprendizaje automático y datos en **tiempo casi real**.

⚠️ Con tantos datos viajando por la red, hay que controlar quién accede, cifrar las comunicaciones, registrar las operaciones y no guardar datos personales más tiempo del necesario.

### 6.3 Inteligencia de negocios (BI)
**BI son las herramientas que analizan los datos de una empresa para ayudarla a tomar mejores decisiones.**

- Los resultados se muestran en **dashboards**: paneles con gráficos, indicadores clave (KPI), filtros, informes automáticos, comparativas con el pasado y alertas.
- **Ejemplo en una tienda online:** qué productos se venden más, qué clientes compran más a menudo, en qué meses hay más pedidos o qué campañas funcionan mejor.
- El BI **no sustituye** a la base de datos, **se apoya en ella**. El proceso es: recoger los datos, limpiarlos y organizarlos, analizarlos y mostrarlos en el dashboard.

---

## 📖 Glosario
- **ACID:** atomicidad, consistencia, aislamiento y durabilidad; las propiedades que hacen fiables las transacciones.
- **Anonimización:** transformar los datos para que no se pueda saber de quién son.
- **Auditoría informática:** registro de quién hizo qué y cuándo.
- **Base de datos centralizada:** toda la información en un solo servidor.
- **Base de datos distribuida:** la información repartida en varios nodos.
- **BI:** análisis de datos para tomar decisiones, normalmente con dashboards.
- **Cifrado:** hacer la información ilegible para quien no tiene la clave.
- **Concurrencia:** varias personas usando los datos a la vez sin conflictos.
- **Derecho al olvido:** pedir que se borren tus datos.
- **Diccionario de datos:** catálogo interno del SGBD (tablas, columnas, relaciones, reglas).
- **Fragmentación:** dividir una tabla en trozos para repartirla entre nodos.
- **Hadoop / Spark:** herramientas de procesamiento distribuido para Big Data.
- **Integridad:** que los datos cumplan las reglas y sean coherentes.
- **LOPDGDD:** Ley Orgánica 3/2018, la ley española de protección de datos.
- **Motor de base de datos:** la parte del SGBD que ejecuta las consultas y accede a los datos.
- **NoSQL / NewSQL:** bases de datos flexibles / relacionales que se reparten en muchos servidores.
- **Replicación:** copias sincronizadas de los datos en varios nodos.
- **RGPD:** Reglamento (UE) 2016/679, la norma europea de protección de datos.
- **SGBD:** el programa que gestiona la base de datos.
- **Teorema CAP:** en un sistema repartido no se pueden tener a la vez consistencia, disponibilidad y tolerancia a fallos de red.
- **Transparencia de distribución:** el usuario no nota que los datos están repartidos.

---

## ✅ Resumen para repasar
- **Ficheros:** el plano lee desde el principio (lento), el indexado usa un índice (rápido, pero hay que mantenerlo) y el de acceso directo calcula la posición (inmediato).
- **Base de datos** son los datos organizados. **SGBD** es el programa que los gestiona, formado por motor, diccionario de datos e interfaz.
- **Funciones del SGBD:** almacenar, recuperar, concurrencia, integridad y seguridad.
- **Modelos:** relacional, documental, clave-valor, grafos, columnas anchas y orientado a objetos.
- **Ubicación:** local, remota, en la nube o híbrida.
- **Centralizada** es simple pero, si falla, se cae todo. **Distribuida** es más compleja pero aguanta fallos y crece.
- **Fragmentación:** horizontal por filas, vertical por columnas, mixta las dos. **Replicación** son copias.
- **CAP:** si falla la red, hay que elegir entre consistencia y disponibilidad (el banco prefiere consistencia, la red social disponibilidad).
- **Leyes:** RGPD (UE 2016/679) y LOPDGDD (LO 3/2018). Recoger solo lo necesario, cifrar, anonimizar, auditar y guardar contraseñas con hash con sal.
- **Big Data:** 5 V (volumen, velocidad, variedad, veracidad, valor). Herramientas: Hadoop y Spark. **BI** son decisiones basadas en dashboards.
