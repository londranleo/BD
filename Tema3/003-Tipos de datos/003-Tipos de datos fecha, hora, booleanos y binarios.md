- **Booleanos**: solo verdadero o falso y ocupan poca memoria. Ejemplos: "activo", "verificado", "publicado", "acepta_notificaciones".
- **Fecha y hora**: registran el momento exacto de un evento. Permiten ordenar ventas o buscar una factura por fecha. Se puede guardar solo la fecha, solo la hora o fecha y hora juntas. En aplicaciones web hay que tener en cuenta la **zona horaria**.
- **Binarios**: archivos que no son texto, como fotos o documentos. Se usan tipos como **BLOB** o **BYTEA**. En aplicaciones web suele ser mejor guardar el archivo fuera y conservar en la base de datos solo la ruta, la URL o los metadatos.

>**Recuerda:** usar el tipo adecuado permite ordenar y filtrar sin conversiones. Guardar fechas como texto provoca errores de ordenación, comparación y formato.