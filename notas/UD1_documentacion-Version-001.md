# UD1 · Almacenamiento de la información

> De los **ficheros** a las **bases de datos**: qué son, tipos, SGBD, arquitectura centralizada/distribuida, **protección de datos** y **Big Data**.

## Índice
1. Ficheros (planos, indexados, acceso directo)
2. Bases de datos: concepto, modelos y ubicación
3. Sistemas gestores de bases de datos (SGBD)
4. Centralizadas vs. distribuidas y fragmentación
5. Legislación sobre protección de datos
6. Big Data e inteligencia de negocios

---

## 1. Ficheros

| Tipo               | Cómo guarda / busca                                                                                               | Uso ideal            | Velocidad                |
| ------------------ | ----------------------------------------------------------------------------------------------------------------- | -------------------- | ------------------------ |
| **Plano**          | Secuencial (cada dato nuevo, normalmente al final). Sin índice → hay que leer desde el principio                  | Pocos datos          | Lenta                    |
| **Indexado**       | Fichero + **índice** (estructura auxiliar que relaciona claves con la posición del registro)                      | Búsquedas frecuentes | Rápida                   |
| **Acceso directo** | Se **calcula la posición** del registro a partir de una clave (operación matemática sobre un identificador único) | Acceso inmediato     | Alta en accesos directos |
|                    |                                                                                                                   |                      |                          |

### 1.1 Ficheros planos
- Archivo de texto sencillo, información **línea a línea**, sin estructura compleja.
- Ejemplos: `.txt`, `.csv`, `.log`, `.env` (también `.json` y `.xml`, que ya organizan la información con estructura propia).
- ✅ Ventaja: sencillos, se transportan entre sistemas sin SGBD.
- ❌ Limitación: al crecer el volumen, se lee todo hasta encontrar el dato.
- Usos: `.env` (configuración), `.log` (errores), `.csv` (exportar usuarios).
- Si guardan datos personales → hay que aplicar protección de datos (control de accesos, limitar lo recogido, conservar solo el tiempo necesario).

| Característica | Fichero plano | Base de datos |
|---|---|---|
| Complejidad de uso | Baja | Media o alta |
| Velocidad de búsqueda | Lenta | Menos recorrido en búsquedas estructuradas |
| Seguridad | Básica | Control de accesos, restricciones y auditoría |

### 1.2 Ficheros indexados
- El **índice** no sustituye a los datos: es una **guía** para encontrarlos (como el índice de un libro).
- Ej.: guarda el identificador de cada cliente y la posición de su registro.
- ❌ Coste: ocupa **espacio adicional** y debe **actualizarse** en cada insertar/modificar/eliminar. Si queda desactualizado → posiciones incorrectas o registros perdidos.

### 1.3 Acceso directo y criterios de elección
- Se salta directo a la posición sin recorrer el archivo.
- Su eficacia depende del **método de direccionamiento** y de cómo se gestionen **colisiones** o posiciones ocupadas.
- Indexado: tabla auxiliar. Directo: se necesita conocer la clave o posición.
- Elegir según: tamaño del proyecto, rapidez de acceso, y también seguridad, integridad, acceso simultáneo y necesidad de relacionar registros.

---

## 2. Bases de datos

### 2.1 Concepto
- **Base de datos:** conjunto organizado de datos relacionados, almacenados de forma estructurada para facilitar su **consulta, modificación, protección e integridad**.
- **BD ≠ SGBD:** la BD es el conjunto de datos; el SGBD es el programa que la crea, consulta, modifica, protege y administra.
- Objetivo: organizar datos, reducir duplicidades, permitir consultas/actualizaciones y controlar accesos.

| Característica | Sistema de ficheros | Base de datos |
|---|---|---|
| Búsqueda | Lenta y manual | Mediante consultas e índices |
| Repetición | Alta (datos duplicados) | Reduce y controla la duplicación |
| Seguridad | Permisos del sistema de archivos | Permisos, roles y auditoría según el SGBD |
| Integridad | La controla la aplicación | La puede gestionar el SGBD (reglas y restricciones) |
| Concurrencia | Problemática si varios modifican a la vez | Controlada con mecanismos internos |

💡 Toda BD con datos personales debe aplicar medidas de seguridad técnicas para cumplir el **RGPD**.

### 2.2 Tipos según el modelo de datos
| Modelo | Organización | Cuándo usarlo | Ejemplo |
|---|---|---|---|
| **Relacional** | Tablas y columnas | Estructura clara y muchas relaciones (clientes, pedidos, facturas) | Facturas |
| **Documental** | Documentos semiestructurados | Estructuras variables (perfiles, catálogos) | Catálogos o perfiles |
| **Clave-valor** | Etiqueta y dato | Acceso muy rápido (sesiones, caché, preferencias) | Ajustes |
| **Grafos** | Puntos (nodos) conectados | Importan las relaciones (amistades, rutas, recomendaciones) | Redes |
| **Columnas anchas** | — | Datos masivos distribuidos, registros de actividad, sensores | — |

