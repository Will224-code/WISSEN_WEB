# 03 · Alcance del proyecto

## 1. Alcance general
Aplicación web (React, con posibilidad de PWA) donde estudiantes de la UMT
con un emprendimiento publican sus productos o servicios, y la comunidad
estudiantil los consulta y contacta a los vendedores.

Roles: Visitante, Usuario registrado (Cliente), Vendedor y Administrador
(ver [usuarios](04-usuarios.md)).

## 2. Incluye (núcleo)

### Vistas (mínimo 7, interrelacionadas)
1. Inicio / catálogo general
2. Detalle de producto o servicio
3. Login
4. Registro (todos se registran como usuario; se pasa a vendedor al crear un negocio; la cuenta de administrador se da de alta en la base de datos)
5. Panel del vendedor (publicaciones, configuración y reportes del negocio)
6. Perfil público del negocio (con sus publicaciones) y listado de negocios
7. Panel del usuario
8. Panel del administrador (publicaciones, negocios, usuarios, categorías, administradores, bitácora y reportes)

### Funcionalidades (primer borrador)
- Registro e inicio de sesión con matrícula y contraseña; el sistema identifica el rol de la cuenta y aplica el control de acceso
- Catálogo general visible para cualquier visitante, con buscador y filtros por categoría y por tipo (Producto / Servicio); el listado de negocios tiene buscador y filtro por categoría. Los filtros muestran cuántos resultados hay, se pueden limpiar y se conservan al volver desde un detalle
- Detalle de cada publicación
- Panel del vendedor: crear, editar y eliminar publicaciones
- Calificar (una calificación por usuario, que puede cambiar) y comentar las publicaciones; el visitante solo los lee y, al intentarlo, se le invita a registrarse
- El contacto es solo por WhatsApp: se usa el teléfono del negocio y al pulsarlo se abre WhatsApp
- El contacto del vendedor se ve en el detalle de la publicación, también para el visitante
- El administrador inicia sesión como cualquier usuario y ve el catálogo normal; el botón «Ver panel de administrador» abre su panel en pantalla completa y «Salir del panel» regresa al catálogo
- En el panel, el administrador ve todas las publicaciones y negocios (con buscador y filtros) y da de baja usuarios, negocios, comentarios y publicaciones; los demás usuarios no. Las publicaciones y los negocios se dan de baja desde su detalle o perfil, no desde la lista
- El administrador agrega, edita y elimina categorías de negocio; al editar, el nombre cambia también en los negocios que la usan; al eliminar, esos negocios pasan a «Otro» (que no se puede editar ni eliminar)
- El administrador agrega más cuentas de administrador (hijos) y puede darlas de baja; el administrador maestro nunca se elimina
- Cada baja es permanente y queda registrada en una bitácora
- Cada comentario muestra su fecha de publicación y se pueden ordenar por «Más recientes» (inicial) o «Más antiguos»
- Reportes por mes para el dueño de cada negocio, en la pestaña «Reportes» de su panel: vista general (vistas, clics en WhatsApp, calificación promedio y comentarios nuevos, con las publicaciones que más clics tuvieron) y vista por publicación (tabla y detalle día por día). El administrador tiene la pestaña «Reportes» con los mismos datos de todo el sistema, filtrados por negocio y por mes
- Cada calificación guarda su fecha, para que los reportes muestren el promedio de las calificaciones de cada mes
- Qué se mide: vistas del detalle y clics en el teléfono (WhatsApp). Solo se cuenta cuántas veces, no quién; las vistas del propio dueño no cuentan. No se miden ventas ni pedidos porque la compra se coordina por WhatsApp fuera de la aplicación

### Frontend
- Formularios con controles variados: texto, contraseña, radiobotones
  (tipo producto/servicio), listas desplegables (categoría) y áreas de texto
- Hojas de estilo CSS y archivos JavaScript organizados por componentes
- El [prototipo](../05-vista/boceto-refinado/boceto-refinado.html) es solo una maqueta visual (HTML sin servidor) para validar pantallas y flujos. La aplicación real se construye en React y debe cumplir lo que dicen estos documentos aunque la maqueta no lo muestre.
- Controles que la aplicación debe incluir (confirmados):
  - Radiobotones «Producto / Servicio» en Agregar y Modificar publicación (pantallas 10 y 15); obligatorio, con «Producto» marcado por defecto
  - Buscador y filtros (categoría y tipo) en el catálogo y el listado de negocios; en el panel del administrador, buscador en todas las listas
  - Menú del catálogo con solo «Catálogo» y «Negocios»; acceso al perfil rotulado «Mi perfil»
  - Títulos con frase de apoyo y ruta de navegación en detalles y perfiles
  - No se incluyen la casilla de términos ni «publicación activa»

### Base de datos
Usuarios (con rol), negocios, publicaciones, categorías y datos de contacto.

## 3. Incluye si alcanza el tiempo (deseable)
- Carrito de compras con manejo de sesiones y registro de pedidos
- Reportes de ventas en dinero y pedidos, y descargar o exportar los reportes (solo se consultan en pantalla)
- PWA (instalable)

## 4. No incluye (esta versión)
- Pagos en línea
- Chat interno entre comprador y vendedor
- Favoritos / lista de deseos
- Búsqueda avanzada (se incluye solo el buscador simple con filtros por categoría y tipo)
- Moderación automática (la moderación es manual, a cargo del administrador)

## 5. Limitaciones
- Solo estudiantes de la UMT; no hay verificación de identidad en esta versión (propuesta rechazada por el equipo en el [ADR-005](../03-arquitectura/decisiones/ADR-005-verificacion-de-identidad.md))
- La compra se coordina directamente entre cliente y vendedor, fuera de la página
- Proyecto académico con fecha de entrega fija (2 de diciembre de 2026)