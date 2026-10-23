# UD2 · Bases de datos relacionales

## Introducción

El modelo relacional organiza la información mediante tablas formadas por filas y columnas. Permite representar entidades, relacionarlas entre sí y aplicar reglas que mantienen la coherencia de los datos.

En esta unidad se estudian:
- Modelo de datos, relación, tupla, atributo, dominio, esquema e instancia.
- Tipos de datos.
- Claves primarias y claves foráneas.
- Restricciones e índices.
- Valores NULL.
- Vistas.
- Los lenguajes DDL y DCL.

## Objetivos

- Analizar el formato de almacenamiento de la información y crear las tablas y sus relaciones.
- Seleccionar los tipos de datos adecuados y definir los campos clave.
- Implementar las restricciones del diseño lógico y crear vistas.
- Interpretar el uso de índices para reducir lecturas innecesarias.
- Crear usuarios y asignarles privilegios.
- Utilizar asistentes y herramientas gráficas.
- Aplicar lenguajes de definición y control de datos.
- Gestionar la seguridad mediante privilegios y roles.

---

# 1. Modelo de datos

Un modelo de datos es una representación organizada de la información. Permite interpretar la estructura de la base de datos, sus reglas y sus relaciones.

El modelo relacional usa la **tabla** como componente básico:
- Las filas se llaman **tuplas** y contienen los registros.
- Las columnas se llaman **atributos** y representan las propiedades de cada registro.

Elementos principales:
- **Clave primaria**: identifica cada fila con un valor único.
- **Clave foránea**: referencia la clave primaria (o una clave única) de otra tabla. Permite relacionar tablas sin duplicar datos.
- **DDL**: crea y modifica la estructura de la base de datos.
- **DCL**: concede y revoca permisos.
- **Álgebra relacional**: operaciones (selección, proyección, unión, diferencia y producto cartesiano) para obtener resultados nuevos a partir de los datos.
- **Vista**: tabla virtual creada a partir de una consulta. Evita reescribir consultas, oculta columnas confidenciales y no altera las tablas originales.

## 1.1. Concepto de modelo de datos: niveles de abstracción y arquitectura ANSI/SPARC

Un modelo de datos es una representación organizada de la información. Permite interpretar y mantener la estructura de la base de datos con claridad. Para ello se emplea la arquitectura ANSI/SPARC.

Este sistema divide los datos en tres niveles distintos. Así, cada usuario trabaja con una vista adaptada, sin depender de cómo se almacenan los datos.

| Nivel | Qué representa |
|---|---|
| **Interno** | Cómo se guardan los datos físicamente (disco) |
| **Conceptual** | Qué información existe y cómo se estructura |
| **Externo** | Lo que ve cada usuario o aplicación (aquí están las **vistas**) |

El modelo relacional organiza la información en tablas formadas por filas y columnas. Para identificar cada fila de forma unívoca se usa la **clave primaria**, y para conectar tablas, la **clave foránea**.

| Clave | Función | Ejemplo |
|---|---|---|
| **Primaria** | Identifica un registro único en su tabla | Identificador de cliente generado por el sistema |
| **Foránea** | Conecta una tabla con otra | Código de departamento en la ficha de un empleado |

- **DDL**: crea y modifica tablas, vistas, índices y restricciones.
- **DCL**: concede y revoca permisos sobre usuarios, roles y objetos.

## 1.2. Características del modelo relacional: tablas, tuplas y atributos

Cada tabla tiene dos partes:
- **Atributos** (columnas): indican qué dato se guarda, por ejemplo el nombre de un cliente.
- **Tuplas** (filas): contienen los datos reales de una persona, objeto o registro.

Cada fila usa una **clave primaria** para diferenciarse del resto.

Otros elementos:
- Para relacionar tablas distintas se usan **claves foráneas**.
- Para mostrar un resumen o proteger datos sensibles se crean **vistas**.
- El **DDL** define la estructura y el **DCL** gestiona los permisos. Así se separan la estructura, las consultas y la seguridad.

| Elemento común | Nombre técnico | Qué representa |
|---|---|---|
| Columna | Atributo | El tipo de información |
| Fila | Tupla | Registro almacenado |
| Identificador | Clave primaria | Código único del registro |

> 💡 **Recuerda:** la clave primaria es el valor único que identifica cada fila dentro de una tabla.

## 1.3. Álgebra relacional: selección, proyección, unión, diferencia y producto cartesiano

El álgebra relacional es un sistema de reglas para buscar información. Describe operaciones sobre tablas y es la base teórica de muchas consultas SQL.

- **Selección**: elige **filas** completas que cumplen una condición. Ejemplo: solo los clientes mayores de edad.
- **Proyección**: muestra solo ciertas **columnas**. Ejemplo: solo nombres y correos de una lista de usuarios.
- **Unión**: combina resultados compatibles y elimina duplicados. Las columnas deben ser equivalentes en número y tipo.
- **Diferencia**: muestra lo que existe en el primer grupo pero falta en el segundo.
- **Producto cartesiano**: combina cada fila de una tabla con todas las de otra. Consume muchos recursos, por eso suele usarse con condiciones.

Estas operaciones son la base para construir consultas SQL, que pueden guardarse como **vistas**.

| Lenguaje | Propósito |
|---|---|
| **DDL** | Crear y modificar tablas, vistas, índices y restricciones |
| **DCL** | Conceder y revocar permisos sobre usuarios, roles y objetos |

---

# 2. Terminología del modelo relacional

