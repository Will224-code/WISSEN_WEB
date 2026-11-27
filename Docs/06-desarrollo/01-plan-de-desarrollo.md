# 01 · Plan de desarrollo

**Entrega:** miércoles 2 de diciembre de 2026 · **Inicio del plan:** viernes 27 de noviembre · **Días de trabajo:** 6 (27, 28, 29, 30, 1 y 2)
**Equipo:** 1 persona en Supabase (base de datos, permisos e imágenes) y el resto en el frontend (React). El plan se reparte en **tres pistas de frontend (A, B y C)**; si son dos personas, la pista C se reparte entre A y B (ver 3.4).
**Se basa en:** [documento de desarrollo](00-documento-de-desarrollo.md) (qué construir), [reglas de CSS](../05-vista/04-reglas-css.md) y los [ADR](../03-arquitectura/decisiones/ADR-001-estilo-de-arquitectura.md).

Este plan fija **en qué orden** se construye, quién hace qué cada día y qué se recorta si el tiempo no alcanza.

---

## 1. Regla principal: frontend y Supabase no se esperan

La mayor amenaza es que el frontend se quede parado esperando la base de datos. Para evitarlo:

1. **Hoy se acuerda el contrato** (sección 4): la lista de funciones de `src/services/` con lo que reciben y lo que devuelven.
2. **El frontend arranca con datos de prueba.** Cada servicio tiene una versión simulada que devuelve los datos del boceto (Dulces Sofi, Brownies de chocolate…). El frontend no sabe si los datos vienen de Supabase o de la simulación.
3. **El miércoles-jueves se cambia, servicio por servicio,** la simulación por la llamada real a Supabase. Cada cambio es pequeño y se prueba solo.

Así nadie bloquea a nadie y la integración es un cambio por archivo, no una reescritura.

---

## 2. Qué es obligatorio y qué es recortable

| Prioridad | Qué entra | Si no alcanza |
|---|---|---|
| **P1 · Núcleo** (sin esto no hay entrega) | Registro e inicio de sesión · Catálogo con buscador y filtros · Detalle de publicación · Listado y perfil de negocios · Mi perfil · Añadir negocio, entrar al negocio · Panel del negocio: crear, modificar y eliminar publicaciones, configuración · Calificar y comentar · Permisos por rol (`RutaProtegida` y RLS) · Despliegue en Vercel | No se recorta |
| **P2 · Requerido por el alcance** | Panel de administrador: Publicaciones, Negocios, Usuarios, Categorías, Administradores, Bitácora y bajas · Editar perfil y negocio · Ventanas de confirmación | Se recorta primero lo de P3; si aún falta, se entrega el administrador con menos secciones |
| **P3 · Reportes y extras** | Reportes del negocio (general y por publicación) · Reporte día por día · Reportes del administrador · Contadores (vistas y clics en WhatsApp) · Cámara · Buscador en Categorías y Administradores | **Se recorta en este orden:** 1) reporte día por día, 2) reportes del administrador, 3) reportes del negocio, 4) cámara |

Ya están **omitidos** por decisión del equipo: recuperar contraseñas, estado «subiendo imagen», foco de teclado en ventanas, y mostrar más de un enlace por red social (ver sección 9 del [documento de desarrollo](00-documento-de-desarrollo.md)).

---

## 3. Calendario

### 3.1 Resumen

| Día | Supabase | Frontend |
|---|---|---|
| **Vie 27** · Arranque | Proyecto creado, **tablas**, claves en `.env`, decidir el inicio de sesión con matrícula | Estructura de carpetas, estilos base, rutas, **servicios simulados**, piezas básicas |
| **Sáb 28** · Cimientos | **Autenticación** y usuario administrador maestro, categorías y datos de prueba | Piezas comunes y estructuras de página, `AuthContext`, `RutaProtegida`, Inicio de sesión y Registro |
| **Dom 29** · Páginas públicas | **RLS** (permisos) en todas las tablas, bucket de imágenes y su política | Catálogo, Detalle, Negocios, Perfil de negocio, Mi perfil, Añadir negocio |
| **Lun 30** · Vendedor | **Contadores** de reportes, bitácora, funciones del administrador | Panel del negocio, formulario de publicación, configuración, comentarios y estrellas |
| **Mar 1** · Integración | Apoya la integración; revisa permisos con cuentas de cada rol | **Cambio de servicios simulados a Supabase**, panel del administrador, reportes; **despliegue en Vercel** |
| **Mié 2** · Entrega | Solo correcciones | Solo correcciones y pruebas finales. **No se agregan funciones** |

