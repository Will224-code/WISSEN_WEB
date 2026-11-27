# 00 · Documento de desarrollo

**Estado:** Entregado · **Para:** el equipo de React
**Fuentes:** [boceto refinado](../05-vista/boceto-refinado/index.html) (44 pantallas), [alcance](../01-producto/03-alcance.md), [usuarios](../01-producto/04-usuarios.md), [ADR-002](../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md) a [ADR-007](../03-arquitectura/decisiones/ADR-007-despliegue-y-entornos.md), [bases del diseño](../05-vista/04-bases-del-diseno.md) y [reglas de CSS](../05-vista/05-reglas-css.md)

Este documento es la **lista de trabajo**: qué páginas hay que crear, cuáles pantallas del boceto se unen en una sola página, qué piezas se reutilizan y qué mensajes existen. Si el boceto y el alcance o usuarios no coinciden, **prevalecen alcance y usuarios** (ver sección 9).

Cómo se contó: pantallas en las listas `CATEG` y `CATEG_ADM`; páginas por las funciones de vista que comparten (`V[2]=V[4]`, `V[9]=V[11]`, etc.); ventanas en el objeto `M` y la tabla `MODALS`; alertas buscando `S.err=` (cada texto distinto, una vez); avisos buscando `toast(`; sugerencias en los recuadros `adm-hint`; estados vacíos en las tarjetas «Todavía no…», «Aún no hay…», «No hay…» y «Ningún … coincide».

---

## 1. Resumen en cifras

| Qué | Cantidad | Nota |
|---|:-:|---|
| Pantallas del boceto | **44** | 32 vistas + 12 ventanas emergentes |
| Páginas que salen del boceto | **18** | Las 32 vistas se unen en 18 páginas (sección 2) |
| Páginas que exige la arquitectura y no están en el boceto | **1** | «Página no encontrada» (ADR-007) |
| **Páginas a programar** | **19** | |
| Ventanas emergentes | **12** | Todas usan la pieza `Ventana`; solo hay **6 contenidos distintos** |
| Alertas de error en el boceto | **14** | Textos distintos (el de «Falta llenar el campo…» cuenta una vez) |
| Alertas que faltan en el boceto | **7** | 5 que exige la entrega y el alcance + 2 recomendadas |
| Avisos de éxito | **5** | Mensajes que desaparecen solos |
| Sugerencias | **6** | Recuadros morados del panel de administrador |
| Estados vacíos | **14** | Todos con la pieza `EstadoVacio` |
| Piezas de estilo | **48** | 42 se reutilizan en 2 o más pantallas; 6 son de una sola página |
| Estructuras de página | **8** | Barras superiores y esquemas que se repiten (sección 4) |

---

## 2. De pantallas a páginas

