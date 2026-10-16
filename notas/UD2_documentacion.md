# UD2 · Bases de datos relacionales

> El **modelo relacional** (tablas, claves, restricciones), **índices**, **NULL**, **vistas**, **usuarios y privilegios**, y los lenguajes **DDL** y **DCL**.

## Índice
1. Modelo de datos
2. Terminología del modelo relacional
3. Tipos de datos
4. Claves primarias
5. Restricciones de validación
6. Índices
7. El valor NULL
8. Claves foráneas
9. Vistas
10. Usuarios y privilegios
11. DDL
12. DCL

---

## 1. Modelo de datos

- **Modelo de datos:** representación organizada de la información; permite interpretar la estructura, reglas y relaciones de la BD.
- Modelo relacional: la **tabla** es el componente básico → filas = **tuplas**, columnas = **atributos**.

### 1.1 Niveles de abstracción: arquitectura ANSI/SPARC
Separa lo que ve el usuario de cómo se guardan los datos físicamente.

| Nivel | Qué representa |
|---|---|
| **Interno** | Cómo se guardan los datos físicamente (disco) |
| **Conceptual** | Qué información existe y cómo se estructura |
| **Externo** | Lo que ve cada usuario/aplicación (aquí están las **vistas**) |

| Clave | Función | Ejemplo |
|---|---|---|
| Primaria | Identifica un registro único en su tabla | Identificador de cliente generado por el sistema |
| Foránea | Conecta una tabla con otra | Código de departamento en la ficha de un empleado |

### 1.2 Características del modelo relacional
| Elemento común | Nombre técnico | Qué representa                  |
| -------------- | -------------- | ------------------------------- |
| Columna        | Atributo       | El tipo de información general  |
| Fila           | Tupla          | Registro almacenado en una fila |
| Identificador  | Clave primaria | El código único del registro    |

- Clave foránea: columna (o conjunto) que referencia una clave primaria **o única** de otra tabla → relaciona sin duplicar datos.
- Permite separar claramente: **estructura** (DDL), **consultas** y **seguridad** (DCL).

### 1.3 Álgebra relacional
Operaciones matemáticas sobre tablas; base teórica de las consultas SQL.

| Operación | Qué hace |
|---|---|
| **Selección** | Elige **filas** que cumplen una condición (ej. clientes mayores de edad) |
| **Proyección** | Muestra solo ciertas **columnas** (ej. nombre y correo) |
| **Unión** | Combina resultados compatibles y **elimina duplicados** (mismas columnas en número y tipo) |
| **Diferencia** | Filas que están en el primer conjunto pero **no** en el segundo |
| **Producto cartesiano** | Combina **cada fila** de una relación con **todas** las de otra (muchos resultados, consume recursos → se usa con condiciones) |

| Lenguaje | Propósito |
|---|---|
| **DDL** | Crear y modificar la estructura (tablas, vistas, índices, restricciones) |
| **DCL** | Conceder y revocar permisos (usuarios, roles, objetos) |

---

## 2. Terminología del modelo relacional

### 2.1 Equivalencias
| Término técnico | Término común | Significado |
|---|---|---|
| **Relación** | Tabla | Conjunto de datos |
| **Tupla** | Fila | Registro único |
| **Atributo** | Columna | Característica |
| **Dominio** | Tipo y valores permitidos | Conjunto de valores válidos para una columna |

- Dominio: no solo indica texto/número/fecha, sino qué valores son válidos (ej. edad = enteros positivos).
💡 La clave primaria identifica una fila. La clave foránea referencia la clave primaria de otra tabla.

### 2.2 Esquema
- **Esquema:** forma y reglas de los datos (nombres de tablas, columnas, tipos, claves, restricciones y relaciones). **No contiene datos reales.**
- **Esquema de relación:** normas de una tabla concreta.
- **Esquema de base de datos:** el sistema general y todas las conexiones entre tablas.
- Ej.: tabla `Usuarios` (con PK) y `Préstamos` (con FK al usuario).

