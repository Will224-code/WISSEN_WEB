# 04 · Bases del diseño

**Estado:** Entregado · **Aplica a:** las 44 pantallas del [boceto refinado](boceto-refinado/index.html) y a la aplicación en React
**Identidad:** diseño «Cuaderno», que eligió el equipo el 25 de noviembre de 2026 entre tres propuestas. Reemplaza los colores y la fuente Figtree del primer boceto; los documentos [00-Componentes de pantallas](00-Componentes-de-pantallas.docx) y [01-Secuencias y navegación](01-Secuencias-y-navegacion.docx) ya usan este diseño.

Este documento fija **todas** las decisiones visuales. Si un valor no está aquí, no se usa. Los nombres entre comillas invertidas (por ejemplo `--c-azul`) son los nombres de las variables que ya usa el boceto refinado y que usará el CSS de React.

---

## 1. Principios

1. **Parece una libreta escolar, pero se usa como una aplicación.** El papel cuadriculado, la cinta y el marcatextos dan identidad; nunca tapan la información ni la acción principal.
2. **Un color, un significado.** Azul es actuar, verde es confirmar, rojo es eliminar o error, morado es administrador. No se usan para decorar.
3. **Todo sale de una escala.** Tamaños de letra, espacios, esquinas y sombras se eligen de las tablas de este documento, nunca a ojo.
4. **Pensado primero para el celular.** Los vendedores publican y toman fotos desde el teléfono: cada control mide al menos 44 px de alto y nada se sale de la pantalla a 375 px.
5. **Se puede usar sin ratón.** Todo lo que se pulsa se alcanza con la tecla Tab y muestra un foco visible.

---

## 2. Colores

### 2.1 Colores de marca y de acción

| Nombre | Variable | Valor | Para qué sirve | Dónde **no** usarlo |
|---|---|---|---|---|
| Tinta | `--c-tinta` | `#16224A` | Texto principal, bordes de tarjetas y fichas, sombras sólidas, contador de imágenes, avisos | Fondos grandes (salvo la cámara) |
| Azul | `--c-azul` | `#1F4FB8` | Acción principal (un solo botón azul relleno por bloque), barra superior, enlaces, foco del teclado, filtro activo | Texto decorativo, íconos sin acción |
| Azul oscuro | `--c-azul-oscuro` | `#163A8A` | Solo la sombra de la libreta en Inicio de sesión | Texto, botones |
| Azul claro | `--c-azul-claro` | `#DCE6FF` | Fondo de etiquetas, de miniaturas y de filas al pasar el cursor | Texto |
| Amarillo (marcatextos) | `--c-amarillo` | `#FFE45C` | Subrayado de títulos, acción secundaria («Invitado», «Comentar» con candado), pestaña «Catálogo», avatar, estrellas | Texto sobre blanco (no se lee), errores |
| Amarillo claro | `--c-amarillo-claro` | `#FFF6C7` | Ficha de la publicación nueva, ilustraciones de ejemplo | Botones |
| Menta | `--c-menta` | `#9FE3C4` | Pestaña «Negocios», etiqueta de categoría en la credencial, notas de la portada | Estados de éxito (para eso es el verde) |
| Verde | `--c-verde` | `#146B44` | Confirmar y guardar: «Aceptar», «Ingresar», «Guardar»; barras de clics en reportes | Acciones que borran |
| Verde claro | `--c-verde-claro` | `#D9F5E8` | Botón suave «Editar información», mensajes de éxito | Texto |
| Rojo | `--c-rojo` | `#C42B21` | Eliminar, cancelar, salir, alertas de error, precio, margen de la hoja | Acción principal, decoración |
| Rojo claro | `--c-rojo-claro` | `#FFE0DB` | Fondo de alertas y de la «Zona de baja», tercera pestaña del negocio | Botones |
| Morado | `--c-morado` | `#5B34C4` | Todo lo del modo administrador: barra, «Ver panel de administrador», «Opciones», etiqueta «Hijo» | Vistas de usuario y de negocio (salvo «Opciones» y «Modificar» en la ventana de opciones) |
| Morado claro | `--c-morado-claro` | `#EEE8FC` | Fondo de las sugerencias del panel de administrador | Texto |

