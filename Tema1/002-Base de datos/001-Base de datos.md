
# 2.1 Concepto de BD

Es un conjunto de datos Relacionados, alacenadas de forma estructurada para facilitar su consulta, modificacion, proteccion e integridad.

Su objetivo es organizar los datos y permitir que el SGBD La consulte, modifique, proteja y administre.

| Característica | Sistema de ficheros                       | Base de datos                                       |
| -------------- | ----------------------------------------- | --------------------------------------------------- |
| Búsqueda       | Lenta y manual                            | Mediante consultas e índices                        |
| Repetición     | Alta (datos duplicados)                   | Reduce y controla la duplicación                    |
| Seguridad      | Permisos del sistema de archivos          | Permisos, roles y auditoría según el SGBD           |
| Integridad     | La controla la aplicación                 | La puede gestionar el SGBD (reglas y restricciones) |
| Concurrencia   | Problemática si varios modifican a la vez | Controlada con mecanismos internos                  |
>Toda BD con datos personales debe aplicar medidas de seguridad técnicas para
>cumplir el **RGPD**.