El modelo relacional organiza los datos en tablas conectadas entre sí. Términos principales:
- **Relación**: una tabla completa.
- **Tupla**: cada fila.
- **Atributo**: cada columna.
- **Dominio**: los valores permitidos en una columna. Ejemplo: "edad" solo admite enteros positivos y "correo", texto con formato válido.
- **Esquema**: define la forma y las reglas de los datos, pero no contiene la información real.
- **Instancia**: los datos almacenados en un instante concreto. Cambia al añadir, modificar o borrar registros.

## 2.1. Relación, tupla, atributo y dominio

La relación (tabla) está formada por filas y columnas:
- Cada línea horizontal es una **tupla** (fila): un registro único.
- Cada línea vertical es un **atributo** (columna): una característica.
- Cada columna acepta un único formato o conjunto de valores: el **dominio** (tipo de dato). Indica si el dato es texto, número o fecha, y también qué valores son válidos.

| Término técnico | Término común | Significado |
|---|---|---|
| **Relación** | Tabla | Conjunto de datos |
| **Tupla** | Fila | Registro único |
| **Atributo** | Columna | Característica |
| **Dominio** | Tipo y valores permitidos | Valores válidos para una columna |

Las claves sirven para identificar y relacionar:
- La **clave primaria** es un identificador irrepetible para cada fila.
- Las **claves foráneas** referencian claves primarias o únicas de otras tablas. Así se enlaza la información sin duplicarla.

> 💡 **Recuerda:** la clave primaria identifica una fila. La clave foránea referencia la clave primaria de otra tabla.

Las vistas son tablas virtuales que permiten consultar sin modificar las tablas base. Se crean con **DDL** y sus permisos se gestionan con **DCL**.

## 2.2. Esquema de relación y esquema de base de datos

El **esquema** define la estructura de la base de datos: nombres de tablas, columnas, tipos de datos, claves, restricciones y relaciones. Es como el plano de una casa.

- **Esquema de relación**: las normas de una tabla concreta.
- **Esquema de base de datos**: el sistema completo y todas las conexiones entre tablas.

Ejemplo de biblioteca: una tabla "Usuarios" con su clave primaria y una tabla "Préstamos" con una clave foránea que indica qué usuario hizo cada préstamo.

Las vistas y los permisos (DCL) forman parte de la administración. El esquema es la estructura lógica sobre la que se apoyan.

## 2.3. Instancia de base de datos: estado y evolución temporal

La estructura de las tablas es fija, pero los datos guardados en un momento concreto forman una **instancia**. Si se añade un usuario, la instancia cambia. Esto es la evolución temporal de los datos.

Ejemplo: una tabla tiene 3 filas el lunes y 5 el viernes. El esquema es el mismo, pero la instancia ha cambiado.

---

# 3. Tipos de datos

Usar tipos de datos adecuados aporta varias ventajas:
- La información es coherente y hay menos errores.
- Se ocupa menos espacio, así que consultas, ordenaciones y filtros son más rápidos.

Al crear la base de datos (con DDL) se indica el formato exacto de cada columna.

Categorías:
- **Numéricos**: enteros y decimales. Hay que pensar en la precisión y el valor máximo.
- **Texto**: CHAR, VARCHAR y TEXT. Hay que pensar en la longitud máxima, la codificación y si se usará en búsquedas o índices.
- **Fecha y hora**, **booleanos** y **binarios**.

Los nombres exactos de los tipos varían según el SGBD.

## 3.1. Tipos de datos numéricos: enteros, decimales y coma flotante; precisión y rango

- **Enteros**: cantidades completas sin fracciones. Ejemplos: edad, unidades en stock, identificador de un registro.
- **Decimales**: valores exactos con fracciones. Ejemplo: el precio de un producto.
- **Coma flotante**: cantidad variable de decimales, adecuada para mediciones científicas. Puede introducir pequeñas aproximaciones, así que **no** es buena opción para importes monetarios.

La capacidad de estos números se configura con dos propiedades: rango y precisión.

> 💡 **Recuerda:** el **rango** marca los límites del número y la **precisión** define cuántos dígitos exactos se pueden guardar.

| Concepto | Función | Ejemplo |
|---|---|---|
| Clave primaria | Identificar cada fila | Número de cliente |
| Número entero | Valores sin fracción | Edad de una persona |
| Número decimal | Fracciones exactas | Precio de un producto |
| Vista | Consultar datos de forma segura | Catálogo de precios |

## 3.2. Tipos de cadena de caracteres: CHAR, VARCHAR y TEXT

- **CHAR**: tamaño exacto o fijo. Siempre usa los mismos caracteres y rellena el espacio que no usa. Ejemplos: códigos postales, códigos de país, abreviaturas.
- **VARCHAR**: tamaño variable. Solo usa el espacio necesario. Ejemplos: nombres, correos, títulos, direcciones breves.
- **TEXT**: contenidos muy extensos, como artículos enteros. No es la mejor opción para búsquedas frecuentes, salvo que se use un índice de texto completo.

El acceso a estos datos se puede limitar con vistas y permisos (DCL).

| Tipo | Tamaño del texto | Ejemplo |
|---|---|---|
| **CHAR** | Fijo y corto | Códigos postales |
| **VARCHAR** | Variable y corto | Nombres |
| **TEXT** | Variable y largo | Comentarios |

> 💡 **Recuerda:** elige CHAR para tamaños fijos, VARCHAR para textos cortos y TEXT para textos muy largos.

## 3.3. Tipos de fecha, hora, booleanos y binarios