### 2.3 Instancia
- **Instancia:** conjunto exacto de datos almacenados **en un instante concreto**.
- Cambia con cada alta/modificación/baja (evolución temporal) aunque el **esquema** siga igual.
- Ej.: 3 filas el lunes, 5 el viernes → mismo esquema, distinta instancia.

---

## 3. Tipos de datos

- Elegir bien el tipo: datos coherentes, menos errores, **menos espacio** y consultas más rápidas.
- Los nombres exactos **varían según el SGBD**.

### 3.1 Numéricos
| Tipo | Uso | Ejemplo |
|---|---|---|
| **Enteros** | Cantidades sin fracción | Edad, stock, identificador |
| **Decimales** | Valores **exactos** con fracciones | Precio |
| **Coma flotante** | Cifras con decimales variables; **aproximados** | Mediciones científicas |

- Coma flotante: puede dar pequeñas diferencias de aproximación → **no** usar para importes monetarios ni cálculos exactos.
💡 **Rango** = límites del número. **Precisión** = cuántos dígitos exactos se guardan.

### 3.2 Cadenas de caracteres
| Tipo | Tamaño | Ejemplo |
|---|---|---|
| `CHAR` | Fijo y corto | Códigos postales, códigos de país, abreviaturas |
| `VARCHAR` | Variable y corto (ajusta la longitud, ahorra espacio) | Nombres, correos, títulos, direcciones breves |
| `TEXT` | Variable y **largo** | Comentarios, artículos |

- Tener en cuenta: longitud máxima, codificación y si se usará en búsquedas/índices.
- `TEXT` no es buena opción para búsquedas frecuentes o índices completos salvo índices de **texto completo**.

### 3.3 Fecha/hora, booleanos y binarios
- **Booleano:** verdadero/falso, ocupa poco (`activo`, `verificado`, `publicado`, `acepta_notificaciones`).
- **Fecha y hora:** solo fecha, solo hora o marca temporal completa. Cuidado con la **zona horaria**.
  - ⚠️ Guardar fechas como **texto** → errores de ordenación, comparación y formato.
- **Binarios** (`BLOB`, `BYTEA`): fotos, documentos. En muchas apps web es mejor guardar el archivo **fuera** y en la BD solo la **ruta, URL o metadatos**.

---

## 4. Claves primarias

- Identifica cada registro de forma **unívoca**. Debe ser: **única, no nula, estable en el tiempo** y, si es posible, sencilla.
- Se define con DDL (`PRIMARY KEY`).

### 4.1 Superclave → clave candidata → clave primaria
| Clave | Qué es | Ejemplo |
|---|---|---|
| **Superclave** | Cualquier combinación que identifica un registro (puede llevar columnas de más) | Nombre + teléfono |
| **Clave candidata** | Superclave **mínima** (una tabla puede tener varias) | Pasaporte |
| **Clave primaria** | La candidata **elegida** | El documento oficial |

💡 La PK debe ser siempre única y **nunca vacía**.

### 4.2 Simples y compuestas
| Característica | Simple | Compuesta |
|---|---|---|
| Datos | Un dato | Dos o más datos |
| Ejemplo | Número de socio | Fila y butaca; alumno + asignatura (matrículas) |

- La compuesta **complica las claves foráneas** que la referencian → reservarla para cuando ningún atributo aislado identifica el registro.

### 4.3 Claves artificiales (surrogate keys)
- Generadas por el sistema, **sin significado de negocio**; se mantienen estables aunque cambien los datos reales.
- Se usan cuando no hay clave natural estable.

