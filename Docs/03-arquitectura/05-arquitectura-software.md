# 05 · Arquitectura del software

Página web de publicaciones de ventas para estudiantes emprendedores de la UMT. Borrador inicial: a revisar y completar en equipo.

## 1. Propósito y alcance

Este documento describe cómo está armado el sistema por dentro: qué piezas tiene, qué hace cada una y cómo se comunican.

- **Cubre:** estilo de arquitectura, capas, componentes, rutas, backend, seguridad, flujos, preparación para PWA y despliegue.
- **No cubre:** el modelo de la base de datos (análisis ER del equipo de BD) ni el diseño de las pantallas (Figma). Aquí solo se indica qué entidades y qué vistas existen.

## 2. Visión general

Tres tipos de usuario usan una aplicación que corre en el navegador y que se comunica con un backend externo (Supabase).

| Actor | Qué puede hacer |
| --- | --- |
| Visitante | Ver el catálogo y el detalle de las publicaciones; no ve los contactos |
| Usuario registrado (cliente) | Todo lo del visitante, más ver el contacto del vendedor y usar su panel |
| Vendedor | Todo lo del usuario registrado, más crear, editar y eliminar sus publicaciones y agregar sus contactos |

## 3. Estilo de arquitectura

El sistema es **cliente–servidor**: una **SPA** hecha con React en el navegador y **Supabase** como backend listo para usar (BaaS).

### 3.1 Qué es una SPA

SPA (Single Page Application) es una aplicación de una sola página. El navegador descarga la app una vez y, al cambiar de vista, solo cambia lo necesario en pantalla; los datos se piden al backend sin recargar la página.

| Aspecto | Web tradicional | SPA |
| --- | --- | --- |
| Cambio de vista | El servidor envía una página nueva | El navegador cambia las piezas en pantalla |
| Recarga | En cada clic | Ninguna |
| Datos | Vienen dentro de cada página | Se piden a la API cuando hacen falta |
| Base para PWA | No directa | Sí: el cascarón de la app se guarda en caché |

### 3.2 Por qué una SPA en este proyecto

- **React ya la entrega:** una app React con Vite es una SPA por defecto; no requiere configuración extra y es la tecnología permitida por el profesor.
- **Navegación fluida:** pasar del catálogo al detalle de una publicación o al panel no recarga la página.
- **Componentes reutilizables:** una misma tarjeta de publicación sirve en el catálogo y en la página del vendedor.
- **Sin servidor propio:** los datos vienen de Supabase, así que no hace falta programar ni mantener un backend.
- **Camino directo a PWA:** el service worker guarda el cascarón de la SPA y permite instalarla en el celular.

Limitaciones y cómo se atienden:

| Limitación | Cómo se atiende |
| --- | --- |
| Buscadores indexan peor el contenido armado en el navegador | Poco relevante: es una comunidad cerrada de la UMT que llega por enlace |
| La primera carga descarga toda la app | Código dividido por vistas e imágenes comprimidas |
| Los permisos no pueden vivir solo en el navegador | Se aplican también en la base de datos con RLS |

### 3.3 Backend como servicio (BaaS)

Supabase ofrece autenticación, base de datos PostgreSQL, API automática y almacenamiento de archivos. El equipo configura y consume estos servicios en lugar de programar un servidor desde cero.

## 4. Capas y componentes

```
┌─────────────────────────────────────────────┐
│ NAVEGADOR (cliente)                         │
│  Presentación : React (vistas, formularios) │
│  Navegación   : React Router                │
│  Estado/datos : Context + servicios         │
└──────────────────┬──────────────────────────┘
                   │ HTTPS (cliente supabase-js)
┌──────────────────▼──────────────────────────┐
│ SUPABASE (backend)                          │
│  Auth  │  API (REST)  │  Storage (imágenes) │
│  Row Level Security (permisos por rol)      │
└──────────────────┬──────────────────────────┘
┌──────────────────▼──────────────────────────┐
│ PostgreSQL (base de datos)                  │
└─────────────────────────────────────────────┘
```

| Capa | Responsabilidad | Tecnología |
| --- | --- | --- |
| Presentación | Mostrar vistas y formularios | React, JSX, CSS |
| Navegación | Rutas y protección por rol | React Router |
| Estado y servicios | Sesión del usuario; llamadas a la API | Context API, JavaScript |
| Backend | Login, permisos, API y archivos | Supabase |
| Datos | Guardar la información | PostgreSQL |

## 5. Estructura del frontend

