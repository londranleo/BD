# UD6 · Interpretación de Diagramas Entidad/Relación

> Representar la información con un **modelo visual (E/R)** antes de crear las tablas, y luego pasarlo al **modelo relacional**, con reglas de integridad y **normalización**.

## Índice
1. El modelo E/R: entidades, relaciones, cardinalidades, debilidad
2. El modelo E/R ampliado: generalización, especialización, agregación
3. Del diagrama E/R al modelo relacional
4. Restricciones semánticas
5. Normalización

---

## 1. El modelo E/R

### 1.1 Entidades y atributos
- **Entidad:** elemento real sobre el que se guarda información (ej. `Alumno`).
- **Atributo:** detalle que describe la entidad (nombre, DNI, dirección, teléfono, fecha de nacimiento).
- En los esquemas, la entidad va en el centro y los atributos alrededor.

| Tipo | Característica | Ejemplo |
|---|---|---|
| Simple | No se puede dividir | Documento de identidad |
| Compuesto | Se puede dividir en partes con significado | Dirección postal (calle, número, CP, ciudad) |
| Multivaluado | Acepta varios valores | Teléfonos |
| Derivado | El sistema lo calcula | Edad (a partir de la fecha de nacimiento) |

- Derivado: almacenar el **dato base** (fecha de nacimiento) y calcular el derivado (edad) cuando haga falta.
- Multivaluado: suele modelarse en una **tabla aparte**.

### 1.2 Relaciones: grado, cardinalidad y participación
- **Grado:** nº de entidades que participan. Lo habitual: **binarias** (entre dos entidades).
- **Cardinalidad:** cuántas ocurrencias de una entidad se asocian con las de otra → `1:1`, `1:N`, `M:N`.

| Cardinalidad | Ejemplo |
|---|---|
| 1:1 | Un vehículo ↔ una matrícula (la FK lleva `UNIQUE` si es necesario) |
| 1:N | Un comprador realiza muchos pedidos; cada pedido es de un solo comprador |
| M:N | Alumnos ↔ asignaturas (muchos a muchos) |

**Participación** (obligatoria u opcional):
| Tipo | ¿Obligatoria? | Ejemplo |
|---|---|---|
| Total | Sí | Todo pedido debe estar asociado a un cliente |
| Parcial | No | Empleado y vehículo |

💡 Cardinalidad = **cuántas** ocurrencias. Participación = si la relación es **obligatoria u opcional**.
💡 Participación total → **línea doble** en el diagrama.

### 1.3 Entidades débiles
- Entidad que **no tiene sentido por sí sola** y depende de una **entidad fuerte**.
- **Dependencia de existencia:** no puede existir sin la fuerte (movimiento bancario → cuenta bancaria).
- **Dependencia de identificación:** no tiene clave propia completa; combina la **clave de la fuerte + un discriminador**.
- **Discriminador:** dato adicional que diferencia registros débiles de la misma entidad fuerte (nº de movimiento dentro de una cuenta).

| Característica | Entidad fuerte | Entidad débil |
|---|---|---|
| Existencia | Independiente | Depende de otra |
| Identificación | Clave propia | Clave de la fuerte + discriminador |

💡 Si se elimina la fuerte hay que definir qué pasa con las débiles: **borrado en cascada** o **impedir el borrado** mientras existan registros asociados.

---

## 2. El modelo E/R ampliado

Se usa cuando el sistema es más complejo. Añade **jerarquías** (generalización/especialización) y **agregación**.

### 2.1 Generalización y especialización
| Concepto | Dirección | Resultado | Ejemplo |
|---|---|---|---|
| **Generalización** | De abajo hacia arriba | Agrupa entidades en categorías amplias | Coche + Motocicleta + Furgoneta → Vehículo |
| **Especialización** | De arriba hacia abajo | Divide un grupo en subcategorías | Empleado → Programador, Diseñador |

- **Herencia:** los subtipos heredan los atributos comunes del tipo general y añaden los suyos. Reduce redundancia.
- Ejemplo: `Vehículo` (matrícula, marca, color, modelo) → `Coche`, `Moto`, `Furgoneta` con atributos propios.

**Reglas de la jerarquía:**
| Regla | Pregunta que responde | Valores |
|---|---|---|
| **Solapamiento** | ¿Un elemento puede pertenecer a varios subtipos a la vez? | Sí (ej. persona Profesor y Estudiante) / No |
| **Cobertura** | ¿Todo elemento del supertipo debe estar en algún subtipo? | **Total** (todos) / **Parcial** (algunos solo en el supertipo) |