| Pantallas del boceto | Página | Por qué se unen |
|---|---|---|
| 1 | `IniciarSesion` | — |
| 3 | `Registro` | — |
| 2, 4 | `Catalogo` | Es la misma función (`catalog`). Solo cambia la barra: «Iniciar sesión» si es visitante, o «Mi perfil» y avatar si hay sesión (y «Ver panel de administrador» si es administrador) |
| 20, 21, 38 | `DetallePublicacion` | Misma galería, precio, datos y comentarios. Cambia por quién la ve: el visitante (21) ve el formulario bloqueado con candado; el usuario (20) puede calificar y comentar; el administrador (38) ve barra morada, «Zona de baja» y «Eliminar» en cada comentario, sin formulario |
| 27 | `ListadoNegocios` | — |
| 28, 39 | `PerfilNegocio` | Misma credencial y fichas. El administrador (39) ve barra morada y «Zona de baja» |
| 5, 7 | `MiPerfil` | Es la misma función (`profile`). 5 es el estado «sin negocios» y 7 «con negocios» |
| 24 | `EditarPerfil` | — |
| 6 | `AnadirNegocio` | — |
| 8 | `IngresoNegocio` | — |
| 9, 11 | `PanelNegocio` | Es la misma función (`panel`). 11 solo resalta la publicación recién creada con la variante `--nueva` |
| 12 | `DetallePublicacionNegocio` | No se une a `DetallePublicacion`: vive dentro del panel del negocio, muestra la fecha, «Opciones» y «Salir» y no tiene formulario. Reutiliza sus piezas (`Galeria`, `Precio`, `Comentario`) |
| 10, 15 | `FormularioPublicacion` | Es la misma función (`postForm`). 10 crea (vacío) y 15 modifica (con datos) |
| 16 | `ConfiguracionNegocio` | — |
| 17 | `EditarNegocio` | — |
| 41, 42 | `ReportesNegocio` | Misma página con dos subpestañas: «General» y «Por publicación» |
| 43 | `ReportePublicacion` | Se abre desde el negocio (42) y desde el administrador (44). Misma página con la barra de quien la abrió |
| 29, 30, 31, 32, 33, 34, 44 | `PanelAdministrador` | Una página con 7 secciones (pestañas) que comparten barra morada: Publicaciones, Negocios, Usuarios, Categorías, Administradores, Bitácora y Reportes |
| 13, 14, 18, 19, 22, 23, 25, 26, 35, 36, 37, 40 | *(no son páginas)* | Ventanas emergentes (sección 5) |
| — | `NoEncontrada` | No está en el boceto. Necesaria porque la app es una SPA con rutas (ADR-007) |

---

## 3. Páginas

Roles: **visitante** (sin sesión), **usuario** (con sesión), **vendedor** (usuario que entró a su negocio con la contraseña del negocio), **administrador**.