- **Booleanos**: solo verdadero o falso y ocupan poca memoria. Ejemplos: "activo", "verificado", "publicado", "acepta_notificaciones".
- **Fecha y hora**: registran el momento exacto de un evento. Permiten ordenar ventas o buscar una factura por fecha. Se puede guardar solo la fecha, solo la hora o fecha y hora juntas. En aplicaciones web hay que tener en cuenta la **zona horaria**.
- **Binarios**: archivos que no son texto, como fotos o documentos. Se usan tipos como **BLOB** o **BYTEA**. En aplicaciones web suele ser mejor guardar el archivo fuera y conservar en la base de datos solo la ruta, la URL o los metadatos.

> 💡 **Recuerda:** usar el tipo adecuado permite ordenar y filtrar sin conversiones. Guardar fechas como texto provoca errores de ordenación, comparación y formato.

---

# 4. Claves primarias

La **clave primaria** identifica cada registro de forma unívoca. La clave elegida debe ser:
- única,
- no nula,
- estable en el tiempo,
- y, si es posible, sencilla.

Tipos de clave primaria:
- **Simple**: una sola columna. Ejemplo: número de documento de identidad.
- **Compuesta**: combina varias columnas.
- **Artificial** (surrogate key): la genera el sistema cuando los datos reales no ofrecen un buen identificador. Puede ser un **autoincremento** o un **UUID**.

Se definen con DDL mediante `PRIMARY KEY`.

## 4.1. Superclave, clave candidata y clave primaria

- **Superclave**: cualquier combinación de datos que identifica un registro de forma única, aunque tenga más columnas de las necesarias. Ejemplo: nombre completo + teléfono.
- **Clave candidata**: la superclave reducida a lo mínimo necesario. Ejemplo: número de pasaporte. Una tabla puede tener varias.
- **Clave primaria**: la clave candidata elegida. Solo hay una.

Proceso: superclaves → claves candidatas → clave primaria.

Cuando una clave primaria se coloca en otra tabla para conectarlas, se llama **clave foránea**.

> 💡 **Recuerda:** la clave primaria siempre es única y nunca puede estar vacía.

| Clave | Qué es | Ejemplo |
|---|---|---|
| **Superclave** | Combinación identificadora | Nombre y teléfono |
| **Clave candidata** | Combinación mínima | El pasaporte |
| **Clave primaria** | Identificador elegido | El documento oficial |

## 4.2. Claves primarias simples y compuestas

- **Clave simple**: usa un solo dato. Ejemplo: número de socio.
- **Clave compuesta**: combina varios valores cuando uno solo no basta. Ejemplos: fila + butaca de un cine; identificador de alumno + identificador de asignatura en una tabla de matrículas.

La elección forma parte del diseño y se define con **DDL** al crear la tabla. La clave compuesta complica las referencias de las claves foráneas, así que conviene reservarla para cuando ningún atributo solo identifica el registro.

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

> 💡 **Recuerda:** las claves artificiales mantienen estable la identificación aunque cambien los datos reales.

---

# 5. Restricciones de validación

Las restricciones limitan los valores que se pueden guardar. Mantienen la integridad y evitan información incoherente.

- **NOT NULL**: obliga a que la columna siempre tenga información.
- **UNIQUE**: impide que un dato se repita (por ejemplo, el mismo correo). Una tabla puede tener varias.
- **CHECK**: comprueba una condición antes de guardar (por ejemplo, edad mayor de 18). Cada SGBD tiene sus propias normas.

## 5.1. Restricción NOT NULL

Una casilla vacía se representa con **NULL**. NULL no es una cadena vacía, ni cero, ni un espacio en blanco. Indica que no se conoce el dato o que no existe.

**NOT NULL** impide guardar el registro si falta ese dato. Ejemplo: el correo al registrarse en una página.

Diferencia con la clave primaria:
- La clave primaria exige el dato **y** además lo identifica de forma única.
- NOT NULL solo obliga a que exista un valor. No garantiza que sea único ni correcto.

Al crear la tabla con DDL se decide qué campos son obligatorios. Ejemplo: nombre y correo obligatorios, teléfono secundario opcional.

> 💡 **Recuerda:** NOT NULL no comprueba si el dato es correcto; solo garantiza que la casilla no se quede vacía.

## 5.2. Restricción UNIQUE

**UNIQUE** evita que otros datos se repitan, como el correo de una persona. Si se intenta guardar un correo duplicado, el sistema bloquea la entrada.

En muchos SGBD, UNIQUE permite varios NULL, porque NULL no se compara como un dato normal. Depende del SGBD.

> 💡 **Recuerda:** UNIQUE impide duplicar valores reales. El tratamiento de varios NULL depende del SGBD.

| Característica | Clave primaria | UNIQUE |
|---|---|---|
| Objetivo | Identificar filas | Evitar repetidos |
| Acepta nulos | Nunca | Depende del SGBD; en muchos casos sí |

## 5.3. Restricción CHECK

**CHECK** obliga a los datos a cumplir una condición antes de guardarse. Se configura con DDL y usa expresiones sencillas (mayor, menor, igual).

Ejemplos:
- Evitar precios negativos.
- Validar rangos o estados permitidos.
- Relacionar columnas de la misma fila.

Los SGBD pueden aplicarla de forma distinta según la versión, el motor o la configuración. Por eso no basta con escribirla: hay que comprobar que funciona.

| SGBD / versión | Comportamiento | Acción recomendada |
|---|---|---|
| Aplica la restricción | Rechaza datos no válidos | Usarla normalmente |
| Tiene limitaciones | Puede no aplicar algunas comprobaciones | Revisar la documentación y probar |