- No hay un modelo válido para todo: depende de la forma de la información y de cómo haya que conectarla.
- También existen las bases de datos **orientadas a objetos**.

### 2.3 Tipos según la ubicación
| Tipo | Almacenamiento |
|---|---|
| **Local** | Equipo propio |
| **Remota** | Servidor externo accesible por red |
| **Nube** | Servidores de un proveedor externo, por internet |
| **Híbrida** | Equipo propio + online |

- La ubicación afecta a: rendimiento, administración, disponibilidad y seguridad.
- La obligación legal de proteger datos existe **dondequiera** que estén. Valorar: quién administra la infraestructura, dónde se alojan los datos, qué permisos se aplican y qué garantías ofrece.
🧠 Las grandes empresas usan BD distribuidas por todo el mundo: si un equipo falla, la web sigue funcionando.

---

## 3. SGBD (Sistema Gestor de Bases de Datos)

**SGBD:** programa que permite crear, mantener y controlar el acceso a una base de datos.

### 3.1 Funciones
| Función        | Descripción                                                                                      |
| -------------- | ------------------------------------------------------------------------------------------------ |
| Almacenamiento | Guarda los datos de forma organizada y persistente                                               |
| Recuperación   | Permite restaurar la información si ocurre un fallo o se<br>necesita volver a una copia anterior |
| Concurrencia   | Gestiona accesos simultáneos reduciendo conflictos e<br>inconsistencias.                         |
| Integridad     | Datos correctos y coherentes con las reglas; evita valores imposibles o que rompan relaciones    |
| Seguridad      | Protege los datos de accesos no autorizados                                                      |

- Además: ejecutar **transacciones** (se completan todas o se cancelan por completo).
- Ayuda a cumplir RGPD/LOPDGDD, pero el cumplimiento depende también de la configuración, las medidas aplicadas y el uso que haga la organización.

### 3.2 Componentes
| Componente | Función principal | Ejemplo |
|---|---|---|
| **Motor** | Ejecuta consultas, gestiona transacciones, coordina el acceso físico a los datos (guarda y busca en disco) | Ejecución de una consulta SQL |
| **Diccionario de datos** | Catálogo interno: tablas, campos, tipos, relaciones, restricciones, permisos (metadatos) | Catálogo interno del SGBD |
| **Interfaz de usuario** | Permite administrar y consultar sin programar | Consola SQL o herramienta gráfica |

- Muchos SGBD incluyen también: procesador de consultas, optimizador, gestor de transacciones, copias de seguridad, recuperación ante fallos, conectores para lenguajes y herramientas de administración (usuarios, rendimiento).

### 3.3 Tipos de SGBD
| Tipo                    | Característica                                                                                                             | Ejemplos (SGBD)                                         | Uso                                                     |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------- |
| **Relacional**          | Guardan la información en tablas conectadas entre si  suelen utilizar SQL                                                  | MySQL, MariaDB, PostgreSQL, Oracle Database, SQL Server | Gestión, tiendas online, facturación, registro bancario |
| **NoSQL**               | Destacan por ser muy flexibles dentro de NoSQL se encuentran modelos documentales, clave-valor, columnas anchas, grafos.   | MongoDB, Redis, Cassandra, Neo4j                        | Redes sociales                                          |
| **NewSQL**              | Modelo relacional + escalabilidad combinan el uso de tablas con una alta velocidad de trabajo y capacidad de distribución. | CockroachDB, TiDB, Google Spanner                       | Sistemas globales de alta concurrencia                  |
| **Orientado a objetos** | Almacenan la información en bloques de datos mas complejos integrando datos (cercano a POO)                                | —                                                       | Ingeniería                                              |

- Elegir según: tipo de información, volumen, necesidad de relaciones, usuarios simultáneos, seguridad y rendimiento.

---

## 4. Centralizadas vs. distribuidas

| Característica | Centralizada | Distribuida |
|---|---|---|
| Ubicación | Un solo servidor principal | Varios equipos (nodos) en red |
| Mantenimiento | Muy sencillo | Más complejo |
| Riesgo de fallo | Cae el sistema si falla el servidor | Puede seguir funcionando si hay replicación, redundancia y tolerancia a fallos |
| Escalabilidad | Más limitada | Mayor (se añaden nodos) |

### 4.1 Centralizada
- Todo en **un único servidor**. Instalación rápida, control concentrado.
- ❌ Concentra el riesgo: si falla, todo se detiene (salvo copias de seguridad, servidor de respaldo o alta disponibilidad).
- Encaja en: apps pequeñas/medianas, administración interna, prototipos, pocos usuarios concurrentes.
- Para reducir riesgos: copias de seguridad, monitorización, restauración probada y servidor de respaldo.