| # | Página | Ruta sugerida | Pantallas | Quién puede entrar | Piezas que usa |
|:-:|---|---|---|---|---|
| 1 | `IniciarSesion` | `/entrar` | 1 | Solo visitante | `Libreta`, `Marca`, `Campo`, `Boton`, `Alerta` |
| 2 | `Registro` | `/registro` | 3 | Solo visitante | `Tarjeta`, `Campo`, `Boton`, `Alerta` |
| 3 | `Catalogo` | `/` | 2, 4 | Todos | `BarraSuperior`, `Pestanas`, `EncabezadoSeccion`, `BarraFiltros`, `TarjetaPublicacion`, `EstadoVacio` |
| 4 | `DetallePublicacion` | `/publicacion/:id` y `/admin/publicacion/:id` | 20, 21, 38 | Todos; el modo administrador solo el administrador | `BarraSuperior`, `Ruta`, `Hoja`, `Galeria`, `Precio`, `Etiqueta`, `DatoContacto`, `Estrellas`, `FormularioComentario`, `Comentario`, `Ordenar`, `ZonaBaja`, `Alerta`, `EstadoVacio` |
| 5 | `ListadoNegocios` | `/negocios` | 27 | Todos | `BarraSuperior`, `Pestanas`, `EncabezadoSeccion`, `BarraFiltros`, `TarjetaNegocio`, `FotoNegocio`, `EstadoVacio` |
| 6 | `PerfilNegocio` | `/negocio/:id` y `/admin/negocio/:id` | 28, 39 | Todos; el modo administrador solo el administrador | `BarraSuperior`, `BarraVolver`, `Ruta`, `Credencial`, `FotoNegocio`, `Etiqueta`, `TarjetaPublicacion`, `ZonaBaja`, `EstadoVacio` |
| 7 | `MiPerfil` | `/perfil` | 5, 7 | Usuario | `BarraSuperior`, `Tarjeta`, `Avatar`, `FilaNegocio`, `MenuOpciones`, `Boton`, `EstadoVacio` |
| 8 | `EditarPerfil` | `/perfil/editar` | 24 | Usuario | `Tarjeta`, `FotoEditable`, `MenuOpciones`, `Campo`, `Boton`, `Alerta` |
| 9 | `AnadirNegocio` | `/negocio/nuevo` | 6 | Usuario | `Tarjeta`, `Campo`, `Boton`, `Alerta` |
| 10 | `IngresoNegocio` | `/mi-negocio/:id/entrar` | 8 | Usuario dueño de ese negocio | `Tarjeta`, `FotoNegocio`, `Campo`, `Boton`, `Alerta` |
| 11 | `PanelNegocio` | `/mi-negocio/:id` | 9, 11 | Vendedor con el negocio abierto | `BarraSuperior`, `Pestanas`, `EncabezadoSeccion`, `TarjetaPublicacion`, `EstadoVacio` |
| 12 | `DetallePublicacionNegocio` | `/mi-negocio/:id/publicacion/:pid` | 12 | Vendedor con el negocio abierto | `BarraSuperior`, `Pestanas`, `Hoja`, `Galeria`, `Precio`, `Etiqueta`, `Estrellas`, `Comentario`, `Ordenar` |
| 13 | `FormularioPublicacion` | `/mi-negocio/:id/publicar` y `…/publicacion/:pid/editar` | 10, 15 | Vendedor con el negocio abierto | `BarraSuperior`, `Pestanas`, `Tarjeta`, `SelectorImagenes`, `Campo`, `Opcion`, `DatoContacto`, `Boton`, `Alerta` |
| 14 | `ConfiguracionNegocio` | `/mi-negocio/:id/configuracion` | 16 | Vendedor con el negocio abierto | `BarraSuperior`, `Pestanas`, `Tarjeta`, `Avatar`, `Etiqueta`, `BotonRed`, `Boton` |
| 15 | `EditarNegocio` | `/mi-negocio/:id/configuracion/editar` | 17 | Vendedor con el negocio abierto | `BarraSuperior`, `Pestanas`, `Tarjeta`, `FotoEditable`, `MenuOpciones`, `Campo`, `BloqueRed`, `Boton`, `Alerta` |
| 16 | `ReportesNegocio` | `/mi-negocio/:id/reportes` | 41, 42 | Vendedor con el negocio abierto | `BarraSuperior`, `Pestanas` (separador y segmento), `SelectorMes`, `Indicador`, `GraficaBarras`, `TablaReporte`, `EstadoVacio` |
| 17 | `ReportePublicacion` | `/mi-negocio/:id/reportes/:pid` y `/admin/reportes/:pid` | 43 | Vendedor dueño o administrador | `BarraSuperior`, `BarraVolver`, `Ruta`, `SelectorMes`, `Indicador`, `GraficaDias`, `Etiqueta` |
| 18 | `PanelAdministrador` | `/admin/:seccion` | 29–34, 44 | Administrador | `BarraSuperior` (administrador), `Pestanas` (administrador), `EncabezadoSeccion`, `Sugerencia`, `BarraFiltros`, `Buscador`, `FilaLista`, `MenuOpciones`, `Etiqueta`, `Indicador`, `GraficaBarras`, `TablaReporte`, `SelectorMes`, `EstadoVacio`, `Aviso` |
| 19 | `NoEncontrada` | `*` | — | Todos | `BarraSuperior`, `EstadoVacio`, `Boton` |

---

## 4. Estructuras de página

Son componentes de estructura (regla 3): se escriben una vez y cada página los usa.

| Estructura | Qué tiene | Páginas que la usan |
|---|---|---|
| `Portada` | Fondo azul con la libreta al centro, sin barra | `IniciarSesion` |
| `PaginaCentrada` | Papel cuadriculado y una tarjeta centrada, sin barra | `Registro`, `EditarPerfil`, `AnadirNegocio`, `IngresoNegocio` |
| `BarraSuperior--catalogo` | Marca «Wissen», pestañas «Catálogo» y «Negocios», y «Iniciar sesión» o «Mi perfil» con avatar (más «Ver panel de administrador» si corresponde) | `Catalogo`, `ListadoNegocios`, `PerfilNegocio`, `NoEncontrada` |
| `BarraSuperior--detalle` | «Volver», logo y sesión | `DetallePublicacion` (visitante y usuario) |
| `BarraSuperior--perfil` | Logo (vuelve al catálogo), «Mi perfil» y avatar | `MiPerfil` |
| `BarraSuperior--negocio` | Foto y nombre del negocio y pestañas «Panel inicial», «Configuración» y «Reportes» | `PanelNegocio`, `DetallePublicacionNegocio`, `FormularioPublicacion`, `ConfiguracionNegocio`, `EditarNegocio`, `ReportesNegocio`, `ReportePublicacion` (desde el negocio) |
| `BarraSuperior--administrador` | Barra morada «Modo administrador», 7 pestañas con contador y «Salir del panel» | `PanelAdministrador`, `PerfilNegocio` (modo administrador), `ReportePublicacion` (desde el administrador) |
| `BarraSuperior--administrador-detalle` | Barra morada con «Volver», título y «Salir del panel» | `DetallePublicacion` (modo administrador) |