> 💡 **Recuerda:** verifica el comportamiento del SGBD antes de confiar en una restricción CHECK.

---

# 6. Índices. Características

Funcionan como una guía telefónica ordenada por alfabeto: sin orden habría que leer todo desde el principio. Los **índices** aceleran las búsquedas igual que el índice de un libro. Se crean automáticamente en el campo que identifica cada registro.

- Se crean con **DDL**. El acceso a las tablas se controla con **DCL**.
- Tipos: **B-Tree**, **Hash** y **Full-Text**. Pueden tener una o varias columnas.
- Mejoran la lectura, pero cuesta más escribir y mantener los datos.
- Su utilidad depende de varias cosas: el tipo de búsqueda, las columnas, la cantidad de datos y la selectividad.

## 6.1. Concepto de índice: estructura, tipos y funcionamiento

Buscar en una tabla grande puede requerir muchas lecturas. Con un índice, el sistema no lee registro por registro: va al índice y encuentra la ubicación exacta.

- **B-Tree**: el más común. Organiza los datos en ramas, como un árbol invertido. Sirve para igualdad, rangos y ordenación.
- **Hash**: asigna una ubicación directa al dato. Sirve para coincidencias exactas.
- **Full-Text**: crea un catálogo de palabras, como un buscador de internet. Sirve para buscar palabras en textos largos.

| Tipo | Funcionamiento | Uso recomendado |
|---|---|---|
| **B-Tree** | Organiza los datos en ramas | Buscar rangos de valores |
| **Hash** | Asigna una ubicación directa | Buscar coincidencias exactas |
| **Full-Text** | Cataloga palabras de un texto | Buscar términos en textos largos |

> 💡 **Recuerda:** los índices aceleran las búsquedas, pero consumen espacio de almacenamiento.

## 6.2. Índices simples y compuestos: orden de columnas y selectividad

- **Índice simple**: una sola columna.
- **Índice compuesto**: varias columnas a la vez. Ejemplo: buscar por nombre y apellido.

Se crean con DDL.

En un índice compuesto **el orden de las columnas importa**. Suele ir primero la de mayor **selectividad**, si es el filtro principal de las consultas.
- **Selectividad**: capacidad de los datos para descartar opciones y localizar resultados únicos. Ejemplo: un teléfono es mucho más selectivo que un país.
- El orden ideal depende de cómo se escriban las consultas: no es lo mismo buscar por teléfono, por ciudad o por las dos.

> 💡 **Recuerda:** la selectividad mide lo únicos que son los datos. La columna más selectiva suele ir primero si es el filtro principal, pero depende de las consultas más frecuentes.

| Tipo | Columnas | Ejemplo |
|---|---|---|
| **Índice simple** | Una | Buscar solo por correo |
| **Índice compuesto** | Dos o más | Buscar por apellido y ciudad |

## 6.3. Ventajas e inconvenientes: lectura, escritura y mantenimiento

- **Ventaja**: rendimiento en lectura. Se encuentra la información sin leer toda la tabla.
- **Inconveniente**: coste en escritura. Cada vez que se añade o modifica un dato, también hay que actualizar el índice, lo que exige más mantenimiento.

Los índices no sustituyen a un buen diseño de tablas, claves, consultas y relaciones.

> 💡 **Recuerda:** los índices aceleran las búsquedas, pero ralentizan el guardado de datos.

| Aspecto | Ventaja | Inconveniente |
|---|---|---|
| Lectura | Menos lecturas en consultas compatibles | Ocupan espacio extra |
| Escritura | Información organizada | Más tiempo de guardado |

---

# 7. El valor NULL

Si no sabes el teléfono de un amigo, dejas el hueco en blanco en tu libreta. En las bases de datos ese hueco es **NULL**: un dato que falta o que todavía no se conoce.

- Un **texto vacío** sí es un valor (una cadena sin caracteres).
- **NULL** es ausencia de valor.

NULL añade una tercera opción a la lógica (además de verdadero y falso): la **lógica ternaria**. Por eso existen comandos específicos para detectar los NULL y sustituirlos.

## 7.1. Concepto y semántica del valor NULL

El valor desconocido no debe sustituirse por 0, FALSE o una cadena vacía, porque cada uno significa otra cosa. Se usa **NULL**, una marca especial de falta de información.

> 💡 **Recuerda:** NULL no equivale a un cero ni a un texto vacío; es solo un marcador de información faltante.

NULL aparece por dos motivos:
- **Ausencia de valor**: el dato no existe. Ejemplo: una persona sin teléfono fijo.
- **Valor desconocido**: el dato existe, pero no se sabe. Ejemplo: el usuario no recuerda el número.

También se usa cuando el dato aún no se ha registrado o no aplica a ese caso.

| Situación | Significado | Ejemplo |
|---|---|---|
| **Ausencia de valor** | El dato no existe | No tener teléfono |
| **Valor desconocido** | Existe, pero se ignora | Olvidar el número |

## 7.2. Lógica ternaria en SQL

Como NULL no es un valor real, el sistema no puede saber si una comparación con él es verdadera o falsa. SQL aplica una **lógica ternaria**: verdadero, falso y desconocido.

| Situación | Resultado |
|---|---|
| El dato coincide con la búsqueda | Verdadero |
| El dato es distinto | Falso |
| Un valor es nulo | Desconocido |

> 💡 **Recuerda:** NULL no es un cero matemático ni un espacio en blanco; es la ausencia total de información en una casilla.

## 7.3. IS NULL, IS NOT NULL, COALESCE y NULLIF