| Tipo | Funcionamiento | Ejemplo | Cuándo |
|---|---|---|---|
| **Autoincremento** | Números enteros seguidos | 1, 2, 3, 4 | Sencillo; BD centralizadas (un servidor genera los números) |
| **UUID** | Cadena larga aleatoria única | `550e8400-e29b-41d4-a716-446655440000` | Sistemas **distribuidos**, IDs generados en distintos servidores (muy baja probabilidad de colisión) |

---

## 5. Restricciones de validación

Limitan los valores que se pueden guardar y mantienen la integridad.

### 5.1 `NOT NULL`
- Obliga a que la columna **siempre tenga valor** (campos obligatorios).
- NULL ≠ cadena vacía ≠ 0 ≠ espacio en blanco.
- Diferencia con la PK: la PK exige valor **y** unicidad; `NOT NULL` solo exige que exista valor (no garantiza que sea único ni correcto).
- Ej.: nombre y correo obligatorios; teléfono secundario opcional.

### 5.2 `UNIQUE`
- Impide valores **repetidos** (ej. dos usuarios con el mismo correo).
- Una tabla puede tener **varias** restricciones UNIQUE.
- Con NULL: en muchos SGBD permite **varios NULL**, pero **depende del SGBD**.

| Característica | Clave primaria | UNIQUE |
|---|---|---|
| Objetivo | Identificar filas | Evitar repetidos |
| Acepta nulos | Nunca | Depende del SGBD; en muchos casos sí |

### 5.3 `CHECK`
- Exige que el dato cumpla una **condición** (>, <, =…) antes de guardarse: precios no negativos, rangos, estados permitidos, relaciones simples entre columnas de la misma fila, edad ≥ 18.
- ⚠️ Su comportamiento **varía según SGBD, versión, motor o configuración** → verificar la documentación y probar antes de confiar en él.

| SGBD / versión | Comportamiento | Acción recomendada |
|---|---|---|
| Aplica la restricción | Rechaza datos no válidos | Usarla normalmente |
| Tiene limitaciones | Puede no aplicar ciertas comprobaciones | Verificar documentación y hacer pruebas |

---

## 6. Índices

- Estructura auxiliar que permite encontrar datos **sin leer todas las filas** (como el índice de un libro / guía telefónica ordenada).
- Se crean automáticamente en el campo principal que identifica cada registro; se crean con **DDL**; el acceso se controla con **DCL**.
- Su utilidad depende del tipo de búsqueda, columnas, cantidad de datos y **selectividad**.

### 6.1 Tipos
| Tipo | Funcionamiento | Uso recomendado |
|---|---|---|
| **B-Tree** | Organiza los datos en ramas (árbol invertido) | Igualdad, **rangos** y ordenación (el más común) |
| **Hash** | Asigna una ubicación directa | Coincidencias **exactas** |
| **Full-Text** | Cataloga palabras de un texto | Buscar términos en **textos largos** |

### 6.2 Simples y compuestos
| Tipo | Columnas | Ejemplo |
|---|---|---|
| **Simple** | Una | Buscar solo por correo |
| **Compuesto** | Dos o más | Buscar por apellido y ciudad |

- En el compuesto, **el orden de las columnas importa**: suele ir primero la de **mayor selectividad** si se usa como filtro principal.
- **Selectividad:** capacidad de los datos para descartar opciones y localizar resultados únicos (teléfono > país).
- El orden ideal depende también de cómo se escriban las consultas más frecuentes.

### 6.3 Ventajas e inconvenientes
| Aspecto | Ventaja | Inconveniente |
|---|---|---|
| Lectura | Menos lecturas en consultas compatibles | Ocupan **espacio extra** |
| Escritura | — | Cada alta/modificación **actualiza el índice** → más tiempo y mantenimiento |

💡 Aceleran búsquedas pero ralentizan el guardado. **No sustituyen** un buen diseño de tablas, claves, consultas y relaciones.

---

## 7. El valor NULL

### 7.1 Semántica
- **NULL** = marca de **falta de información**. No es 0, ni `FALSE`, ni cadena vacía (texto vacío **sí es un valor**).
- No sustituir un desconocido por 0, FALSE o ''.

