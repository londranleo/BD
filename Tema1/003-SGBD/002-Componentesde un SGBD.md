# 3.2 Componentes de un SGBD

los SGBD dividen sus tareas entre distintos componentes para gestionar y proteger la información.

Para funcionar bien el sistema necesita varios componentes:

| Componente               | Función principal                                                                                          | Ejemplo                           |
| ------------------------ | ---------------------------------------------------------------------------------------------------------- | --------------------------------- |
| **Motor**                | Ejecuta consultas, gestiona transacciones, coordina el acceso físico a los datos (guarda y busca en disco) | Ejecución de una consulta SQL     |
| **Diccionario de datos** | Catálogo interno: tablas, campos, tipos, relaciones, restricciones, permisos (metadatos)                   | Catálogo interno del SGBD         |
| **Interfaz de usuario**  | Permite administrar y consultar sin programar                                                              | Consola SQL o herramienta gráfica |

Muchos SGBD ofrecen una consola de comando, conectores para lenguajes de programación y herramientas de administracion para crear copias de seguridad, revisar el rendimiento o gestionar usuarios.



