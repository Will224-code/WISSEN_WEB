# 00 · Ficha de idea inicial: Página web

> **Para qué sirve este documento:** es un borrador rápido para dejar clara la idea antes de la documentación oficial. No es detallado a propósito. Primero nos ponemos de acuerdo en *qué* vamos a hacer; después, en equipo, lo complementamos y de aquí sale la documentación formal.
>
> **Cómo leerlo:** los puntos marcados con ⚠️ son decisiones pendientes del equipo (ver punto 9).
>
> **Término usado en todo el documento:** *Vendedor* = estudiante de la UMT con un emprendimiento que publica sus productos o servicios en la página.

---

## 1. La idea en una frase

Queremos hacer una página web de publicaciones de ventas para estudiantes emprendedores de la UMT. Permitirá a los usuarios navegar el catálogo de productos y servicios de los vendedores, y a los vendedores les permitirá subir, editar y eliminar sus propias publicaciones.

---

## 2. Tipo de proyecto

- [x] Tienda en línea / catálogo
- [ ] Portal informativo o blog
- [ ] Sistema de reservas o citas
- [ ] Plataforma de gestión (inventario, tareas, clientes, etc.)
- [x] Comunidad / red (perfiles, publicaciones, comentarios)
- [ ] Otro: ______

**¿Por qué este tipo?** Es principalmente un catálogo, porque su función central es mostrar los productos y servicios de los vendedores. Tiene un componente de comunidad (secundario) por los perfiles de vendedor y las reseñas.

---

## 3. Problema o necesidad

Muchos estudiantes de la UMT tienen emprendimientos, pero no cuentan con una forma accesible de darse a conocer. Esta página resuelve ese problema: cualquier estudiante con un emprendimiento puede registrarse como vendedor y subir sus productos o servicios, que quedan visibles para la comunidad y llegan a más personas.

**Objetivo:** facilitar la visibilidad de los emprendimientos estudiantiles dentro de la UMT.

---

## 4. Público objetivo

- **Dirigida a:**
  - Vendedores: estudiantes de la UMT que tengan un emprendimiento.
  - Compradores: la comunidad estudiantil de la UMT.
- **Qué necesitan o esperan de la página:** un lugar donde publicar y encontrar productos y servicios, de alumnos para alumnos, y poder contactar fácilmente al vendedor.
- **Familiaridad con la tecnología:** media.

---

## 5. Tipos de usuario

| Usuario | Qué puede hacer |
|---|---|
| **Visitante** (sin cuenta) | Navegar por la página y ver el catálogo de productos y servicios y los datos de contacto de los vendedores. No puede calificar, comentar ni crear negocios. |
| **Usuario registrado** | Todo lo del Visitante, más: calificar productos y dejar reseñas, y crear un negocio para pasar a Vendedor. |
| **Vendedor** (registrado) | Todo lo del Usuario registrado, más: acceder a su panel, subir, editar y eliminar sus propias publicaciones, y su teléfono de WhatsApp es su contacto, y consultar los reportes mensuales de sus negocios. |
| **Administrador** | Controla el sistema: ve todas las publicaciones, da de baja publicaciones, comentarios, usuarios y negocios, agrega, edita y elimina categorías, consulta los reportes de todo el sistema y agrega más cuentas de administrador. Ver [04-usuarios](04-usuarios.md). |


---

## 6. Funcionalidades (lluvia de ideas)

**Imprescindibles (⭐):**
- ⭐ El usuario puede registrarse; pasa a vendedor al crear un negocio.
- ⭐ El usuario puede iniciar sesión con su matrícula y el sistema identifica su rol.
- ⭐ Cualquier visitante puede ver el catálogo de productos y servicios.
- ⭐ El vendedor puede acceder a su panel de vendedor.
- ⭐ El vendedor puede subir, editar y eliminar sus publicaciones desde el panel.
- ⭐ El contacto del vendedor es su teléfono de WhatsApp (el del negocio).
- ⭐ Cualquier usuario puede ver la sección de contacto del vendedor para adquirir un producto o servicio.

- ⭐ El usuario registrado o el vendedor puede calificar productos y comentarlos; cada comentario muestra su fecha y se ordena por más recientes o más antiguos.
- ⭐ El visitante o usuario puede buscar y filtrar publicaciones por categoría y por tipo (producto o servicio), y buscar negocios.
- ⭐ Cualquier persona puede ver el perfil público de un negocio con todas sus publicaciones.
- ⭐ El vendedor puede ver reportes mensuales de sus negocios (vistas, clics en WhatsApp, calificación y comentarios) y el administrador los de todo el sistema.

**Deseables (no imprescindibles):**
- Carrito de compras y registro de pedidos.
- Reportes de ventas en dinero (no se incluyen por ahora; los reportes actuales miden el interés, no las ventas).
- PWA (instalable).

---

## 7. Producto mínimo

Si solo pudiera hacer 4 cosas, serían:
1. Registrarse e iniciar sesión (y pasar a vendedor al crear un negocio).
2. Ver el catálogo de productos y servicios.
3. El vendedor puede subir, editar y eliminar sus publicaciones desde su panel.
4. El vendedor puede agregar su contacto y los usuarios pueden verlo.

---

## 8. Fuera por ahora

Ideas buenas que se dejan para después:
- Pagos en línea
- Chat interno entre comprador y vendedor
- Favoritos / lista de deseos
- Búsqueda avanzada (se incluye solo el buscador simple con filtros por categoría y tipo)
- Descargar o exportar los reportes

---