La jerarquía se transforma luego en tablas con: tabla única, tabla por tipo o tabla por hoja (ver 3.3).

### 2.2 Agregación
- Trata una **relación** (o conjunto de elementos relacionados) como si fuera una **entidad de nivel superior**.
- Se usa cuando una relación binaria no describe bien la situación sin perder relaciones relevantes.
- Ejemplo: Médico + Paciente → **Consulta**; esa consulta se relaciona con los **Medicamentos** recetados.
- También representa un **todo formado por partes** (Motor, Ruedas, Asientos → Coche).
- **Composición:** caso particular de agregación con dependencia de existencia más fuerte; las partes no existen ni tienen sentido sin el todo.
- Sirve para simplificar diseños complejos: menos relaciones directas y menos ambigüedad.

| Concepto | Qué hace | Ejemplo |
|---|---|---|
| Agregación | Trata una relación como unidad superior | Consulta médico-paciente ↔ medicamentos |
| Generalización | Agrupa entidades por características comunes | Coche y moto → vehículo |

(La agregación **no clasifica tipos**: une elementos o relaciones en una estructura superior.)

### 2.3 Básico vs. ampliado y notaciones
| Modelo | Elementos | Uso habitual |
|---|---|---|
| E/R básico | Entidades, atributos, relaciones, cardinalidades | Diseños simples o directos |
| E/R ampliado | Jerarquías, herencia, generalización, especialización, agregación | Diseños complejos con categorías, subtipos o relaciones avanzadas |

🧠 **Notación de pata de gallo:** representa las cardinalidades con símbolos gráficos en los extremos de las relaciones (ej. 1 → N: una barra en el lado "uno" y la "pata" en el lado "muchos").

---

## 3. Del diagrama E/R al modelo relacional

Idea general:
- Cada **entidad** (fuerte o débil) → una **tabla**.
- Las **relaciones** 1:1, 1:N, M:N → claves foráneas o tablas nuevas.
- Se aplican **restricciones** para evitar huérfanos, inconsistencias y referencias inválidas (la FK solo apunta a valores que existen).
- Las **jerarquías** se convierten con 3 estrategias.

### 3.1 Entidades fuertes y débiles → tablas
| Categoría | Estructura final |
|---|---|
| Entidad fuerte | Tabla con **clave primaria propia** |
| Entidad débil | Tabla dependiente con **FK** a la fuerte y, normalmente, **PK compuesta** = clave de la fuerte + discriminador |

Ejemplo: `Libros(id_libro PK, titulo, autor)` y `Capitulos(id_libro FK, numero, titulo)` con PK (`id_libro`, `numero`).

### 3.2 Relaciones → tablas / claves foráneas
| Tipo | Ejemplo | Solución |
|---|---|---|
| 1:1 | Ciudadano y pasaporte | FK en una de las tablas, normalmente con `UNIQUE` |
| 1:N | Cliente y recibos | FK en la tabla del lado **"muchos"** (Recibos) |
| M:N | Alumnos y cursos | **Tabla intermedia nueva** (ej. Matrículas) con las PK de ambas |

- En 1:1 se elige dónde poner la FK según qué entidad **dependa más** de la otra o cuál tenga **participación total**.
- Si la relación tiene **atributos propios** (fecha de matrícula, importe), van en la **tabla intermedia**.
- Se aplican restricciones para que las FK apunten a registros existentes (no comprar un producto que no existe en el catálogo).

### 3.3 Jerarquías de especialización → 3 estrategias
| Estrategia | Organización |
|---|---|
| **Tabla única** | Todo (general + específico) en una sola tabla grande. Suele llevar una columna de tipo (ej. `tipo_vehiculo`). Quedan celdas vacías |
| **Por tipo** | Una tabla general (atributos comunes) + una tabla por cada subtipo (datos particulares) |
| **Por hoja** | Se elimina la tabla general; solo tablas para los subtipos finales, cada una con atributos comunes + específicos |

Ejemplo: tabla `Vehiculos` (matrícula, marca, color) + `Coches`, `Motocicletas`, `Furgonetas` = estrategia **por tipo**.

💡 Elegir estrategia según: cantidad de información compartida, atributos específicos de cada subtipo y consultas que hará el sistema.

---

## 4. Restricciones semánticas

- Son reglas que actúan como **filtro automático**: revisan cada dato antes de guardarlo (ej. impedir una edad negativa).
- Definirlas en la BD hace que el sistema rechace lo incorrecto y evita comprobaciones duplicadas en las aplicaciones.
- Tres niveles de control:

| Nivel | Qué asegura |
|---|---|
| Integridad de entidad | Cada registro tiene PK única y no nula |
| Integridad referencial | Las FK apuntan a registros válidos |
| Restricciones de usuario | Reglas de negocio propias (CHECK, triggers) |

### 4.1 Integridad de entidad
- Cada registro se identifica de forma única con una **clave primaria**.
- Dos condiciones obligatorias:
  - La PK **no puede repetirse**.
  - La PK **no puede ser nula/vacía**.
- Si se repite, no se distinguen dos registros; si está vacía, no hay identificación válida.
- En muchas BD se prefiere un **identificador interno generado por el sistema**.

| Cliente | Clave | Estado |
|---|---|---|
| Ana | 123 | ✅ Correcta (única y con valor) |
| Luis | Vacío | ❌ Nula o vacía |
| Pedro | 123 | ❌ Repetida |

### 4.2 Integridad referencial
- Una **FK** es un campo que apunta a la PK de otra tabla (ej. `ventas.id_cliente` → `clientes`).
- Asegura que las FK hagan referencia a **datos reales**: no hay venta para el cliente 100 si no existe.
- Evita **registros huérfanos**.
- Permite definir qué pasa al borrar/modificar un registro relacionado: **bloquear**, **actualizar en cascada** o **dejar el campo nulo**.

```sql
CREATE TABLE ventas (
  id_venta INT PRIMARY KEY,
  id_cliente INT NOT NULL,
  fecha DATE,
  importe DECIMAL(10,2),
  FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente)
);
```

### 4.3 Restricciones de usuario (reglas de negocio)
- **Regla de negocio:** norma que hace que los datos tengan lógica en el mundo real.
- Herramientas:

| Herramienta | Qué hace | Ejemplo |
|---|---|---|
| **Constraint / `CHECK`** | Valida que el valor cumpla una condición | Precio > 0 |
| **Trigger (disparador)** | Rutina automática ante INSERT/UPDATE/DELETE en una tabla; para reglas más complejas, auditorías o validaciones | Registrar quién cambió un dato y cuándo |

```sql
CREATE TABLE producto (
  id_producto INT PRIMARY KEY,
  nombre VARCHAR(100),
  precio DECIMAL(10,2) CHECK (precio > 0)
);

-- Trigger simplificado de auditoría
CREATE TRIGGER auditoria_precio
AFTER UPDATE ON producto
FOR EACH ROW
INSERT INTO auditoria (tabla, id_registro, fecha)
VALUES ('producto', OLD.id_producto, NOW());
```
💡 Los constraints bloquean automáticamente cualquier dato que no cumpla las condiciones.
🧠 Un trigger puede registrar cambios en una tabla de auditoría (quién y cuándo) si se programa para ello.

---

## 5. Normalización

- **Normalización:** organiza las tablas para **reducir redundancias** y evitar **anomalías** al insertar, actualizar o borrar.
- Cada dato se guarda en **un único lugar**.
- Si es demasiado estricta puede afectar a la velocidad → **desnormalización controlada**.

### 5.1 Dependencias funcionales
- **Dependencia funcional:** un dato permite identificar o conocer otros de forma directa (DNI → nombre, apellidos, fecha de nacimiento).

| Término | Significado | Ejemplo |
|---|---|---|
| **Determinante** | Atributo (o conjunto) que determina otros | DNI |
| **Atributo dependiente** | Lo que se descubre gracias al determinante | Apellidos, fecha de nacimiento |
| **Cierre de atributos** | Conjunto de todos los atributos que se pueden deducir a partir de uno o varios iniciales | Cierre de DNI = {Nombre, Apellidos, Fecha_Nacimiento} |

- Comprender las dependencias permite **identificar claves candidatas** y estructurar mejor las tablas.
- Se suele preferir un **identificador interno estable** que localice cada registro sin ambigüedad.

### 5.2 Formas normales 1FN, 2FN, 3FN
Son reglas **progresivas**; cada una corrige un tipo de problema.

| Regla | Exige | Ejemplo / objetivo |
|---|---|---|
| **1FN** | Valores **atómicos**: un solo dato por celda, sin listas | No guardar varios teléfonos separados por comas |
| **2FN** | (Cumple 1FN) **Dependencia completa** de la clave primaria (sin dependencias parciales); importa con PK compuesta | Cada atributo depende de toda la PK |
| **3FN** | (Cumple 2FN) **Sin dependencias transitivas** entre atributos no clave | Un atributo no clave no depende de otro no clave |

