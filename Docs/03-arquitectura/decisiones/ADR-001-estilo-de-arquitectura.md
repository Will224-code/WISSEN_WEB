# ADR-001 · Estilo de arquitectura: SPA con React + Supabase (BaaS)

- **Estado:** Propuesta (pendiente de revisión del equipo)
- **Fecha:** 2026-11-22

## Contexto

Wissen Web es una página de publicaciones de ventas para estudiantes emprendedores de la UMT. Tiene tres roles (visitante, usuario registrado y vendedor), un catálogo público, contactos visibles solo con sesión y subida de imágenes por parte del vendedor.

Condiciones que limitan la decisión:

- **Tecnología:** React es la tecnología permitida por el profesor.
- **Plazo:** la entrega es el 2 de diciembre de 2026.
- **Equipo:** estudiantes, sin experiencia previa en programar y mantener servidores.
- **Costo:** debe operar sin gastos (plan gratuito de Supabase y hospedaje gratuito).
- **Uso:** poco tráfico, comunidad cerrada de la UMT que llega por enlace.
- **Futuro:** posible PWA (deseable).

**Principio rector del equipo: facilitar el desarrollo y evitar la complejidad.** Entre dos opciones que cumplen, se elige la más simple.

## Opciones consideradas

| Opción | Descripción | Resultado |
|---|---|---|
| A. Web tradicional con servidor propio | Páginas generadas en el servidor (PHP o Node) | Descartada: no aprovecha React y exige programar y mantener un servidor |
| **B. SPA + BaaS** | React en el navegador y Supabase como backend | **Elegida** |
| C. SPA + API propia | React con un backend hecho a mano (Express y PostgreSQL) | Descartada: añade trabajo y hospedaje sin aportar nada que Supabase no dé |
| D. SSR con Next.js | React con páginas generadas en el servidor | Descartada: su ventaja principal (SEO) importa poco aquí y añade complejidad |
| E. Microservicios o serverless puro | Muchos servicios pequeños | Descartada: desproporcionada para el tamaño del proyecto |

## Decisión

El sistema será **cliente–servidor**, con:

- **Frontend:** una SPA hecha con React, empaquetada con Vite.
- **Backend:** Supabase como servicio (BaaS), que aporta autenticación, base de datos PostgreSQL, API automática, almacenamiento de archivos y permisos por fila (RLS).

No se programará un servidor propio.

## Consecuencias

**A favor**

- No hay servidor que programar, hospedar ni mantener: el esfuerzo se concentra en la interfaz.
- Todo opera en planes gratuitos.
- La navegación entre vistas es fluida (sin recargar la página).
- Una SPA es la base natural para añadir una PWA después.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| Los buscadores indexan peor una SPA | Poco relevante: la comunidad llega por enlace |
| La primera carga descarga toda la app | Dividir el código por vista y comprimir imágenes |
| Los permisos no pueden vivir solo en el navegador | Aplicarlos también en la base de datos con RLS |
| Dependencia de Supabase y de su plan gratuito (pausa tras 7 días sin actividad; límites de 1 GB de archivos y 5 GB de ancho de banda al mes) | Abrir la app antes de la entrega y comprimir imágenes |

## Decisiones relacionadas (por documentar)

- Organización interna del frontend (capas o por funcionalidades).
- Estrategia de permisos (rutas protegidas y RLS).
- Almacenamiento de imágenes (Storage y URL en la base de datos).