### 4.2 Distribuida: CAP y ACID
- Reparte la información entre varios ordenadores (**nodos**) conectados en red. Usa fragmentación y/o replicación.
- **ACID:** atomicidad, consistencia, aislamiento y durabilidad → fiabilidad de las transacciones.
- **Teorema CAP:** ante una partición de red, no se puede garantizar a la vez consistencia, disponibilidad y tolerancia a particiones → hay que priorizar.

| Propiedad CAP | Significado |
|---|---|
| Consistencia | Todas las lecturas reciben datos coherentes |
| Disponibilidad | Los nodos disponibles responden, aunque pueda haber compromisos con la consistencia |
| Tolerancia a particiones | Funciona aunque falle la conexión de red |

- Ejemplos: un sistema **bancario** prioriza la consistencia; una **red social** acepta pequeños retrasos para mantener la disponibilidad.

### 4.3 Fragmentación, replicación y transparencia
| Técnica | División | Ejemplo |
|---|---|---|
| **Horizontal** | Por **filas** | Clientes europeos en un servidor y americanos en otro (clientes por país) |
| **Vertical** | Por **columnas** | Identificación en un nodo; contacto/preferencias en otro |
| **Mixta** | Filas + columnas | Cuentas por regiones |

- Objetivo: repartir carga, mejorar rendimiento, dividir la administración y adaptar el almacenamiento a la ubicación de usuarios/servicios.
- **Replicación:** copias (totales o parciales) en varios nodos → más disponibilidad y tolerancia a fallos. Exige **sincronización** para evitar inconsistencias.
- **Transparencia de distribución:** el usuario ve la información como si estuviera en un único lugar.
- Siempre: cumplir la ley (dónde se almacenan los datos, quién accede, protección, copias y servidores en distintos países).

---

## 5. Legislación sobre protección de datos

- **Dato personal:** cualquier información que permite identificar a una persona (nombre, DNI, correo).
- **RGPD:** Reglamento (UE) **2016/679**. Norma europea para tratar los datos personales de forma lícita, transparente y segura.
- **LOPDGDD:** Ley Orgánica **3/2018**. Adapta/complementa el RGPD en España y garantiza los derechos digitales.
- Aplica a: BD relacionales, NoSQL, ficheros, copias de seguridad, registros y servicios en la nube.
- Incumplir → multas muy altas y daño a la confianza y la reputación.

### 5.1 RGPD
- Se pueden tratar datos si hay una **base jurídica válida**: consentimiento, ejecución de un contrato, obligación legal o interés legítimo justificado.
- Consentimiento: **válido, libre, informado, específico e inequívoco**. (No todo tratamiento necesita consentimiento: una tienda necesita ciertos datos para tramitar una compra o facturar.)
- Nunca guardar datos personales sin informar antes a la persona.
- **Derechos del interesado:**
  - Acceso a sus datos
  - Rectificación (si hay errores)
  - Supresión / **derecho al olvido** (cuando proceda)
  - Limitación del tratamiento
  - Oposición al uso de los datos
  - Portabilidad (recibirlos en formato estructurado)

### 5.2 LOPDGDD
- Pedir **solo la información necesaria** (una tienda no necesita preguntar sobre la salud).
- Configurar bien los programas para bloquear ataques; permisos por **roles**, contraseñas seguras, copias de seguridad, cifrado cuando sea adecuado y registro de accesos o modificaciones.
- El sistema debe ofrecer mecanismos para ejercer los derechos (acceder, corregir, suprimir).

| Requisito legal | Aplicación en el sistema |
|---|---|
| Minimización | Recoger solo los datos necesarios para la finalidad |
| Seguridad técnica | Contraseñas y cifrado |
| Control de acceso | Limitar quién consulta cada archivo |

### 5.3 Implicaciones prácticas en el diseño
Principios de diseño: **minimización de datos**, **limitación de finalidad**, control de accesos, conservación el tiempo necesario, protección **desde el diseño y por defecto**.

| Técnica | Función | Ejemplo |
|---|---|---|
| **Cifrado** | Transformar la información para que solo la lea quien tenga la clave | Cifrar copias de seguridad o comunicaciones |
| **Anonimización** | Eliminar/transformar datos identificativos para que **no** pueda asociarse a una persona | Quitar identificadores hasta que sea imposible reidentificar |
| **Auditoría** | Registro continuo de quién hizo qué y cuándo (como un "libro de visitas") | Guardar la hora de entrada |
| **Hash con sal** | Proteger contraseñas | Guardar credenciales de forma segura |

- Contraseñas: **no** cifradas de forma reversible → **hash seguro con sal**.
- Si aún se puede reidentificar combinando datos → es **seudonimización**, no anonimización completa.
- Proteger también los **registros de auditoría** (pueden contener información sensible).
- La seguridad no depende solo del programa: también del diseño, los permisos y el ciclo de vida de la información.