### 2.2 Neutros

| Nombre | Variable | Valor | Para qué sirve | Dónde **no** usarlo |
|---|---|---|---|---|
| Papel | `--c-papel` | `#FFFDF6` | Fondo de toda pantalla y de la hoja del detalle | Dentro de tarjetas (son blancas) |
| Cuadrícula | `--c-cuadricula` | `#E6ECF7` | Líneas del papel (cada 24 px) y renglones de los cuadros de texto grandes | Bordes de controles |
| Campo | `--c-campo` | `#F5F8FE` | Fondo de los cuadros de texto, fondos sutiles, filas al pasar el cursor | Texto |
| Línea | `--c-linea` | `#C9D3E6` | Bordes de controles secundarios, separadores punteados, botón deshabilitado | Texto (no tiene contraste) |
| Texto suave | `--c-suave` | `#4A5478` | Texto de apoyo, ayudas, fechas, etiquetas de campo secundarias | Títulos |
| Blanco | `--c-blanco` | `#FFFFFF` | Tarjetas, ventanas, texto sobre azul, verde, rojo, morado y tinta | Fondo de pantalla (es papel) |

### 2.3 Transparencias (solo estas)

| Variable | Valor | Uso |
|---|---|---|
| `--c-velo` | tinta al 70 % | Fondo oscuro detrás de una ventana emergente |
| `--c-velo-claro` | papel al 88 % | Fondo del indicador de carga |
| `--c-cinta` | amarillo al 85 % | Trozo de cinta adhesiva de las fichas y de la foto del detalle |
| `--c-blanco-transp` | blanco al 16 % | Pestañas inactivas sobre la barra morada |

### 2.4 Ilustraciones de ejemplo

Las imágenes de producto del boceto son figuras de colores que reemplazan a las fotos reales. Solo combinan colores de la paleta (fondo claro + dos figuras). En React se mostrarán las fotos de los vendedores; estas combinaciones solo se usan si una publicación aún no tiene foto.

---

## 3. Tipografía

### 3.1 Fuentes y pesos

| Fuente | Variable | Para qué | Pesos permitidos |
|---|---|---|---|
| **Baloo 2** (Google Fonts) | `--f-titulos` | Títulos, nombre de la app, precios, cifras de reporte, pestañas de separador | 700 (`--peso-fuerte`) y 800 (`--peso-extra`) |
| **Nunito** (Google Fonts) | `--f-texto` | Todo lo demás: texto, botones, campos, tablas | 400 (`--peso-normal`), 600 (`--peso-medio`), 700 (`--peso-fuerte`), 800 (`--peso-extra`) |

No se usan cursivas ni otros pesos. Interlineado: 1.5 para texto (`--interlineado`) y 1.15 para títulos (`--interlineado-titulo`).

### 3.2 Escala cerrada (8 tamaños)

El primer boceto usaba 14 tamaños (11, 12, 13, 14, 15, 17, 18, 20, 22, 24, 26, 28, 30 y 34 px). Quedan 8:

| Variable | Tamaño | Uso | Ejemplos |
|---|---|---|---|
| `--tam-etiqueta` | 12 px | Etiquetas pequeñas, contadores, títulos de columna | «1 / 3», «VENDIDO POR», «Producto» en tablas |
| `--tam-apoyo` | 14 px | Texto de apoyo, botones chicos, filtros, sugerencias, alertas | «6 en total», «Más recientes», ruta de navegación |
| `--tam-cuerpo` | 16 px | Texto normal, campos y botones normales | Descripción, «Iniciar sesión» |
| `--tam-subtitulo` | 18 px | Nombre y precio en la ficha, nombre del negocio en la barra | «Brownies de chocolate · $45» |
| `--tam-seccion` | 22 px | Título de sección dentro de una pantalla y de una ventana emergente, nombre de la app | «Calificaciones», «¿Quieres salir del negocio?» |
| `--tam-pantalla` | 28 px | Título de cada pantalla (con marcatextos) y precio en círculo | «Catálogo», «Agregar publicación» |
| `--tam-detalle` | 36 px | Cifras de reporte y título de portada en el celular | «48 Vistas», «4.7» |
| `--tam-portada` | 48 px | Solo «Bienvenido» en Inicio de sesión (escritorio) | «Bienvenido» |

Regla: **el título de pantalla mide siempre 28 px** y el texto gris de ayuda **siempre 14 px**.

---

## 4. Espaciado

| Variable | Valor | Uso típico |
|---|---|---|
| `--esp-1` | 4 px | Separación entre ícono y texto pequeño, relleno de etiquetas |
| `--esp-2` | 8 px | Entre etiqueta y campo, entre botones chicos, relleno de chips |
| `--esp-3` | 12 px | Entre elementos de una fila, relleno de filas y alertas |
| `--esp-4` | 16 px | Relleno de botones, entre bloques pequeños, márgenes de página en el celular |
| `--esp-5` | 24 px | Relleno de tarjetas y de páginas en escritorio, separación entre tarjetas |
| `--esp-6` | 32 px | Separación vertical entre fichas, margen de la hoja de cuaderno |
| `--esp-7` | 48 px | Relleno de los estados vacíos, margen rojo de la libreta |

**Regla:** todo margen, relleno y separación sale de esta escala (o es 0). El papel cuadriculado mide 24 px (`--cuadricula`), igual que `--esp-5`, para que los bloques caigan sobre la cuadrícula.

---

## 5. Formas

### 5.1 Esquinas

| Variable | Radio | Para qué elementos |
|---|---|---|
| `--rad-ficha` | 6 px | Fichas de publicación, miniaturas, etiquetas de categoría, contador de imágenes |
| `--rad-control` | 10 px | Botones, cuadros de texto, listas, filas, alertas, sugerencias |
| `--rad-tarjeta` | 16 px | Tarjetas, menús, hoja de cuaderno, estados vacíos, indicadores de reporte |
| `--rad-ventana` | 20 px | Ventanas emergentes, credencial del negocio, libreta de inicio, marco de la pantalla |
| `--rad-pildora` | 999 px | Filtros Todos/Productos/Servicios, etiquetas «Maestro»/«Hijo», contadores de pestaña |
| — | 50 % | Solo avatares, fotos circulares de negocio, botones de redes y el círculo del precio |

### 5.2 Bordes y sombras

| Elemento | Valor | Uso |
|---|---|---|
| Borde | `--borde` = 2 px | Único grosor de borde (tarjetas, controles, separadores punteados) |
| `--sombra-relieve` | 0 4px 0, tinta al 35 % | Botones rellenos: parecen «en relieve». Al presionar desaparece |
| `--sombra-tarjeta` | 0 4px 0, tinta sólida | Tarjetas, fichas, ventanas, credencial: sombra dura de cuaderno |
| `--sombra-flotante` | 0 8px 24px, tinta al 18 % | Lo que flota: menús de opciones y la foto instantánea del detalle |

No hay más sombras.

---

## 6. Tamaños de elementos

| Elemento | Variable | Medida |
|---|---|---|
| Botón y campo (alto) | `--alto-control` | 44 px |
| Botón chico, filtro, pestaña de segmento (alto) | `--alto-control-chico` | 36 px |
| Ancho máximo de la página | `--ancho-pagina` | 1100 px |
| Avatar en comentarios | `--avatar-chico` | 32 px |
| Avatar en la barra | `--avatar` | 40 px |
| Avatar del perfil y foto al editar | `--avatar-grande` | 112 px |
| Foto del negocio (listado y credencial) | `--foto-negocio` | 128 px |
| Ícono chico / normal / grande | `--icono-chico` / `--icono` / `--icono-grande` | 16 / 20 / 28 px |
| Imagen de ficha y galería | — | Proporción 4:3, ancho de la columna |
| Miniaturas de galería | — | 64 px de ancho, 4:3 |
| Cuadros de imagen al publicar | — | 56 × 56 px |