| Situación | Significado real | Ejemplo |
|---|---|---|
| Ausencia de valor | El dato **no existe** | No tener teléfono fijo |
| Valor desconocido | Existe, pero se **ignora** | Olvidar el número |

(También: dato aún no registrado o que no aplica.)

### 7.2 Lógica ternaria
SQL usa **tres** resultados: **verdadero, falso y desconocido**.

| Situación | Resultado |
|---|---|
| El dato coincide con la búsqueda | Verdadero |
| El dato es distinto | Falso |
| Un valor es nulo | **Desconocido** |

### 7.3 Predicados y funciones
| Función | Propósito |
|---|---|
| `IS NULL` | Encuentra los campos sin información |
| `IS NOT NULL` | Selecciona las filas que sí tienen dato |
| `COALESCE` | Devuelve el **primer argumento no nulo** (ej. teléfono secundario o "No proporcionado") |
| `NULLIF` | Si los dos valores son **iguales**, devuelve NULL (tratar valores especiales como ausencia) |

---

## 8. Claves foráneas

### 8.1 Concepto
- **FK (restricción FK):** referencia a la **tabla referenciada** (la principal). La PK identifica en su tabla; la FK apunta a la PK de otra.
- Mantiene la **integridad referencial**: bloquea incoherencias (compra de un cliente inexistente).

| Componente | Función | Ejemplo |
|---|---|---|
| Clave primaria | Identificar un elemento de forma exclusiva | El DNI único de una persona |
| Clave foránea | Vincular con la tabla referenciada | El DNI anotado en un contrato de alquiler |

### 8.2 Acciones referenciales
Qué pasa con los registros dependientes al borrar/modificar el referenciado.

| Regla | Efecto | Ejemplo |
|---|---|---|
| `RESTRICT` | **Bloquea** el borrado si existen datos conectados | No borrar un cliente con pedidos |
| `CASCADE` | Elimina/actualiza los datos vinculados **en cadena** (con precaución) | Borrar cliente → se borran sus pedidos |
| `SET NULL` | Mantiene el registro pero **vacía** la conexión | Borrar producto → historial de ventas sin producto |
| `SET DEFAULT` | Sustituye la conexión por un **valor por defecto** | Reasignar a un perfil general |

### 8.3 FK en DDL
- Claves **compuestas:** varias columnas forman un solo identificador.
- **Desactivación temporal de FK:** apaga la comprobación durante una operación controlada.
🧠 Solo en **cargas controladas**, con copias de seguridad y **verificación posterior** de la integridad (el SGBD no comprobará cada referencia mientras esté desactivada).

---

## 9. Vistas

- **Vista:** tabla **virtual** generada a partir de una consulta; muestra datos filtrados o combinados **sin modificar** las tablas base y **sin almacenar datos propios**.
- Ventajas: evitar reescribir consultas complejas, **ocultar columnas sensibles** (contraseñas, datos bancarios, márgenes), mostrar a cada usuario solo lo que necesita.

| Característica | Tabla base | Vista |
|---|---|---|
| Almacenamiento | Guarda los datos en el disco | No guarda datos físicamente |
| Propósito | Almacenar información completa | Mostrar datos específicos |

### 9.2 Gestión (DDL)
| Comando | Acción |
|---|---|
| `CREATE VIEW` | Crear una vista |
| `ALTER VIEW` | Modificar una vista existente |
| `DROP VIEW` | Borrarla |

**`WITH CHECK OPTION`:** al crear la vista, comprueba que cualquier cambio hecho **a través de la vista** siga cumpliendo la condición de la consulta original. Solo tiene sentido en vistas **actualizables**.