**Ejemplo paso a paso:**

*Tabla no normalizada:* `pedidos_sin_normalizar(id_pedido, cliente, ciudad_cliente, productos)`
→ `productos` guarda "Teclado, Ratón" en una celda → incumple 1FN.

*Paso 1 – 1FN:* una fila por producto:
`pedidos_1fn(id_pedido, cliente, ciudad_cliente, producto, precio_producto)`
PK compuesta (`id_pedido`, `producto`). Problema: `cliente` y `ciudad_cliente` dependen solo de `id_pedido`; `precio_producto` solo de `producto` → **dependencia parcial**.

*Paso 2 – 2FN:* se divide en tres tablas:
- `pedidos_2fn(id_pedido, cliente, ciudad_cliente)`
- `productos_2fn(producto, precio_producto)`
- `detalle_pedido_2fn(id_pedido, producto)` ← tabla intermedia con FKs

*Paso 3 – 3FN:* en `pedidos_2fn` persiste una **dependencia transitiva**: `id_pedido → cliente → ciudad_cliente`. Se separa:
- `pedidos_3fn(id_pedido, cliente)`
- `clientes_3fn(cliente, ciudad_cliente)`

→ Cada atributo no clave depende únicamente de la PK de su tabla.

### 5.3 FNBC, 4FN y desnormalización
| Forma | Qué exige | Cuándo |
|---|---|---|
| **FNBC** (Boyce-Codd) | Versión más estricta de 3FN: **todo determinante es clave candidata** | Cuando 3FN no basta |
| **4FN** | (Cumple FNBC) sin **dependencias multivaluadas** independientes entre sí | Tabla que mezcla varios conjuntos de datos independientes |

**Desnormalización controlada:** introducir cierta **redundancia** (o datos calculados) a propósito para reducir uniones (JOIN) en consultas concretas y mejorar rendimiento.

| Característica | Normalización avanzada | Desnormalización controlada |
|---|---|---|
| Objetivo | Reduce redundancias y dependencias problemáticas | Reduce uniones en consultas concretas con redundancia controlada |
| Estructura | Puede aumentar nº de tablas o relaciones | Requiere controles para evitar inconsistencias |

🧠 Las formas normales reducen redundancias y anomalías, pero hay que equilibrarlas con las necesidades reales de consulta y rendimiento.
(Coste de desnormalizar: más almacenamiento a cambio de menos uniones.)

---

## Glosario rápido
| Término | Definición |
|---|---|
| Anomalía | Fallo al actualizar/borrar datos en una tabla desorganizada |
| Atributo compuesto / derivado / multivaluado / simple | Ver tabla 1.1 |
| CHECK | Restricción que valida una condición antes de aceptar un valor |
| Cardinalidad | Nº de ocurrencias que se asocian: 1:1, 1:N, M:N |
| Clave candidata | Atributo(s) mínimo(s) que identifican cada registro; de ellas se elige la PK |
| Clave foránea | Campo que apunta a la PK de otra tabla |
| Clave primaria | Dato único que identifica cada registro |
| Cobertura | ¿Todo elemento del supertipo está en algún subtipo? (total/parcial) |
| Composición | Agregación con dependencia de existencia fuerte entre partes y todo |
| Desnormalización | Redundancia controlada para reducir uniones |
| Determinante | Atributo(s) del que dependen otros |
| Discriminador | Atributo que, con la clave de la fuerte, identifica a la entidad débil |
| Entidad débil / fuerte | Depende de otra para identificarse / existe de forma independiente |
| Especialización / Generalización | Dividir un supertipo en subtipos / agrupar entidades en un supertipo |
| Participación total / parcial | Relación obligatoria (línea doble) / opcional |
| Solapamiento | ¿Un elemento puede estar en varios subtipos a la vez? |
| Trigger | Rutina automática ante un evento sobre una tabla |

---

## ✅ Chuleta final
- Entidad → tabla. Entidad débil → FK + PK compuesta (clave fuerte + discriminador).
- 1:1 → FK + `UNIQUE` · 1:N → FK en el lado "muchos" · M:N → tabla intermedia.
- Jerarquía → tabla única / por tipo / por hoja.
- PK: **única y no nula**. FK: apunta a un registro **que existe**.
- `CHECK` para reglas simples, **trigger** para las complejas.
- 1FN = atómico · 2FN = dependencia completa de la PK · 3FN = sin transitivas · FNBC = todo determinante es clave candidata · 4FN = sin multivaluadas independientes.
