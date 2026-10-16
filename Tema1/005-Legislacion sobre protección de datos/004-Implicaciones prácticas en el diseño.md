# 5.3 Implicaciones prácticas en el diseño

Proteger la información reduce el riesgo de accesos no autorizados, pérdidas de datos
y usos indebidos. Las leyes actuales obligan a cuidar los datos personales de los usuarios. 

**Técnicas para proteger los datos:**
- **Cifrado:** convierte la información en un código que solo puede leer quien tiene la clave. Se usa en copias de seguridad, comunicaciones y datos sensibles.
- **Hash con sal:** es la forma correcta de guardar **contraseñas**. Las contraseñas **no** se cifran de forma reversible.
- **Anonimización:** borra o transforma los datos que identifican a alguien, de modo que **ya es imposible** saber de quién son. Es útil para estadísticas.
  - Si combinando otros datos todavía se pudiera saber quién es, se llama **seudonimización**, no anonimización.
- **Auditoría:** es como un **libro de visitas automático** que apunta quién entró, qué hizo y cuándo. Estos registros también hay que protegerlos, porque contienen información sensible.

>La seguridad no depende solo del programa: depende también de cómo diseñas la base de datos, de cómo das los permisos y de cuánto tiempo guardas la información.