### 9.3 Tipos de vistas
| Vista | ¿Modificable? | Almacenamiento | Detalle |
|---|---|---|---|
| **Actualizable** | Sí | Virtual | Los cambios van a la tabla original |
| **No actualizable** | No | Virtual | Solo consulta. Pasa cuando hay agrupaciones, agregaciones, uniones complejas, `DISTINCT` o cálculos que impiden saber qué fila modificar |
| **Materializada** | Generalmente no | **Físico** | Guarda una copia física del resultado → lectura rápida, pero puede quedar **desactualizada** si no se **refresca**; varía según SGBD |

---

## 10. Usuarios y privilegios

### 10.1 Gestión de usuarios
- Crear, modificar y eliminar cuentas (según SGBD: `CREATE USER`, `ALTER USER`, `DROP USER`).
- Herramientas gráficas: **pgAdmin, MySQL Workbench, phpMyAdmin**.
- A veces es más seguro **desactivar** una cuenta que eliminarla (se conserva el historial de auditoría).
- **Autenticación:** comprobar usuario y contraseña antes de dejar entrar. Entornos profesionales: políticas de contraseñas, autenticación externa, certificados, **autenticación multifactor**.

### 10.2 Privilegios
| Tipo | Alcance | Ejemplo |
|---|---|---|
| **De sistema** | Permisos muy amplios | Crear usuarios, cambiar la configuración |
| **De objeto** | Acciones concretas sobre un objeto | Leer o modificar una tabla |

- `GRANT` = dar permisos · `REVOKE` = quitarlos.
- **Granularidad:** exactitud con la que se ajustan las tareas de cada usuario.
- Las vistas permiten consultar datos específicos sin dar acceso a toda la tabla.

### 10.3 Roles
- **Rol:** agrupa varios privilegios bajo un mismo perfil/nombre, que se asigna a uno o varios usuarios.
- Si cambia el puesto, basta con cambiar el rol → permisos actualizados.

| Característica | Individual | Por roles |
|---|---|---|
| Tiempo | Elevado | Reducido |
| Riesgo de errores | Mayor | Menor |
| Ante cambios | Más ajustes | Se centraliza en el rol |

---

## 11. DDL (Lenguaje de Definición de Datos)

Define, cambia y borra **estructuras** (no cambia los datos de las filas como hacen `INSERT`/`UPDATE`/`DELETE`).

### 11.1 Tablas
| Comando | Función |
|---|---|
| `CREATE TABLE` | Crear una tabla nueva |
| `ALTER TABLE` | Modificar la estructura (añadir/quitar columnas, restricciones, tipos, relaciones) sin perder los datos; puede estar limitado si ya hay datos |
| `DROP TABLE` | Borrar la tabla **y todo su contenido** (con mucha precaución) |

```sql
CREATE TABLE cliente (
  id_cliente INT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  email VARCHAR(150) UNIQUE
);

ALTER TABLE cliente ADD COLUMN telefono VARCHAR(20);
DROP TABLE cliente;
```

### 11.2 Otros objetos
| Comando | Función |
|---|---|
| `CREATE SCHEMA` | Agrupar objetos de la BD |
| `CREATE VIEW` | Mostrar el resultado de una consulta (tabla virtual) |
| `CREATE INDEX` | Acelerar búsquedas (ocupa espacio y se actualiza con los cambios) |

🧠 Las vistas no almacenan datos: solo guardan la consulta.