```
src/
├── pages/          # una por vista (Inicio, Detalle, Login, ...)
├── components/     # piezas reutilizables (Navbar, TarjetaPublicacion, Filtro...)
├── context/        # AuthContext: usuario y rol actuales
├── services/       # funciones que hablan con Supabase
├── routes/         # definición de rutas y RutaProtegida
├── lib/            # configuración del cliente de Supabase
├── styles/         # CSS por componente
└── main.jsx
```

- **Las páginas no llaman a Supabase directamente:** usan `services/`, así el acceso a datos queda en un solo lugar.
- **AuthContext:** guarda quién inició sesión y su rol; cualquier componente lo consulta.
- **RutaProtegida:** componente que bloquea una ruta si el rol no corresponde.

## 6. Rutas y acceso por rol

| Ruta | Vista | Visitante | Usuario | Vendedor |
| --- | --- | --- | --- | --- |
| `/` | Inicio / catálogo general | Sí | Sí | Sí |
| `/publicacion/:id` | Detalle (contacto oculto al visitante) | Sí | Sí | Sí |
| `/vendedor/:id` | Página del vendedor (su catálogo) | Sí | Sí | Sí |
| `/login` | Login | Sí | No | No |
| `/registro` | Registro | Sí | No | No |
| `/panel-usuario` | Panel del usuario | No | Sí | Sí |
| `/panel-vendedor` | Panel del vendedor | No | No | Sí |

La lista final de vistas la confirma el diseño en Figma.

## 7. Backend en Supabase

| Servicio | Función en el proyecto |
| --- | --- |
| Auth | Registro, login y sesión |
| PostgreSQL | Base de datos del sistema |
| API | CRUD de publicaciones, perfiles, categorías y contactos |
| Storage | Imágenes de las publicaciones |
| RLS | Reglas de quién puede leer o modificar cada fila |

### 7.1 Almacenamiento de imágenes

Las imágenes **no se guardan dentro de la base de datos**: se suben a Supabase Storage y en la tabla de publicaciones solo queda el texto de su URL (`imagen_url`). Así las tablas se mantienen ligeras y las fotos se entregan por CDN.

Flujo de subida (desde el navegador del vendedor, sin pasar por un servidor propio):

1. Validar el archivo y comprimirlo en el navegador.
2. Renombrarlo con un UUID y una ruta que incluya el id del vendedor (ej. `<vendedor_id>/<uuid>.webp`).
3. Subirlo al bucket (ej. `publicaciones`) con `supabase.storage`.
4. Pedir la URL pública del archivo.
5. Guardar esa URL en la publicación.

| Control | Qué se hace | Por qué |
| --- | --- | --- |
| Permisos (RLS en Storage) | Lectura pública; subir, editar y borrar solo con sesión activa y rol vendedor | El visitante ve el catálogo; nadie más sube archivos |
| Nombre único | UUID o marca de tiempo en vez del nombre original | Dos `foto.jpg` no se sobrescriben |
| Validación y compresión | Solo `image/jpeg`, `image/png`, `image/webp`; comprimir a 150–300 KB; máximo 2 MB | Cuida el espacio y la velocidad |
| Archivos huérfanos | Limpieza periódica de imágenes subidas cuyo formulario no se guardó | Evita basura acumulada |
| Miniaturas | Versiones redimensionadas por parámetro de URL (ej. 300×300 px) | No se duplican archivos |

### 7.2 Límites del plan gratuito de Supabase

| Recurso | Cuota | Impacto en el proyecto |
| --- | --- | --- |
| Storage | 1 GB | 3,000 a 6,000 fotos si se comprimen a \~200 KB; sin comprimir (4–8 MB) alcanzaría para 150–250 |
| Base de datos | 500 MB | Sobra: solo se guarda texto y URLs |
| Ancho de banda | 5 GB al mes | Descarga de imágenes al navegar el catálogo |

- Un proyecto gratuito se **pausa tras 7 días sin actividad**; los datos se conservan y se reactiva solo con una nueva petición.
- Antes de la entrega (2 de diciembre de 2026) conviene abrir la app para comprobar que esté activa.

## 8. Entidades de datos (alto nivel)

| Entidad | Qué representa |
| --- | --- |
| Perfiles | Cada usuario del sistema, con su rol (cliente o vendedor) |
| Categorías | Clasificación de las publicaciones |
| Publicaciones | Producto o servicio que ofrece un vendedor, con su imagen |
| Contactos | Medios de contacto de un vendedor (WhatsApp, Messenger, Telegram, etc.) |