> El **martes 1** es el día de integrarlo todo y desplegar; el **miércoles 2** queda como margen. Si el martes por la noche hay algo roto, se arregla ese día; el miércoles no se empieza nada nuevo.

### 3.2 Supabase (1 persona)

| Día | Tareas | Listo cuando |
|---|---|---|
| **Vie 27** | Crear el proyecto (plan gratuito). Crear las tablas según el análisis de base de datos: usuarios (con rol), negocios, categorías, publicaciones, imágenes, calificaciones, comentarios, contadores y bitácora. Pasar a todos `URL` y `anon key` para el `.env` (ADR-007) | Las tablas existen y el equipo puede conectarse |
| **Sáb 28** | Activar autenticación. **Definir cómo se inicia sesión con matrícula** (ver 5). Crear el administrador maestro. Cargar categorías (incluida «Otro») y datos de prueba iguales a los del boceto | Se puede registrar y entrar desde el cliente de Supabase |
| **Dom 29** | **RLS** en cada tabla según el [ADR-003](../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md): visitante solo lee; usuario califica y comenta lo suyo; vendedor edita solo su negocio; administrador da de baja. Bucket de imágenes público para leer y restringido para escribir ([ADR-004](../03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md)) | Una cuenta de cada rol prueba que no puede hacer lo que no le toca |
| **Lun 30** | Contadores por publicación, tipo y día ([ADR-006](../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md)). Bitácora de bajas. Contraseña del negocio. Dar de baja en cascada (publicación → imágenes y comentarios). Categoría eliminada → negocios pasan a «Otro» | Cada operación del administrador funciona y deja registro |
| **Mar 1** | Ayuda a cambiar los servicios simulados por los reales. Prueba de permisos con cuentas reales | Ningún rol puede saltarse la interfaz |
| **Mié 2** | Solo correcciones | — |

### 3.3 Frontend

El detalle de las páginas y piezas está en el [documento de desarrollo](00-documento-de-desarrollo.md) (secciones 3, 4 y 7).

| Pista | Responsabilidad | Piezas y páginas |
|---|---|---|
| **A · Base y cuentas** | Estilos, estructuras y todo lo de la sesión | `00-variables.css`, `01-base.css`, `02-estructura.css`, `Boton`, `Campo`, `Tarjeta`, `Alerta`, `Ventana`, `Icono`, `Avatar`, `Cargando`, `BarraSuperior`, `Pestanas`, `Pagina`, `AuthContext`, `RutaProtegida`, `mensajes.js` · `IniciarSesion`, `Registro`, `MiPerfil`, `EditarPerfil`, `NoEncontrada` |
| **B · Público y comprador** | Lo que ve el visitante y el usuario | `Buscador`, `BarraFiltros`, `TarjetaPublicacion`, `EstadoVacio`, `Galeria`, `Precio`, `Etiqueta`, `DatoContacto`, `Estrellas`, `Comentario`, `FormularioComentario`, `Ordenar` · `Catalogo`, `DetallePublicacion`, `ListadoNegocios`, `PerfilNegocio`, `AnadirNegocio`, `IngresoNegocio` |
| **C · Vendedor y administrador** | Panel del negocio y panel de administrador | `SelectorImagenes`, `Opcion`, `FotoEditable`, `MenuOpciones`, `Indicador`, `GraficaBarras`, `TablaReporte`, `FilaLista`, `ZonaBaja`, `Sugerencia`, `Aviso` · `PanelNegocio`, `DetallePublicacionNegocio`, `FormularioPublicacion`, `ConfiguracionNegocio`, `EditarNegocio`, `ReportesNegocio`, `ReportePublicacion`, `PanelAdministrador` |