- **IS NULL**: encuentra las celdas vacías.
- **IS NOT NULL**: selecciona solo las filas que sí tienen información.
- **COALESCE**: revisa una lista y devuelve el **primer valor no nulo**. Ejemplo: muestra el teléfono secundario o, si no existe, el texto "No proporcionado".
- **NULLIF**: compara dos valores. Si son iguales, devuelve **NULL**. Sirve para tratar ciertos valores especiales como ausencia de información.

| Función | Propósito |
|---|---|
| **IS NULL** | Encuentra los campos sin información |
| **IS NOT NULL** | Selecciona las celdas con algún dato |
| **COALESCE** | Cambia un vacío por el primer dato válido |
| **NULLIF** | Borra el contenido si los dos valores coinciden |

---

# 8. Claves foráneas

Ejemplo: en una biblioteca, para saber quién tiene cada libro no se anotan todos los datos del lector, solo su **número de socio**. Ese número conecta el préstamo con su ficha. Las bases de datos funcionan igual: relacionan la información sin duplicarla.

Cuando se borra o modifica un registro vinculado, unas reglas (CASCADE, SET NULL, RESTRICT…) controlan qué ocurre. Todo esto se gestiona con **DDL** y **DCL**.

## 8.1. Clave foránea e integridad referencial

- **Clave primaria**: identifica cada elemento de forma exacta y evita duplicados.
- **Clave foránea** (restricción FK): apunta a la clave primaria de otra tabla, la **tabla referenciada**.
- **Integridad referencial**: regla que asegura la coherencia. Ejemplo: el sistema impide registrar una compra de un cliente que no existe.

| Componente | Función | Ejemplo cotidiano |
|---|---|---|
| **Clave primaria** | Identificar un elemento de forma exclusiva | El DNI de una persona |
| **Clave foránea** | Vincular con la tabla referenciada | El DNI anotado en un contrato de alquiler |

> 💡 **Recuerda:** la clave primaria identifica de forma única un registro; la clave foránea enlaza con un dato que está en otra tabla.

## 8.2. Acciones referenciales: CASCADE, SET NULL, SET DEFAULT y RESTRICT

Definen qué ocurre con los registros dependientes cuando se borra o modifica el registro referenciado.

- **RESTRICT**: detiene la operación. Ejemplo: no deja borrar un cliente que tiene pedidos, para no dejar compras sin dueño.
- **CASCADE**: propaga el cambio. Si se borra un cliente, se borran también sus pedidos. Hay que usarla con precaución.
- **SET NULL**: conserva los datos vinculados, pero deja el campo de conexión vacío. Ejemplo: se borra un producto y su historial de ventas sigue existiendo sin producto asignado.
- **SET DEFAULT**: sustituye el dato borrado por un valor predeterminado. Ejemplo: asignar el registro a un perfil general.

| Regla | Efecto |
|---|---|
| **RESTRICT** | Bloquea el borrado si hay datos conectados |
| **CASCADE** | Elimina o actualiza en cadena |
| **SET NULL** | Mantiene el registro pero vacía la conexión |
| **SET DEFAULT** | Pone un valor por defecto en la conexión |

## 8.3. Definición de claves foráneas en DDL: claves compuestas y desactivación temporal

Las tablas y sus claves se crean con **DDL**. La clave foránea actúa como un puente hacia otra tabla. Definirla bien mantiene la integridad referencial.

- **Claves compuestas**: varias columnas forman un solo identificador, cuando un dato no basta.
- **Desactivación temporal**: se deja de comprobar las claves foráneas durante una operación controlada.

> 🧠 **Saber más:** desactiva las claves foráneas solo en cargas controladas, con copias de seguridad y comprobando después la integridad referencial, porque el SGBD no revisará las referencias mientras tanto.

Las **vistas** muestran datos concretos sin cambiar las tablas originales. El **DCL** decide quién puede ver o modificar los datos.

| Concepto | Función |
|---|---|
| **Clave primaria** | Identifica cada fila en su propia tabla |
| **Clave foránea** | Crea un puente hacia los datos de otra tabla |
| **Vistas** | Muestran una parte de los datos como ventana filtrada |

---

# 9. Vistas

Las **vistas** sirven para reutilizar una misma consulta que muestra información filtrada o combinada.

- Se crean y eliminan con comandos de estructura (DDL).
- Su acceso se controla con instrucciones de control (DCL).
- **WITH CHECK OPTION** funciona como un guardia: comprueba que los cambios hechos desde la vista cumplen sus condiciones. Solo tiene sentido en vistas actualizables.

Tipos de vistas:
- **Actualizables**: permiten añadir, cambiar o borrar registros de la tabla de origen, si el SGBD lo permite.
- **No actualizables**: solo sirven para consultar. Ocurre cuando son complejas, agrupan resultados o mezclan varias tablas.
- **Materializadas**: guardan físicamente el resultado de la consulta en un momento dado. Dan respuestas muy rápidas, pero deben refrescarse para reflejar los cambios.

## 9.1. Concepto de vista: ventajas y diferencias con las tablas base

Una vista recopila información al instante para leerla sin alterar los datos físicos. Oculta columnas sensibles y muestra a cada usuario solo lo que necesita.

Ejemplo: un empleado ve el nombre y el correo de los clientes, pero no sus contraseñas, datos bancarios ni márgenes de beneficio.

- **Tablas base**: almacenan los datos físicos.
- **Vistas**: muestran el resultado de una consulta sobre una o varias tablas. Simplifican el trabajo diario.