---

## 5. Ventanas emergentes

Todas son la pieza `Ventana` (velo oscuro + recuadro, se cierra con Escape o pulsando fuera). Solo cambia el contenido.

| Pantalla | Ventana | Se abre desde | Contenido (pieza compartida) |
|:-:|---|---|---|
| 13 | Modificar o eliminar | Botón «Opciones» de `DetallePublicacionNegocio` (12) | `VentanaOpciones`: Modificar, Eliminar, Cancelar |
| 14 | Confirmar eliminación | «Eliminar» en la ventana 13 | **`VentanaConfirmacion`** |
| 18 | Elegir negocio a eliminar | Menú ⋮ › «Eliminar un negocio» en `MiPerfil` (7) | `VentanaEleccion`: lista de opciones + Continuar |
| 19 | Confirmar eliminación del negocio | «Continuar» en la ventana 18 | **`VentanaConfirmacion`** con campo de contraseña |
| 22 | Aviso de registro | Estrellas, cuadro de comentario o «Comentar» del visitante (21) | **`VentanaConfirmacion`** con ícono de usuario: Registrarse / Cancelar |
| 23 | Salir del negocio | «Salir del negocio» en `ConfiguracionNegocio` (16) | **`VentanaConfirmacion`** |
| 25 | Ver foto | Menú de foto en `EditarPerfil` (24) y `EditarNegocio` (17) | `VentanaFoto` |
| 26 | Tomar foto | Menú de foto en 24 y 17 | `VentanaCamara` |
| 35 | Confirmar baja | «Eliminar» en 38, 39, 31, 33, 32 y en cada comentario de 38 | **`VentanaConfirmacion`** (el texto cambia según qué se da de baja) |
| 36 | Agregar categoría | «Agregar categoría» en Categorías (32) | **`VentanaCampo`** |
| 37 | Agregar administrador | «Agregar administrador» en Administradores (33) | **`VentanaCampo`** |
| 40 | Editar categoría | «Editar» en Categorías (32) | **`VentanaCampo`** |

**Comparten pieza:** 14, 19, 22, 23 y 35 son la misma `VentanaConfirmacion` (5 ventanas, 1 componente); 36, 37 y 40 son la misma `VentanaCampo` (3 ventanas, 1 componente). En total: 12 ventanas = 1 `Ventana` + 6 contenidos.

---

## 6. Alertas y avisos

Cada texto se escribe **una sola vez** en `src/lib/mensajes.js` y se usa por su código (regla 4).

### 6.1 Alertas de error del boceto (en línea, pieza `Alerta`)

| Código | Texto exacto | Dónde aparece |
|---|---|---|
| `ACC-01` | La matrícula o la contraseña son incorrectas. | 1 |
| `ACC-02` | Este usuario ya está registrado. Ingresa otra matrícula. | 3, 24 |
| `FRM-01` | Falta llenar el campo {campo}. | 3, 24, 6, 17 |
| `COM-01` | Escribe un comentario antes de publicarlo. | 20 |
| `NEG-01` | Elige la categoría de tu negocio. | 6 |
| `NEG-02` | Contraseña incorrecta. Inténtalo de nuevo. | 8, 19 |
| `NEG-03` | Solo puedes agregar hasta 3 enlaces por red social. | 17 |
| `PUB-01` | Solo puedes subir hasta 10 imágenes. | 10, 15 |
| `PUB-02` | Completa todos los campos y agrega al menos una imagen. | 10, 15 |
| `CAT-01` | Escribe el nombre de la categoría. | 36, 40 |
| `CAT-02` | Esa categoría ya existe. | 36, 40 |
| `ADM-01` | Escribe la matrícula del usuario. | 37 |
| `ADM-02` | No existe un usuario con esa matrícula. | 37 |
| `ADM-03` | Esa cuenta ya es administrador. | 37 |