| Día | Pista A | Pista B | Pista C |
|---|---|---|---|
| **Vie 27** | Estructura de `src/`, variables y estilos base, `Boton`, `Campo`, `Tarjeta`, `Alerta` | Estilos de `TarjetaPublicacion`, `Galeria`, `Etiqueta`, `Buscador` con datos fijos | Servicios simulados (`src/services/`) con los datos del boceto; `Tabla` y `Indicador` con datos fijos |
| **Sáb 28** | `BarraSuperior`, `Pestanas`, `Pagina`, `Ventana`, `AuthContext`, `RutaProtegida`, **Inicio de sesión y Registro** | `Catalogo` y `BarraFiltros` con servicios simulados | `SelectorImagenes`, `Opcion`, `FotoEditable`, `MenuOpciones` |
| **Dom 29** | `MiPerfil`, `EditarPerfil`, `NoEncontrada`, mensajes y alertas | `DetallePublicacion`, `ListadoNegocios`, `PerfilNegocio`, estrellas y comentarios | `PanelNegocio`, `FormularioPublicacion` (crear) |
| **Lun 30** | Apoya a B y C; revisa que todas las páginas usen las piezas y no estilos propios; accesibilidad básica | `AnadirNegocio`, `IngresoNegocio`; empieza el panel del administrador (Publicaciones, Negocios, Usuarios) | `FormularioPublicacion` (modificar), `DetallePublicacionNegocio`, `ConfiguracionNegocio`, `EditarNegocio` |
| **Mar 1** | **Integración con Supabase**: sesión y perfil · **Despliegue en Vercel** | Integración: catálogo, detalle, negocios, comentarios · Administrador: Categorías, Administradores, Bitácora | Integración: panel del negocio e imágenes · Reportes (si hay tiempo) |
| **Mié 2** | Pruebas con la lista de la sección 7 y correcciones | Correcciones | Correcciones |

### 3.4 Si el frontend son dos personas

Quitar la pista C y repartirla: **A** toma `PanelNegocio`, `FormularioPublicacion`, `DetallePublicacionNegocio` y `ConfiguracionNegocio`/`EditarNegocio`; **B** toma el panel de administrador. Los **reportes** pasan a P3 sin dueño: se hacen solo si el martes hay holgura. Con tres personas la ventaja es poder terminar los reportes.

---

## 4. Contrato entre frontend y Supabase

Se acuerda **hoy** y no cambia sin avisar. Cada función vive en `src/services/` (ADR-002). El frontend la usa desde el primer día con datos simulados.

| Servicio | Funciones | Quién la consume |
|---|---|---|
| `auth` | `registrar(datos)`, `entrar(matricula, contrasena)`, `salir()`, `usuarioActual()` | A |
| `usuarios` | `obtener(id)`, `actualizar(id, datos)`, `listar()` (admin), `darDeBaja(id)` (admin) | A, C |
| `negocios` | `listar(filtros)`, `obtener(id)`, `crear(datos)`, `actualizar(id, datos)`, `verificarContrasena(id, contrasena)`, `misNegocios()`, `eliminar(id, contrasena)`, `darDeBaja(id)` | A, B, C |
| `categorias` | `listar()`, `crear(nombre)`, `renombrar(id, nombre)`, `eliminar(id)` | B, C |
| `publicaciones` | `listar(filtros, pagina)` (10 por página), `obtener(id)`, `crear(datos)`, `actualizar(id, datos)`, `eliminar(id)`, `darDeBaja(id)` | B, C |
| `imagenes` | `subir(archivo)` (comprime y devuelve la URL), `eliminar(url)` | C |
| `calificaciones` | `calificar(publicacionId, estrellas)`, `miCalificacion(publicacionId)`, `promedio(publicacionId)` | B |
| `comentarios` | `listar(publicacionId, orden)`, `crear(publicacionId, texto)`, `editar(id, texto)`, `eliminar(id)`, `darDeBaja(id)` | B, C |
| `administracion` | `agregarAdministrador(matricula)`, `listarAdministradores()`, `darDeBajaAdministrador(id)`, `bitacora()` | C |
| `reportes` | `registrarVista(publicacionId)`, `registrarClic(publicacionId)`, `resumenNegocio(negocioId, mes)`, `resumenPublicacion(publicacionId, mes)`, `resumenGeneral(mes)` | B (registra), C (consulta) |

