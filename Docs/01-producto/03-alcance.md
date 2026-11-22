# 03 · Alcance del proyecto

## 1. Alcance general
Aplicación web (React, con posibilidad de PWA) donde estudiantes de la UMT
con un emprendimiento publican sus productos o servicios, y la comunidad
estudiantil los consulta y contacta a los vendedores.

Roles: Visitante, Usuario registrado (Cliente) y Vendedor.

## 2. Incluye (núcleo)

### Vistas (mínimo 6, interrelacionadas)
1. Inicio / catálogo general
2. Detalle de producto o servicio
3. Login
4. Registro (tipo de cuenta: usuario o vendedor)
5. Panel del vendedor
6. Página del vendedor (su catálogo)
7. Panel del usuario

### Funcionalidades
- Registro e inicio de sesión con control de acceso por rol
- Catálogo general visible para cualquier visitante
- Detalle de cada publicación
- Panel del vendedor: crear, editar y eliminar publicaciones
- El vendedor agrega su contacto (WhatsApp, Messenger, Telegram, etc.)
- El usuario registrado ve el contacto del vendedor; el visitante no
- Filtrado de publicaciones por categoría

### Frontend
- Formularios con controles variados: texto, contraseña, radiobotones
  (tipo producto/servicio, tipo de cuenta), checkbox (términos,
  publicación activa), listas desplegables (categoría) y áreas de texto
- Hojas de estilo CSS y archivos JavaScript organizados por componentes

### Base de datos
Usuarios (con rol), publicaciones, categorías y datos de contacto.

## 3. Incluye si alcanza el tiempo (deseable)
- Carrito de compras con manejo de sesiones y registro de pedidos
- Panel de administrador (gestión de usuarios y publicaciones)
- Reportes de ventas diarios y mensuales
- PWA (instalable)
- Calificaciones y reseñas

## 4. No incluye (esta versión)
- Pagos en línea
- Chat interno entre comprador y vendedor
- Favoritos / lista de deseos
- Búsqueda avanzada
- Moderación

## 5. Limitaciones
- Solo estudiantes de la UMT; no hay verificación automática de identidad
- La compra se coordina directamente entre cliente y vendedor, fuera de la página
- Proyecto académico con fecha de entrega fija (2 de diciembre de 2026)