Relaciones: un vendedor tiene muchas publicaciones y varios contactos; una categoría agrupa muchas publicaciones. Campos, tipos y relaciones exactas se definen en el análisis de la base de datos (ER).

## 9. Seguridad y permisos

Los permisos se aplican **en dos lugares**: las rutas protegidas ocultan lo que el rol no debe ver, y RLS en la base de datos impide leer o modificar lo que no corresponde aunque alguien salte la interfaz.

| Entidad | Leer | Crear, editar y eliminar |
| --- | --- | --- |
| Publicaciones | Todos (incluye visitantes) | Solo el vendedor dueño |
| Categorías | Todos | Nadie desde la app |
| Contactos | Solo usuarios con sesión | Solo el vendedor dueño |
| Perfiles | Todos (datos públicos) | Solo el propio usuario |
| Imágenes (Storage) | Todos | Solo el vendedor dueño (la ruta del archivo lleva su id, así la regla puede comprobarlo) |

Las claves del proyecto van en variables de entorno y nunca se suben al repositorio.

## 10. Flujos principales

**Registro e inicio de sesión**

1. El usuario llena el formulario y elige el tipo de cuenta.
2. Supabase Auth crea la sesión y el rol queda guardado en su perfil.
3. `AuthContext` carga el rol y redirige según corresponda.

**Publicar (vendedor)**

1. Entra a `/panel-vendedor` (ruta protegida).
2. Llena el formulario y sube la imagen a Storage.
3. Se guarda la publicación; RLS verifica que sea el dueño.
4. La publicación aparece en el catálogo.

### 10.1 Ejemplo completo: un cliente ve el contacto de un vendedor

Muestra cómo participa cada capa en un solo caso.

| Paso | Capa | Qué ocurre |
| --- | --- | --- |
| 1 | Navegación | El cliente entra a `/publicacion/:id`; React Router muestra la vista de detalle sin recargar |
| 2 | Servicios | La vista llama a `getPublicacion(id)` en `services/` |
| 3 | Backend (API + RLS) | Supabase devuelve título, descripción e `imagen_url`; la lectura está permitida a todos |
| 4 | Storage | El navegador descarga la imagen desde su URL pública |
| 5 | Estado | La vista consulta `AuthContext`: hay sesión, entonces pide los contactos |
| 6 | Backend (API + RLS) | RLS permite leer contactos porque hay sesión activa |
| 7 | Presentación | Se muestran los botones de WhatsApp, Messenger o Telegram; el trato se hace fuera de la página |

Si quien entra es un visitante, el paso 5 no encuentra sesión y la vista muestra una invitación a iniciar sesión. Aunque alguien intentara pedir los contactos por fuera de la app, RLS los negaría.

## 11. Preparación para PWA

Una PWA (Progressive Web App) es una página web que se puede instalar como app y que guarda copias de sus archivos para cargar rápido y, en parte, funcionar sin internet. No cambia la arquitectura: se agrega como una capa sobre la SPA.

| Pieza | Qué es |
| --- | --- |
| Manifest | Archivo con nombre, íconos y colores que permite instalar la app |
| Service worker | Script que corre aparte y guarda copias de archivos e imágenes |
| HTTPS | Obligatorio; lo dan Vercel y Netlify |

Lo que la arquitectura debe permitir desde ahora:

- **SPA con rutas del lado del cliente:** su cascarón (JS, CSS, HTML) es lo que se guarda en caché.
- **Acceso a datos centralizado en `services/`:** permite decidir qué se guarda y qué no.
- **Diseño pensado primero para celular** e imágenes comprimidas, para que lo guardado pese poco.

| Dato | Comportamiento esperado |
| --- | --- |
| Cascarón de la app | Se guarda siempre |
| Catálogo y publicaciones | Se muestra lo guardado y se actualiza en segundo plano |
| Imágenes | Se guardan, con un límite de cantidad |
| Contactos, login y panel del vendedor | No se guardan; siempre van a la red |

Los contactos no se guardan porque solo los usuarios con sesión pueden verlos y una copia en caché se saltaría esa regla. Sin internet se puede ver lo ya visitado, pero no publicar, editar ni ver contactos; la interfaz debe avisar que no hay conexión.

La PWA está en el alcance deseable. Se puede añadir al final con un plugin de Vite (por ejemplo `vite-plugin-pwa`) sin rehacer lo anterior.

## 12. Despliegue

- **Frontend:** Vercel o Netlify, conectado a GitHub para publicar con cada cambio a la rama principal.
- **Backend:** proyecto de Supabase en la nube.
- **Entornos:** local (Vite) y producción.