**Reglas del contrato**
- Todas las funciones devuelven `{ datos, error }`. El `error` trae el **código** de `mensajes.js` (por ejemplo `ACC-01`), no un texto.
- Los nombres de campo salen del [análisis de base de datos](../04-analisis%20de%20base%20de%20datos/incicaciones/00-manual-de-diseno-de-base-de-datos.md). Si Supabase cambia un nombre, avisa al frontend antes.
- Lo que no está en esta tabla no se pide a Supabase; se acuerda primero.

---

## 5. Decisiones que hay que tomar el viernes

| Tema | Por qué importa | Propuesta |
|---|---|---|
| **Inicio de sesión con matrícula** | Supabase Auth pide correo, y el sistema usa matrícula y contraseña | Usar la matrícula como correo interno (por ejemplo `12345678@wissen.local`) y no mostrar nunca ese correo. Confirmar que el registro no pide verificar el correo |
| **Contraseña del negocio** | No debe quedar en texto visible ni poder leerla el frontend | Guardarla con hash y verificarla con una función de la base de datos, no desde el cliente |
| **Administrador maestro** | El alcance dice que se da de alta en la base de datos | Crear la cuenta a mano con matrícula `00000001` y rol maestro |
| **Cómo se cuenta una vista** | Las vistas del propio dueño no cuentan | `registrarVista` ignora si quien ve es dueño del negocio |

---

## 6. Trabajo en Git

- `main` **siempre funciona.** Nadie sube a `main` algo que no compile.
- Una rama por persona y tarea: `a-base`, `b-publico`, `c-vendedor`, `supabase`. Nombre de commit corto y en español.
- **Se une a `main` al terminar cada día** (Pull Request revisado por otra persona; si el tiempo aprieta, por quien sube, pero avisando en el grupo). Antes de empezar cada día: `git pull`.
- Los **estilos son por pieza**: dos personas no tocan el mismo archivo `.css` de `piezas/`. Si una pieza de otra pista falta, se avisa; no se copia.
- El archivo `.env` **nunca** se sube (ADR-007). Se comparte por mensaje privado.

---

## 7. Lista de pruebas antes de entregar

Se prueba el **martes por la noche** y se repite el **miércoles** en la versión de Vercel.

| # | Prueba | Rol |
|:-:|---|---|
| 1 | Entrar como invitado, ver catálogo, buscar y filtrar por categoría y tipo | Visitante |
| 2 | Abrir una publicación; intentar calificar y ver la invitación a registrarse | Visitante |
| 3 | Registrarse y entrar; calificar y comentar; cambiar la calificación | Usuario |
| 4 | Añadir un negocio, entrar con su contraseña, crear una publicación con imágenes | Vendedor |
| 5 | Modificar y eliminar la publicación; salir del negocio | Vendedor |
| 6 | Ver reportes del negocio (si se hicieron) | Vendedor |
| 7 | Entrar a una ruta ajena por la dirección (`/admin/...`) y comprobar que no deja | Usuario y visitante |
| 8 | Dar de baja una publicación, un comentario, un usuario; ver la bitácora | Administrador |
| 9 | Agregar, editar y eliminar una categoría; ver que los negocios pasan a «Otro» | Administrador |
| 10 | Agregar un administrador hijo; comprobar que el maestro no se puede eliminar | Administrador |
| 11 | Abrir la aplicación en el celular (375 px) y repetir las pruebas 1, 3 y 4 | Todos |
| 12 | Recargar la página dentro de un negocio y confirmar que vuelve a pedir la contraseña | Vendedor |

---

## 8. Riesgos

| Riesgo | Qué hacer |
|---|---|
| Supabase se atrasa | El frontend sigue con servicios simulados; nada se detiene. Se recorta en el orden de la sección 2 |
| Dos personas editan el mismo archivo | Cada pista tiene sus piezas y páginas (3.3). Lo común va en la pista A |
| La integración del martes sale mal | Se hace servicio por servicio y se prueba cada uno; no todo junto |
| El plan gratuito se agota o se pausa | Probar el proyecto de Supabase el lunes; no dejarlo para el miércoles |
| «Una cosa más» el último día | El 2 de diciembre no se agrega ninguna función |