| Característica | Tabla base | Vista |
|---|---|---|
| Almacenamiento | Guarda los datos en el disco | No guarda datos físicamente |
| Propósito | Almacenar toda la información | Mostrar datos muy específicos |

> 💡 **Recuerda:** una vista nunca almacena datos por sí misma; funciona como un filtro sobre las tablas base.

## 9.2. Creación, modificación y eliminación de vistas: DDL y WITH CHECK OPTION

Las vistas se crean con **DDL**. Simplifican las búsquedas, protegen datos confidenciales y muestran a cada usuario el resumen que necesita.

| Comando | Acción |
|---|---|
| **CREATE VIEW** | Crear una vista nueva |
| **ALTER VIEW** | Modificar una vista existente |
| **DROP VIEW** | Borrar la vista del sistema |

**WITH CHECK OPTION** se añade al crear la vista. Comprueba que las modificaciones hechas a través de ella siguen cumpliendo la condición definida. Si el dato no pasa la revisión, no se guarda.

> 💡 **Recuerda:** las vistas estándar no almacenan datos propios, solo muestran el resultado de una consulta. Algunas son actualizables y otras solo de consulta, según su definición y el SGBD.

## 9.3. Vistas actualizables, no actualizables y materializadas

- **Vistas actualizables**: permiten cambiar la información, y el cambio se guarda en la tabla original.
- **Vistas no actualizables**: combinan datos de distintos lugares; solo se pueden leer. Una vista deja de ser actualizable si incluye:
  - agrupaciones,
  - funciones de agregación,
  - uniones complejas,
  - DISTINCT,
  - o cálculos que impiden saber qué fila original modificar.
- **Vistas materializadas**: guardan una copia física del resultado de una consulta, como una foto instantánea.
  - Ventaja: lectura rápida.
  - Inconveniente: pueden quedar desactualizadas si no se refrescan.
  - Usarlas implica valorar cada cuánto cambian los datos frente a la necesidad de leer rápido.

| Vista | Modificable | Almacenamiento |
|---|---|---|
| **Actualizable** | Sí | Virtual |
| **No actualizable** | No | Virtual |
| **Materializada** | Generalmente no | Físico |

---

# 10. Usuarios. Privilegios

Para proteger la información hay que controlar quién accede a cada objeto. De esto se encarga el **DCL** (Data Control Language), que permite otorgar o quitar permisos.

1. Primero se **gestionan los usuarios**: crear, modificar, eliminar y verificar su identidad.
2. Después se asignan **privilegios**:
   - **de sistema**, para tareas generales;
   - **de objeto**, para datos concretos.
3. **GRANT** da privilegios y **REVOKE** los retira.
4. Un **rol** agrupa varios permisos en un perfil. Ejemplo: un rol con los permisos de ventas para todos los comerciales. Si cambia el puesto, basta con modificar el rol.

## 10.1. Gestión de usuarios en el SGBD: creación, modificación, eliminación y autenticación

El **DCL** define quién accede y qué puede hacer. Según el SGBD, los usuarios se gestionan con:
- **CREATE USER**
- **ALTER USER**
- **DROP USER**

También hay herramientas gráficas, como **pgAdmin**, **MySQL Workbench** o **phpMyAdmin**, que permiten crear tablas, usuarios y consultas sin escribir SQL.

Gestión de cuentas:
1. **Crear**: registrar a los empleados nuevos.
2. **Modificar**: actualizar los permisos cuando alguien cambia de puesto.
3. **Eliminar**: borrar las cuentas que ya no se usan.

Desactivar una cuenta puede ser más seguro que eliminarla, si se necesita conservar el historial de auditoría.

**Autenticación**: comprueba que el usuario y la contraseña coinciden con las credenciales registradas. En entornos profesionales también se usan:
- políticas de contraseñas,
- autenticación externa,
- certificados,
- autenticación multifactorial.

## 10.2. Privilegios de sistema y de objeto: granularidad, GRANT y REVOKE

- **Privilegios de sistema**: permisos muy amplios, como crear usuarios o cambiar la configuración. Son como una llave maestra.
- **Privilegios de objeto**: acciones concretas, como leer o modificar una única tabla. Son como una llave normal.

Las vistas permiten consultar datos concretos sin dar acceso a toda la tabla.

- **GRANT**: entrega una autorización.
- **REVOKE**: retira un permiso.
- **Granularidad**: capacidad de ajustar con exactitud lo que puede hacer cada usuario.

> 💡 **Recuerda:** GRANT da permisos y REVOKE los quita, para que cada usuario solo acceda a los datos que necesita.

## 10.3. Roles: creación, asignación de privilegios y ventajas

Dar permisos uno por uno exige repetir la misma operación para cada usuario. Para solucionarlo se crea un **rol**, que agrupa varios privilegios en un mismo perfil.

Si un empleado cambia de departamento, se le cambia el perfil y recibe sus nuevas autorizaciones automáticamente.

Ventajas:
- menos tareas repetitivas,
- cambios más sencillos,
- menos errores humanos,
- acceso limitado a lo que necesita cada puesto.

> 💡 **Recuerda:** agrupar autorizaciones en roles evita fallos de seguridad y simplifica el mantenimiento.

| Característica | Asignación individual | Asignación por roles |
|---|---|---|
| Tiempo invertido | Elevado | Reducido |
| Riesgo de errores | Mayor | Menor |
| Ante cambios | Requiere más ajustes | Se centraliza en el rol |

---

# 11. Lenguaje de definición de datos (DDL)