### 6.2 Avisos de éxito (pieza `Aviso`, desaparecen solos)

| Código | Texto exacto | Dónde aparece |
|---|---|---|
| `AVI-01` | Negocio eliminado. | 7 (o 5 si ya no le quedan negocios), después de la ventana 19 |
| `AVI-02` | Baja registrada en la bitácora. | 29, 30, 31, 32, 33 y 38, después de la ventana 35 |
| `AVI-03` | Categoría agregada. | 32, después de la ventana 36 |
| `AVI-04` | Categoría actualizada. | 32, después de la ventana 40 |
| `AVI-05` | {Nombre} ahora es administrador. | 33, después de la ventana 37 |

### 6.3 Sugerencias (pieza `Sugerencia`)

| Código | Texto exacto | Dónde |
|---|---|---|
| `SUG-01` | Pulsa una publicación para ver su detalle, sus comentarios y darla de baja. | 29 |
| `SUG-02` | Pulsa un negocio para ver su perfil y darlo de baja. | 30 |
| `SUG-03` | Usa el botón ⋮ de cada fila para dar de baja una cuenta. | 31 |
| `SUG-04` | Al editar una categoría se actualiza en los negocios que la usan. Al eliminarla, esos negocios pasan a «Otro». | 32 |
| `SUG-05` | El administrador maestro no se puede eliminar. Los hijos se dan de baja con el botón ⋮. | 33 |
| `SUG-06` | Pulsa una publicación para ver su reporte detallado. | 44 |

### 6.4 Estados vacíos (pieza `EstadoVacio`)

| Código | Texto exacto | Dónde |
|---|---|---|
| `VAC-01` | Aún no hay publicaciones. Cuando los negocios publiquen, aparecerán aquí. | 2, 4 |
| `VAC-02` | Ninguna publicación coincide con la búsqueda. | 2, 4, 29 |
| `VAC-03` | Ningún negocio coincide con la búsqueda. | 27, 30 |
| `VAC-04` | Todavía no tienes negocios. Añade el primero para empezar a vender. (con botón «Añadir un negocio») | 5 |
| `VAC-05` | Todavía no hay publicaciones. Pulsa «Agregar publicación». | 9 |
| `VAC-06` | Este negocio todavía no tiene publicaciones. | 28, 39 |
| `VAC-07` | Esta publicación no tiene comentarios. | 38 |
| `VAC-08` | Todavía no tienes publicaciones. Cuando las tengas, aquí verás sus resultados. | 41 |
| `VAC-09` | Todavía no tienes publicaciones. | 42 |
| `VAC-10` | Este negocio no tiene publicaciones. | 44 (con un negocio elegido) |
| `VAC-11` | No hay publicaciones. | 29 |
| `VAC-12` | No hay negocios. | 30, 39 |
| `VAC-13` | No hay usuarios. | 31 |
| `VAC-14` | Todavía no hay bajas registradas. | 34 |

Además hay dos textos de dato vacío dentro de la credencial (no son estados vacíos completos): «Este negocio aún no tiene descripción.» y «Sin redes sociales» (28, 39).

### 6.5 Alertas que faltan en el boceto

