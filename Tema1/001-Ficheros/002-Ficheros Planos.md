# 1.1 Ficheros Planos

Archivo de de texto sencillo, información linea a linea, sin estructura compleja.

- Ejemplos: `.txt`, `.csv`, `.log`, `.env` (también `.json` y `.xml`, que ya organizan la información con estructura propia).

Una ventaja es su sencillez, pueden transportase entre sistemas sin SGDB.

Su limitación Aparece cuando crece el volumen de datos, Para encontrar un dato se lee todo hasta encontrar el dato. 

Si se guardan datos personales, hay que aplicar protección de datos correespodiente.

| Característica        | Fichero plano | Base de datos                                 |
| --------------------- | ------------- | --------------------------------------------- |
| Complejidad de uso    | Baja          | Media o alta                                  |
| Velocidad de búsqueda | Lenta         | Menos recorrido en búsquedas estructuradas    |
| Seguridad             | Básica        | Control de accesos, restricciones y auditoría |