---

## 7. Íconos

**Juego elegido:** [Lucide](https://lucide.dev) (licencia ISC), trazo de **2 px**, puntas y esquinas redondeadas, en `currentColor` (toman el color del texto). En React se usará el paquete `lucide-react`. Ningún emoji se usa como ícono.

| Ícono (nombre en el boceto) | Lucide | Dónde aparece |
|---|---|---|
| libreta | `notebook-pen` | Logotipo de Wissen |
| usuario | `user` | Avatar sin foto, ventana de registro |
| tienda | `store` | Foto de negocio sin imagen, «Vendido por», perfil sin negocios |
| atras | `chevron-left` | Botón «Volver» |
| sig / sig-chico | `chevron-right` | «Ver negocio», filas que abren algo, separador de la ruta |
| mas | `plus` | «Agregar publicación», «Añadir un negocio», «Agregar categoría», cuadro «+» de imágenes |
| salir | `log-out` | «Cerrar sesión», «Salir del negocio», «Salir del panel» |
| etiqueta | `tag` | Categoría del negocio |
| candado | `lock` | Calificar y comentar como invitado, categoría «Otro», administrador maestro |
| opciones | `ellipsis-vertical` | Botón de tres puntos (⋮) |
| camara | `camera` | Tomar foto, insignia de la foto de perfil |
| subir | `upload` | Subir foto |
| ver | `eye` | Ver foto, indicador de vistas |
| basura | `trash-2` | Eliminar (publicación, negocio, comentario, usuario, categoría) |
| lapiz | `pencil` | Editar categoría |
| enlace | `link` | Redes sociales |
| telefono | `phone` | Contacto por WhatsApp, indicador de clics |
| mensaje | `message-circle` | Comentarios nuevos, sin comentarios |
| estrella | `star` | Calificaciones (rellena = elegida, vacía = sin elegir) |
| calendario | `calendar` | Fecha de publicación |
| alerta | `triangle-alert` | Alertas de error y ventanas de eliminar |
| idea | `lightbulb` | Sugerencias del panel de administrador |
| cerrar | `x` | Quitar imagen, «Limpiar filtros» |
| imagen | `image` | Zona para añadir imágenes |
| buscar | `search` | Buscadores y «sin resultados» |
| bandeja | `inbox` | Estados vacíos («aún no hay…») |
| cargando | `loader-circle` | Indicador de carga |
| listo | `circle-check` | Avisos de éxito |

---

## 8. Estados

| Estado | Botones | Campos | Enlaces, fichas y filas que se pulsan |
|---|---|---|---|
| **Normal** | Relleno de su color con `--sombra-relieve`; los de contorno, blancos con borde de 2 px | Fondo `--c-campo` con línea inferior azul de 2 px (renglón) | Su aspecto normal |
| **Cursor encima** | Un poco más claros (brillo +10 %); los de contorno toman fondo `--c-campo` | Fondo `--c-azul-claro` | Fichas y tarjetas suben 4 px; filas toman fondo `--c-campo` o `--c-azul-claro`; enlaces se subrayan |
| **Foco con teclado** | Contorno azul de 3 px separado 2 px. Sobre la barra azul o morada el contorno es amarillo | Igual | Igual. Las fichas y filas también reciben el foco y se abren con Enter o Espacio |
| **Presionado** | Bajan 2 px y pierden la sombra | — | — |
| **Deshabilitado** | Fondo `--c-linea`, texto `--c-suave`, sin sombra, cursor de prohibido | Línea inferior `--c-linea` | No se usan deshabilitados |
| **Error** | — | Línea inferior roja y fondo `--c-rojo-claro` en el campo que falla, más la alerta roja con ícono debajo | — |
| **Activo / elegido** | Filtro: fondo azul y texto blanco. Pestaña de separador: más larga y opaca. Opción de radio: borde y punto del color de la acción | — | Miniatura elegida: borde azul de 3 px |

Las animaciones son cortas (0.15 s) y se apagan si el sistema pide reducir el movimiento.

---

## 9. Responsivo

| Ancho de referencia | Rango | Qué cambia |
|---|---|---|
| **Móvil · 375 px** | hasta 599 px | Las rejillas de dos columnas (detalle, formularios, credencial) pasan a una columna. Fichas y tarjetas de negocio en 2 columnas. Pestañas de separador en una fila a todo lo ancho, sin colgar. La libreta de inicio pierde la espiral y queda en una columna; «Bienvenido» baja a 36 px. Filtros y buscadores a todo lo ancho. Tablas con desplazamiento dentro de su propio cuadro. Rellenos de página de 16 px |
| **Tableta · 768 px** | 600 a 1023 px | Se mantienen las columnas del escritorio, salvo: listado de negocios en 3 columnas e indicadores de reporte en 2 |
| **Escritorio · 1280 px** | desde 1024 px | Diseño completo: fichas en 3 columnas, negocios en 4, indicadores en 4, página de máximo 1100 px centrada |

A ningún ancho la página se desplaza hacia los lados.

---

## 10. Accesibilidad

| Regla | Valor |
|---|---|
| Contraste mínimo de texto | **4.5 a 1** (también para textos de 12 px) |
| Tamaño táctil mínimo | **44 × 44 px** en controles principales; 36 px solo en controles chicos que tienen espacio libre alrededor |
| Foco visible | Siempre, en todo lo que se pulsa (ver 8) |
| Etiquetas | Cada campo tiene su etiqueta visible unida al campo; los íconos solos llevan nombre accesible |
| Color | Nunca es la única pista: los errores llevan ícono y texto; el activo cambia de forma o de borde |

### 10.1 Contrastes comprobados

Las dos combinaciones que preocupaban en el boceto anterior (texto suave sobre fondo y rosa sobre rosa claro) ya cumplen con los colores nuevos.

| Uso | Combinación | Contraste |
|---|---|---|
| Texto normal | tinta sobre papel | 15.2 : 1 |
| Texto suave sobre el fondo | suave sobre papel | 7.3 : 1 |
| Texto suave en tarjeta | suave sobre blanco | 7.4 : 1 |
| Ayuda dentro de un campo | suave sobre campo | 7.0 : 1 |
| Botón principal | blanco sobre azul | 7.3 : 1 |
| Botón confirmar | blanco sobre verde | 6.5 : 1 |
| Botón eliminar | blanco sobre rojo | 5.7 : 1 |
| Botón opciones y barra de administrador | blanco sobre morado | 7.7 : 1 |
| Botón secundario y pestaña «Catálogo» | tinta sobre amarillo | 12.1 : 1 |
| Pestaña «Negocios» | tinta sobre menta | 10.5 : 1 |
| Precio y botones de contorno rojo | rojo sobre blanco | 5.7 : 1 |
| Alerta | rojo sobre rojo claro | 4.6 : 1 |
| Botón suave verde | verde sobre verde claro | 5.7 : 1 |
| Sugerencia del administrador | morado sobre morado claro | 6.4 : 1 |
| Enlace | azul sobre papel | 7.2 : 1 |
| Contador y avisos | blanco sobre tinta | 15.4 : 1 |
| Foco sobre la barra azul (no es texto) | amarillo sobre azul | 5.8 : 1 |

**No permitido:** texto amarillo, menta o `--c-linea` sobre blanco o papel (no llegan a 2 : 1).