| Código | Texto propuesto | Cuándo y dónde | Por qué se exige |
|---|---|---|---|
| `SIS-01` | No hay conexión. Revisa tu internet e inténtalo de nuevo. (con botón «Reintentar») | Cualquier página que lea o guarde datos | Entrega 4 · la app depende de Supabase (ADR-001) |
| `PUB-03` | Ese archivo no es una imagen. Elige una foto JPG, PNG o WebP. | 10, 15, 17, 24 al elegir un archivo | Entrega 4 · ADR-004 (las imágenes se comprimen en el navegador) |
| `SIS-02` | Tu sesión terminó. Inicia sesión de nuevo. | Al usar la app con la sesión vencida; lleva a `IniciarSesion` | Entrega 4 · ADR-003 (permisos verificados con la sesión) |
| `SIS-03` | No encontramos esta página. (con botón «Ir al catálogo») | Página `NoEncontrada` | Entrega 4 · ADR-007 (rutas de la SPA) |
| `FOT-01` | No pudimos usar la cámara. Revisa el permiso o sube una foto. | Ventana 26 (hoy el boceto falla en silencio) | Entrega 4 · alcance (los vendedores suben fotos desde el celular) |
| `SIS-04` | No tienes permiso para hacer esto. | Cuando la base de datos rechaza una acción por RLS | Recomendada · ADR-003 |
| `SIS-05` | No se pudo guardar. Inténtalo de nuevo. | Cualquier formulario si falla el guardado (incluida la subida de imágenes) | Recomendada · ADR-004 |

---

## 7. Piezas y reciclaje

Responde «qué se modulariza». Los nombres coinciden con el catálogo de [05-reglas-css.md](../05-vista/05-reglas-css.md) (archivo `kebab-case`, componente `PascalCase`).

### 7.1 Piezas que se reutilizan (42)