---

## 6. Big Data e inteligencia de negocios

### 6.1 Las 5V
**Big Data:** tecnologías y técnicas para almacenar y analizar grandes volúmenes de datos heterogéneos generados a alta velocidad.

| V | Significado |
|---|---|
| **Volumen** | Cantidad de información almacenada |
| **Velocidad** | Rapidez con que se generan y procesan |
| **Variedad** | Diversidad de formatos (texto, imágenes, audio, vídeo) |
| **Veracidad** | Fiabilidad de los datos (si son incompletos, duplicados o incorrectos, las conclusiones también) |
| **Valor** | Utilidad práctica para decidir (no todos los datos aportan valor) |

- Cruzar grandes volúmenes puede permitir identificar patrones personales aunque los datos parezcan aislados → especial atención a la privacidad.

### 6.2 Tecnologías
**Procesamiento distribuido:** dividir los datos y las tareas de cálculo en trozos para analizarlos a la vez en varios ordenadores (más rápido, menos carga en un único servidor).

| Tecnología | Uso principal |
|---|---|
| **Hadoop** | Plataforma libre: almacenamiento y procesamiento paralelo de grandes volúmenes de datos en varios nodos |
| **Spark** | Procesamiento distribuido de alta velocidad: análisis **en memoria**, aprendizaje automático, datos en tiempo casi real |

- Al mover tanta información: seguridad, control de acceso, cifrar comunicaciones, registrar operaciones y no conservar datos personales más tiempo del necesario.

### 6.3 Inteligencia de negocios (BI)
- **BI:** tecnologías y herramientas que analizan los datos de la organización para **apoyar la toma de decisiones**.
- Resultados en **dashboards** (paneles visuales): gráficos, indicadores y filtros.
- Puede incluir: indicadores clave de rendimiento (KPI), filtros interactivos, informes automáticos, comparativas históricas y alertas.
- Ejemplo (tienda online): productos más vendidos, clientes frecuentes, meses con más pedidos, campañas que funcionan mejor.
- BI **no sustituye** a las BD, se apoya en ellas: recopilar → limpiar y organizar → analizar → mostrar en dashboards.

---

## Glosario rápido
| Término | Definición |
|---|---|
| ACID | Atomicidad, consistencia, aislamiento y durabilidad de las transacciones |
| Anonimización | Eliminar/transformar datos para que no identifiquen a nadie |
| Auditoría informática | Registro de accesos y operaciones: quién y cuándo |
| BD centralizada / distribuida | Un único servidor / varios nodos en red |
| BI | Análisis de datos para decidir (dashboards) |
| Cifrado | Solo lo lee quien tiene la clave |
| Concurrencia | Varios usuarios modificando a la vez sin conflictos |
| Derecho al olvido | Pedir la eliminación de datos sin base legítima para conservarlos |
| Diccionario de datos | Descripción interna de tablas, columnas, índices, relaciones y restricciones |
| Fragmentación | Dividir una tabla en partes para repartirlas entre nodos |
| Hadoop / Spark | Procesamiento distribuido de Big Data |
| Integridad | Datos coherentes con las reglas definidas |
| LOPDGDD | Ley Orgánica 3/2018 (adapta el RGPD en España) |
| Motor de BD | Procesa consultas, transacciones y acceso físico |
| NewSQL / NoSQL | Relacional distribuido / modelos flexibles |
| Replicación | Copias sincronizadas en varios nodos |
| RGPD | Reglamento (UE) 2016/679 |
| SGBD | Programa que crea y gestiona la BD |
| Teorema CAP | No se pueden garantizar a la vez consistencia, disponibilidad y tolerancia a particiones |
| Transparencia de distribución | El usuario no nota la distribución física |

---

## ✅ Chuleta final
- Plano = lento y simple · Indexado = índice (ocupa espacio, hay que mantenerlo) · Directo = posición calculada.
- BD = datos · SGBD = programa que los gestiona (motor + diccionario + interfaz).
- Relacional (tablas) · Documental · Clave-valor · Grafos · NewSQL · Orientada a objetos.
- Local / Remota / Nube / Híbrida.
- Centralizada (simple, un punto de fallo) vs. distribuida (escalable, más compleja).
- Fragmentación: **H**orizontal = filas · **V**ertical = columnas · Mixta = ambas. Replicación = copias.
- CAP: consistencia, disponibilidad, tolerancia a particiones (hay que priorizar).
- RGPD (UE 2016/679) + LOPDGDD (LO 3/2018). Minimización, cifrado, anonimización, auditoría, hash con sal.
- Big Data = 5V (volumen, velocidad, variedad, veracidad, valor). Hadoop y Spark. BI = dashboards.