### 11.3 Restricciones con DDL
- Restricciones habituales: `PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `UNIQUE`, `CHECK`.
- Se pueden **añadir, modificar o eliminar** en tablas existentes sin borrar sus datos.
- Si la tabla ya tiene datos, el SGBD **comprueba que los cumplen** antes de aceptar la restricción → conviene revisar los datos antes.

| Acción | Función | Ejemplo |
|---|---|---|
| Añadir | Crear obligación | Exigir mayoría de edad |
| Modificar | Cambiar norma | Ajustar código postal |
| Eliminar | Quitar regla | Hacer un campo opcional |

---

## 12. DCL (Lenguaje de Control de Datos)

Administra permisos de usuarios, roles y objetos.

### 12.1 `GRANT`
```sql
GRANT SELECT, INSERT ON cliente TO usuario_lectura;
GRANT SELECT ON cliente TO usuario_lectura WITH GRANT OPTION;
```
- Se indica: privilegio, objeto afectado y usuario/rol receptor.
- También vale para vistas, índices, etc.
- **`WITH GRANT OPTION`:** el receptor puede **conceder esos permisos a otros** (delegar). Requiere control.

### 12.2 `REVOKE`
```sql
REVOKE INSERT ON cliente FROM usuario_lectura;
REVOKE SELECT ON cliente FROM usuario_lectura RESTRICT;
REVOKE SELECT ON cliente FROM usuario_lectura CASCADE;
```
| Opción | Comportamiento |
|---|---|
| `RESTRICT` | Frena la retirada si el permiso se ha compartido con otros |
| `CASCADE` | Quita el permiso al usuario y a todos los que lo recibieron de él (permisos derivados; según SGBD) |

### 12.3 Auditoría y buenas prácticas
- La auditoría **complementa** los permisos (no los sustituye): deja **trazabilidad** de quién hizo qué y cuándo.
- Registrar: **inicios de sesión, cambios de permisos, modificaciones de datos sensibles, errores de acceso e intentos fallidos de autenticación**.
- Proteger los propios registros (contienen información sensible).
- Buenas prácticas:
  - Revisar periódicamente usuarios, roles y privilegios.
  - **Principio de mínimo privilegio.**
  - Separar cuentas personales y cuentas de aplicación.
  - **No compartir usuarios** entre varias personas.

---

## Glosario rápido
| Término | Definición |
|---|---|
| Atributo | Columna (propiedad de los datos) |
| Clave artificial | PK generada por el sistema (autoincremento, UUID) |
| Clave candidata | Mínima combinación que identifica filas; puede ser PK |
| DCL / DDL | Control de permisos / definición de estructuras |
| Dominio | Valores válidos de un atributo |
| Esquema | Estructura y reglas, sin datos |
| Índice | Estructura para encontrar datos sin revisar todos los registros |
| Instancia | Datos almacenados en un momento concreto |
| Privilegio | Derecho a realizar operaciones sobre objetos |
| Relación / Tupla | Tabla / fila |
| Rol | Conjunto de privilegios con un nombre |
| Superclave | Identifica filas, aunque lleve información de más |
| Valor NULL | Ausencia de información o dato desconocido |
| Vista | Tabla virtual a partir de una consulta |

---

## ✅ Chuleta final
- Relación = tabla · Tupla = fila · Atributo = columna · Dominio = valores válidos.
- Esquema = estructura (fija) · Instancia = datos de un momento (cambia).
- Superclave ⊇ clave candidata → **una** se elige como PK (única, no nula, estable).
- Surrogate: autoincremento (centralizado) · UUID (distribuido).
- `NOT NULL` = obligatorio · `UNIQUE` = sin repetidos (NULL según SGBD) · `CHECK` = condición (**probar en tu SGBD**).
- Índice: acelera lectura, frena escritura. B-Tree = rangos · Hash = exacto · Full-Text = texto.
- NULL: lógica ternaria → `IS NULL`, `COALESCE`, `NULLIF`.
- FK: `RESTRICT` / `CASCADE` / `SET NULL` / `SET DEFAULT`.
- Vistas: virtuales, `WITH CHECK OPTION`; materializadas = copia física que hay que refrescar.
- `GRANT` (+ `WITH GRANT OPTION`) / `REVOKE` (`CASCADE` o `RESTRICT`). Roles = permisos en paquete. Mínimo privilegio.
- DDL: `CREATE` / `ALTER` / `DROP`. `DROP TABLE` borra estructura **y** datos.