| Pieza | Qué hace | Variantes | Páginas donde se reutiliza |
|---|---|---|---|
| `Pagina` | Fondo, ancho y relleno | portada | Todas |
| `BarraSuperior` | Barra de navegación | catálogo, detalle, perfil, negocio, administrador, administrador-detalle | 14 páginas (todas menos 1, 2, 8, 9 y 10) |
| `Marca` | Logo y «Wissen» | tinta | Todas las barras, `IniciarSesion` |
| `Pestanas` | Cambiar de sección | separador, segmento, administrador | `Catalogo`, `ListadoNegocios`, `PerfilNegocio`, las 7 páginas del negocio, `PanelAdministrador` |
| `Ruta` | Migas de pan | — | `DetallePublicacion`, `PerfilNegocio`, `ReportePublicacion` |
| `BarraVolver` | Volver + ruta | — | `PerfilNegocio`, `ReportePublicacion` |
| `EncabezadoSeccion` | Título con marcatextos + apoyo + acción | — | `Catalogo`, `ListadoNegocios`, `PanelNegocio`, `ReportesNegocio`, `PanelAdministrador` |
| `Hoja` | Hoja de cuaderno con margen rojo | — | `DetallePublicacion`, `DetallePublicacionNegocio` |
| `Boton` | Toda acción | primario, confirmar, peligro, opciones, secundario, suave, contorno, contorno-peligro, chico, ancho, texto | Todas |
| `Campo` | Dato con etiqueta | texto, contraseña, número, área, lista | 8 páginas con formulario y 4 ventanas |
| `Buscador` | Cuadro con lupa | — | `Catalogo`, `ListadoNegocios`, `PanelAdministrador` |
| `BarraFiltros` | Buscar, categoría, tipo, contador, limpiar | con tipo | `Catalogo`, `ListadoNegocios`, `PanelAdministrador` |
| `Opcion` | Radio de una sola elección | acción, peligro | `FormularioPublicacion`, ventana 18 |
| `SelectorImagenes` | Añadir, ver y quitar hasta 10 imágenes | — | `FormularioPublicacion` (crear y modificar) |
| `FotoEditable` | Avatar con menú de foto | — | `EditarPerfil`, `EditarNegocio` |
| `MenuOpciones` | Menú flotante | — | `MiPerfil`, `EditarPerfil`, `EditarNegocio`, `PanelAdministrador` |
| `Estrellas` | Calificación | lectura, selección | `DetallePublicacion`, `DetallePublicacionNegocio`, `Comentario` |
| `Ordenar` | Más recientes / Más antiguos | — | `DetallePublicacion`, `DetallePublicacionNegocio` |
| `SelectorMes` | Mes del reporte | — | `ReportesNegocio`, `ReportePublicacion`, `PanelAdministrador` |
| `Tarjeta` | Contenedor blanco | — | Formularios, perfiles, configuración, reportes |
| `TarjetaPublicacion` | Ficha con cinta | nueva | `Catalogo`, `PanelNegocio`, `PerfilNegocio` (público y administrador) |
| `FotoNegocio` | Foto o ícono de tienda | círculo, credencial | `ListadoNegocios`, `PerfilNegocio`, `IngresoNegocio` |
| `Credencial` | Cabecera del negocio | — | `PerfilNegocio` (público y administrador) |
| `Galeria` | Foto instantánea, contador y miniaturas | — | `DetallePublicacion` (3 modos), `DetallePublicacionNegocio` |
| `Precio` | Precio en círculo rojo | — | `DetallePublicacion`, `DetallePublicacionNegocio` |
| `Etiqueta` | Dato corto | categoría, tipo, rol | Detalles, `PerfilNegocio`, `ConfiguracionNegocio`, `PanelAdministrador`, `ReportePublicacion` |
| `DatoContacto` | Ícono + dato (Vendido por, contacto) | botón, resaltado | `DetallePublicacion`, `FormularioPublicacion` |
| `Comentario` | Un comentario | propio, administrador | `DetallePublicacion`, `DetallePublicacionNegocio` |
| `FormularioComentario` | Calificar y comentar | invitado | `DetallePublicacion` (visitante y usuario) |
| `FilaLista` | Fila del administrador | abre | `PanelAdministrador` (6 secciones) |
| `ZonaBaja` | Recuadro para eliminar | — | `DetallePublicacion` y `PerfilNegocio` (modo administrador) |
| `Avatar` | Foto del usuario | chico, normal, grande | Barras, `MiPerfil`, `EditarPerfil`, `ConfiguracionNegocio`, `Comentario`, `PanelAdministrador` |
| `Icono` | Ícono Lucide | chico, normal, grande | Todas |
| `Alerta` | Error en línea | — | Todos los formularios y ventanas con campo |
| `Aviso` | Éxito que desaparece | — | `MiPerfil`, `PanelAdministrador`, `DetallePublicacion` (modo administrador) |
| `Sugerencia` | Ayuda morada | — | `PanelAdministrador` (6 secciones) |
| `EstadoVacio` | «Aún no hay…» y «sin resultados» | con botón | 8 páginas (sección 6.4) y `NoEncontrada` |
| `Cargando` | Indicador de carga | — | Todas las páginas que esperan datos |
| `Ventana` | Ventana emergente | confirmación, campo, opciones, elección, foto, cámara | 12 ventanas desde 8 páginas |
| `Indicador` | Cifra de reporte | — | `ReportesNegocio`, `ReportePublicacion`, `PanelAdministrador` |
| `GraficaBarras` | Barras horizontales | — | `ReportesNegocio`, `PanelAdministrador` |
| `TablaReporte` | Tabla de resultados | con negocio | `ReportesNegocio`, `PanelAdministrador` |

### 7.2 Piezas de una sola página (6)

Pueden quedar dentro de su página hasta que se repitan: `Libreta` (`IniciarSesion`), `TarjetaNegocio` (`ListadoNegocios`), `FilaNegocio` (`MiPerfil`), `BloqueRed` (`EditarNegocio`), `BotonRed` (`ConfiguracionNegocio`) y `GraficaDias` (`ReportePublicacion`).

---

## 8. Dónde cae cada cosa

Según las capas del [ADR-002](../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md):

