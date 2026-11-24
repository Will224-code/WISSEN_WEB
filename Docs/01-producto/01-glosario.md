# 01 ·  Glosario

Este documento explica a qué nos referimos con ciertas palabras en la documentación, para evitar equivocaciones o malentendidos.

Los términos están organizados por categoría.

---

## Negocio

* **Emprendimiento**: Negocio de un estudiante de la UMT que ofrece un producto o servicio.

* **Producto**: Objeto, alimento o cualquier cosa tangible que un estudiante de la UMT con un emprendimiento ofrece para vender.

* **Servicio**: Atención o acción que un estudiante de la UMT con un emprendimiento ofrece a otras personas.

* **Negocio**: Registro de un emprendimiento dentro del sistema, creado por un Usuario registrado. Un usuario puede tener varios negocios y cada uno agrupa sus publicaciones.

* **Dar de baja**: Acción del Administrador que elimina de forma permanente un usuario, negocio, comentario, publicación, categoría o administrador hijo. No se puede recuperar, pero queda registro de qué se eliminó.

---

## Usuarios y roles

* **Visitante**: Usuario no registrado que solo entra a observar publicaciones y catálogos de los emprendimientos.

* **Usuario registrado**: Usuario con cuenta en el sistema (inicia sesión con su matrícula), pero sin los accesos de un Usuario Vendedor.

* **Usuario Vendedor**: Usuario registrado que creó al menos un negocio, con acceso para crear publicaciones y promocionar su emprendimiento.

* **Usuario Administrador**: Usuario que cuenta con accesos de alto nivel: ve todas las publicaciones, da de baja usuarios, negocios, comentarios o publicaciones de cualquier usuario, agrega, edita y elimina categorías, consulta los reportes de todo el sistema y agrega otras cuentas de administrador, con el objetivo de controlar el tráfico de publicaciones. Su cuenta no se crea desde el registro: la del administrador maestro se da de alta en la base de datos y las demás las otorga un administrador.

* **Administrador maestro**: El único Usuario Administrador dado de alta en la base de datos por el equipo. Nunca se elimina, para que el sistema siempre tenga un administrador.

* **Administrador hijo**: Usuario Administrador al que otro administrador le otorgó el rol. Puede ser dado de baja por cualquier administrador.

* **Vendedor**: Estudiante de la UMT con un emprendimiento que publica sus productos o servicios en la página. En el sistema se representa mediante un Usuario Vendedor.

* **Cliente**: Usuario registrado que solicita algún producto o servicio a un Vendedor.

---

## Contenido de la página

* **Publicación**: Ficha creada por un Vendedor para ofrecer un producto o servicio; incluye imagen y descripción detallada.

* **Categoría**: Sección que sirve para organizar y clasificar las publicaciones de productos o servicios en la página. Solo el Administrador puede agregarlas, editarlas o eliminarlas; al eliminar una, sus negocios pasan a la categoría «Otro».

* **Catálogo**: Sección que enlista las publicaciones de productos o servicios. El *catálogo general* reúne las de todos los vendedores; el *perfil público del negocio* reúne solo las de un negocio.

---

## Operaciones

* **Reporte**: Pantalla con los resultados de las publicaciones en un mes: vistas, clics en WhatsApp, calificación promedio y comentarios nuevos. El vendedor ve los de sus negocios y el administrador los de todo el sistema. Solo se consultan en pantalla; no se descargan ni miden ventas.

* **Vista**: Cada vez que una persona abre el detalle de una publicación. Se cuenta cuántas veces, no quién; las del propio dueño no cuentan.

* **Clic en WhatsApp**: Cada vez que una persona pulsa el teléfono del negocio para abrir WhatsApp. Es la medida de cuántos contactos genera una publicación.

* **Bitácora**: Registro de lo que el Administrador da de baja (qué, quién y cuándo). No se edita ni se borra.

* **Sección de contacto**: Acción que un Cliente hace a un vendedor para acceder a su contacto y poder comunicarse con el Vender para solicitar un servicio o producto.

---

## Interfaz

* **Panel de vendedor**: Pantalla exclusiva del Usuario Vendedor desde la cual sube, edita y elimina sus publicaciones de productos o servicios y consulta los reportes de su negocio.

* **Panel de administrador**: Pantalla exclusiva del Usuario Administrador desde la cual ve todas las publicaciones, da de baja contenido y usuarios, gestiona las categorías, consulta los reportes y agrega cuentas de administrador.