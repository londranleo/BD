Son formas de organizar entidades en una base de datos. 

# Especialización 

Consiste en coger una entidad y dividirla en entidades mas especificas. 

Ejemplo tenemos una `Persona`, que se divide en `Alumno`y `Profesor`. 

![[002.png|700]]

# Generalización 

Es Justo lo contrario. juntar varias entidades específicas para crear una entidad más general. 

Ejemplo tenemos `Choche`y `Moto`, Creamos una entidad general llamada `Vehículo`. 

![[003.png|700]]

Herencia Una entidad hija recibe los atributos de un entidad padre. 

![[004.png|700]]

las entidades hijas heredan todos los atributos de la entidad padre y además pueden tener atributos propios.

**Reglas de la jerarquía:**

| Regla            | Pregunta que responde                                     | Valores                                                        |
| ---------------- | --------------------------------------------------------- | -------------------------------------------------------------- |
| **Solapamiento** | ¿Un elemento puede pertenecer a varios subtipos a la vez? | Sí (ej. persona Profesor y Estudiante) / No                    |
| **Cobertura**    | ¿Todo elemento del supertipo debe estar en algún subtipo? | **Total** (todos) / **Parcial** (algunos solo en el supertipo) |

La jerarquía se transforma luego en tablas con: tabla única, tabla por tipo o tabla por hoja.