| Carpeta | Qué va | Contenido concreto |
|---|---|---|
| `src/pages/` | Una página por fila de la sección 3 | 19 archivos: `IniciarSesion.jsx` … `NoEncontrada.jsx` |
| `src/components/estructura/` | Estructuras de la sección 4 | `BarraSuperior`, `Pestanas`, `Ruta`, `BarraVolver`, `EncabezadoSeccion`, `Pagina`, `Hoja`, `Marca` |
| `src/components/` | Piezas de la sección 7 | Una carpeta o archivo por pieza; las de formulario pueden ir en `components/formulario/` y las de reportes en `components/reportes/` si la carpeta crece |
| `src/services/` | Todo lo que habla con Supabase | `auth` (entrar, registrar, salir), `usuarios`, `negocios` (incluye verificar su contraseña), `publicaciones`, `imagenes` (comprimir y subir, ADR-004), `calificaciones`, `comentarios`, `categorias`, `administracion` (dar rol, bajas, bitácora), `reportes` (contadores, ADR-006) |
| `src/context/` | `AuthContext` | Usuario, rol, y qué negocio tiene abierto (ver decisión 1 de la sección 9) |
| `src/routes/` | Rutas y `RutaProtegida` | Rutas de la sección 3 y cuatro niveles de acceso: visitante, usuario, vendedor con negocio abierto, administrador |
| `src/lib/` | Configuración | Cliente de Supabase y `mensajes.js` con los códigos de la sección 6 |
| `src/styles/` | Estilos | `00-variables.css`, `01-base.css`, `02-estructura.css`, `piezas/`, `paginas/` (ver [05-reglas-css.md](../05-vista/05-reglas-css.md)) |
| `public/` | Fuentes e íconos estáticos | Si no se cargan de Google Fonts; los íconos vienen de `lucide-react` |

---

## 9. Decisiones sobre los pendientes

Resueltas el 26 de noviembre de 2026. Ya no quedan pendientes abiertos.

### 9.1 Decisión previa: enlaces de redes sociales

El formulario (`BloqueRed`, pantalla 17) sigue permitiendo guardar hasta 3 enlaces por red social (`NEG-03`), pero **el sistema solo muestra el primero** de cada red (`BotonRed`, pantallas 16, 28 y 39). Motivo: tiempo de desarrollo y documentación; reestructurar el modelo costaría más de lo que aporta. Los enlaces adicionales se guardan y no se muestran.

### 9.2 Decisiones

| # | Tema | Decisión | Qué implica |
|:-:|---|---|---|
| 1 | Dónde se guarda el «negocio abierto» | Vive en `AuthContext`, por seguridad | Solo en memoria (no en `localStorage`). Al recargar la página se vuelve a pedir la contraseña del negocio; también al pulsar «Salir del negocio» o al cerrar sesión *(duración supuesta, confirmar con el equipo)* |
| 2 | Buscador en Categorías y Administradores | **Se omite** | Esta fuera del alcance Agregar `Buscador` a las pantallas 32 y 33 (prevalece el alcance) |
| 3 | «Carrera» o «Licenciatura» | Se usa **«Carrera»** | Boceto actualizado (etiqueta del campo, perfil y mensaje `FRM-01`). Documento de usuarios ya decía «carrera» |
| 4 | Cuántas publicaciones se cargan | **Máximo 10** por petición | El catálogo pide 10 a la vez y trae más al desplazarse; mientras carga se muestra `Cargando` |
| 5 | Orden del listado de negocios | Se respeta: orden de registro | Sin cambios |
| 6 | Ventanas desde el teclado | **Se omite** | Solo afecta a quien navega con teclado o lector de pantalla. El foco no se mueve al abrir ni al cerrar una ventana. Queda como limitación conocida |
| 7 | Recuperar contraseñas | **Se omite** | Fuera del alcance por complejidad y tiempo. Queda como limitación conocida |
| 8 | Foto mientras se sube | **Se omite** | Sin estado visual de «subiendo imagen» |
| 9 | Miniaturas de «Secuencias y navegación» | Se actualiza | **Hecho:** las 22 imágenes de diagramas de `01-Secuencias-y-navegacion.docx` se regeneraron con capturas del boceto refinado (nombre «Wissen») |