El **DDL** sirve para definir, cambiar y borrar las estructuras donde se guarda la información. El **DCL**, en cambio, solo gestiona permisos.

Comandos para tablas:
- **CREATE TABLE**: crea una tabla.
- **ALTER TABLE**: cambia el diseño de una tabla existente.
- **DROP TABLE**: borra una tabla.

Estos comandos no cambian los datos de las filas. Eso lo hacen **INSERT**, **UPDATE** y **DELETE**.

Otros objetos:
- **CREATE SCHEMA**: agrupa objetos de forma ordenada.
- **CREATE INDEX**: crea índices para reducir lecturas.
- **CREATE VIEW**: crea vistas (tablas virtuales).

**Restricciones** (constraints): reglas que rechazan valores incorrectos. Las habituales son **PRIMARY KEY**, **FOREIGN KEY**, **NOT NULL**, **UNIQUE** y **CHECK**. Con DDL se pueden añadir, modificar o quitar.

## 11.1. Sentencias DDL para tablas: CREATE TABLE, ALTER TABLE y DROP TABLE

**CREATE TABLE** crea una tabla nueva. En ella se define la clave primaria y se pueden crear claves foráneas.

```sql
CREATE TABLE cliente (
  id_cliente INT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  email VARCHAR(150) UNIQUE
);
```

**ALTER TABLE** agrega o quita columnas sin perder los datos guardados. También sirve para:
- añadir restricciones,
- cambiar tipos de datos,
- crear relaciones.

Algunas operaciones pueden estar limitadas si la tabla ya tiene datos.

```sql
ALTER TABLE cliente ADD COLUMN telefono VARCHAR(20);
```

**DROP TABLE** elimina la estructura **y** los datos.

```sql
DROP TABLE cliente;
```

> 💡 **Recuerda:** DROP TABLE elimina definitivamente la tabla y todo su contenido. Úsalo con mucha precaución.

| Comando | Función | Ejemplo |
|---|---|---|
| **CREATE TABLE** | Crear una tabla nueva | Crear la estructura de una tabla |
| **ALTER TABLE** | Modificar la estructura | Modificar columnas o restricciones |
| **DROP TABLE** | Borrar la tabla por completo | Eliminar la estructura de la tabla |

## 11.2. Sentencias DDL para otros objetos: CREATE INDEX, CREATE VIEW y CREATE SCHEMA

- **CREATE SCHEMA**: agrupa la información por áreas, es decir, agrupa los objetos de la base de datos por esquema.
- **CREATE VIEW**: crea una consulta guardada que se presenta como tabla virtual. Protege la información original y muestra solo los datos filtrados.
- **CREATE INDEX**: crea una estructura auxiliar para localizar registros con menos lecturas. Es útil cuando hay miles de registros. Ocupa espacio y se actualiza cuando cambian los datos.

> 🧠 **Saber más:** las vistas no almacenan datos; solo guardan la consulta y muestran la información actualizada al instante.

| Comando | Función | Ejemplo |
|---|---|---|
| **CREATE SCHEMA** | Agrupar objetos | Agrupar objetos de la base de datos |
| **CREATE VIEW** | Filtrar datos | Mostrar el resultado de una consulta |
| **CREATE INDEX** | Acelerar búsquedas | Reducir lecturas en consultas compatibles |

## 11.3. Gestión de restricciones con DDL: añadir, modificar y eliminar

Las **restricciones** son reglas sobre columnas o tablas que limitan los valores admitidos. Con DDL se pueden:
- **añadir** a tablas que ya están en uso, sin borrar sus datos;
- **modificar**;
- **eliminar** cuando ya no encajan con el diseño.

Si la tabla ya tiene datos, el SGBD comprueba que cumplen la nueva regla y puede rechazarla. Por eso conviene revisar los datos antes.

| Acción | Función | Ejemplo |
|---|---|---|
| **Añadir** | Crear una obligación | Exigir mayoría de edad |
| **Modificar** | Cambiar una norma | Ajustar el código postal |
| **Eliminar** | Quitar una regla | Hacer un campo opcional |

---

# 12. Lenguaje de control de datos (DCL)

El **DCL** administra los permisos de los usuarios y protege la información. Dentro de SQL agrupa las instrucciones para conceder y retirar privilegios sobre usuarios, roles y objetos.

- **GRANT**: da acceso para tareas concretas.
  - **WITH GRANT OPTION**: permite que el usuario dé esos mismos permisos a otros. Sirve para delegar.
- **REVOKE**: quita permisos.
  - **CASCADE**: los quita en cadena a todos los que los recibieron de ese usuario.
  - **RESTRICT**: impide retirar el permiso si otros usuarios dependen de él.
- **Auditoría**: registro histórico de la actividad. Sirve para detectar comportamientos raros e investigar incidentes. No sustituye a los permisos, los complementa: deja constancia de **quién hizo qué y cuándo**.

## 12.1. Sentencia GRANT y WITH GRANT OPTION

**GRANT** entrega privilegios para leer o modificar información. Se indica:
1. el privilegio,
2. el objeto,
3. el usuario o rol que lo recibe.

```sql
GRANT SELECT, INSERT ON cliente TO usuario_lectura;
```

> 💡 **Recuerda:** GRANT concede privilegios sobre objetos o acciones del sistema; WITH GRANT OPTION permite al usuario conceder esos permisos a otros.

GRANT también sirve para otros objetos, como vistas o índices, con la misma sintaxis.

| Lenguaje | Función | Ejemplo |
|---|---|---|
| **DDL** | Construir la estructura | Crear una tabla |
| **DCL** | Controlar el acceso | Otorgar un acceso |

**WITH GRANT OPTION** se añade al final para que el usuario pueda compartir sus permisos. Le da mucha responsabilidad, así que requiere control.

```sql
GRANT SELECT ON cliente TO usuario_lectura WITH GRANT OPTION;
```

> 🧠 **Saber más:** si se revoca un permiso dado con WITH GRANT OPTION, el efecto sobre los permisos derivados depende de la opción (CASCADE o RESTRICT) y del SGBD.

## 12.2. Sentencia REVOKE: CASCADE y RESTRICT

**REVOKE** retira los permisos de un usuario sobre una tabla.

```sql
REVOKE INSERT ON cliente FROM usuario_lectura;
```

- **RESTRICT**: bloquea la retirada si el usuario ya compartió el permiso con otros.
- **CASCADE**: quita el permiso al usuario y a todos los que lo recibieron de él (según el SGBD).

```sql
REVOKE SELECT ON cliente FROM usuario_lectura RESTRICT;
REVOKE SELECT ON cliente FROM usuario_lectura CASCADE;
```

| Opción | Comportamiento |
|---|---|
| **RESTRICT** | Frena la acción si el permiso se ha compartido con otros |
| **CASCADE** | Quita el acceso al usuario y a todos los conectados a él |

## 12.3. Auditoría y trazabilidad: registros de actividad y buenas prácticas

Para auditar conviene registrar las operaciones relevantes. Ayudan varias cosas:
- Los identificadores únicos muestran qué elemento concreto se modificó.
- Las vistas limitan el acceso a información privada y permiten supervisar las consultas.
- Una buena gestión de permisos genera registros de **quién accede, qué hace y cuándo**.

La auditoría debe registrar:
- inicios de sesión,
- cambios de permisos,
- modificaciones de datos sensibles,
- errores de acceso,
- intentos fallidos de autenticación.

Estos registros también deben protegerse, porque contienen información sensible.

Buenas prácticas:
- Revisar periódicamente usuarios, roles y privilegios.
- Aplicar el principio de **mínimo privilegio**.
- Separar las cuentas personales de las de aplicación.
- No compartir usuarios entre varias personas.

---

# Resumen

- **Modelo relacional**: organiza los datos en tablas.
  - Niveles **ANSI/SPARC**.
  - **Álgebra relacional**: selección, proyección, unión, diferencia y producto cartesiano.
- **Términos**: relación, tupla, atributo, dominio, esquema e instancia.
- **Para definir tablas coherentes**: tipos de datos, claves primarias, claves artificiales, restricciones y NULL.
- **Relaciones**: claves foráneas e integridad referencial, con CASCADE, SET NULL, SET DEFAULT y RESTRICT.
- **Otros elementos**: índices y vistas.
- **Seguridad**: usuarios, privilegios, roles y auditoría.
- **Lenguajes**:
  - **DDL** crea y modifica tablas, vistas, índices y esquemas.
  - **DCL** concede y retira permisos con GRANT y REVOKE.

---

# Glosario

- **Atributo**: columna de una tabla que representa una propiedad de los datos.
- **CASCADE**: extiende un borrado o una revocación a los elementos dependientes (filas relacionadas o privilegios derivados).
- **CHECK**: restricción con una condición que deben cumplir los valores.
- **Clave artificial**: clave primaria generada por el sistema (autoincremento o UUID), independiente de los datos reales.
- **Clave candidata**: atributo(s) que identifican cada fila y pueden ser clave primaria.
- **Clave foránea**: atributo que referencia la clave primaria de otra tabla.
- **Clave primaria**: campo(s) que identifican cada fila, sin repetidos ni nulos.
- **DCL**: Lenguaje de Control de Datos (GRANT, REVOKE).
- **DDL**: Lenguaje de Definición de Datos (tablas, vistas, índices, esquemas).
- **Diferencia**: filas de una relación que no están en otra con el mismo esquema.
- **Dominio**: conjunto de valores válidos de un atributo.
- **Esquema**: estructura y reglas de una base de datos, sin los datos.
- **GRANT**: concede privilegios sobre un objeto a un usuario o rol.
- **Índice**: estructura que permite encontrar datos sin revisar toda la tabla.
- **Instancia**: datos almacenados en un momento concreto.
- **Integridad referencial**: reglas que mantienen la coherencia entre tablas relacionadas por una clave foránea.
- **Modelo relacional**: sistema que organiza los datos en tablas relacionadas.
- **NOT NULL**: impide que una columna almacene NULL.
- **Privilegios**: derechos para hacer operaciones sobre los objetos.
- **Producto cartesiano**: combina cada fila de una relación con cada fila de otra.
- **Proyección**: obtiene un subconjunto de columnas.
- **Relación**: nombre técnico de una tabla.
- **Restricción**: regla que limita los valores que se pueden almacenar.
- **RESTRICT**: impide borrar o revocar si existen elementos dependientes.
- **REVOKE**: retira privilegios concedidos a un usuario o rol.
- **Rol**: conjunto de privilegios con un nombre, que se asigna a usuarios.
- **Selección**: obtiene las filas que cumplen una condición.
- **Superclave**: identifica cada fila aunque incluya información redundante.
- **Tupla**: fila con los datos de un registro.
- **Unión**: combina las filas de dos relaciones con el mismo esquema, sin duplicados.
- **UNIQUE**: impide valores repetidos (según el SGBD, permite NULL).
- **Valor NULL**: ausencia de información o dato desconocido.
- **Vista**: tabla virtual generada por una consulta.
