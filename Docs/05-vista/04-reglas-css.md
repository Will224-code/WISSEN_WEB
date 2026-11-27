# 05 · Reglas de CSS

**Estado:** Entregado · **Se basa en:** [03-bases-del-diseno.md](03-bases-del-diseno.md) y el [boceto refinado](boceto-refinado/boceto-refinado.html)
**Para:** quien programe los estilos en React (carpeta `src/styles/` del [ADR-002](../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md))

Este documento describe cómo quedó el boceto refinado y cómo se divide su estilo en piezas. La regla central: **una pieza que se repite es una sola pieza**, con un solo archivo de estilo, aunque aparezca en muchas pantallas. Por ejemplo, la ficha de publicación aparece en el catálogo, el panel del negocio, el perfil público del negocio y el perfil del negocio del administrador, y es **un solo** `tarjeta-publicacion.css`.

> El boceto es una maqueta en un solo archivo y por eso usa nombres de clase cortos (`.pcard`, `.btn`). La tabla del punto 5 relaciona cada clase del boceto con el **nombre definitivo** que se usará en React.

---

## 1. Principios

1. Todo valor sale de una variable de `00-variables.css`; no hay números sueltos de color, letra, espacio, esquina ni sombra.
2. Una pieza = un archivo = un componente de React, con el mismo nombre.
3. Las piezas no saben dónde están: se ven igual en cualquier página; la página solo decide dónde colocarlas.
4. Las variantes viven dentro de su pieza (`boton--peligro` está en `boton.css`).
5. Se diseña primero para el celular y se agregan reglas para pantallas más anchas.

---

## 2. Colores, tipografía, tamaños, espacios y formas

Son los de las [bases del diseño](03-bases-del-diseno.md), sin cambios al refinar. Así quedan en `00-variables.css`:

| Grupo | Variables |
|---|---|
| Colores | `--c-tinta`, `--c-azul`, `--c-azul-oscuro`, `--c-azul-claro`, `--c-amarillo`, `--c-amarillo-claro`, `--c-menta`, `--c-verde`, `--c-verde-claro`, `--c-rojo`, `--c-rojo-claro`, `--c-morado`, `--c-morado-claro`, `--c-papel`, `--c-cuadricula`, `--c-campo`, `--c-linea`, `--c-suave`, `--c-blanco` |
| Transparencias | `--c-velo`, `--c-velo-claro`, `--c-cinta`, `--c-blanco-transp` |
| Fuentes | `--f-titulos` (Baloo 2), `--f-texto` (Nunito) |
| Tamaños de letra | `--tam-etiqueta` 12, `--tam-apoyo` 14, `--tam-cuerpo` 16, `--tam-subtitulo` 18, `--tam-seccion` 22, `--tam-pantalla` 28, `--tam-detalle` 36, `--tam-portada` 48 |
| Pesos | `--peso-normal` 400, `--peso-medio` 600, `--peso-fuerte` 700, `--peso-extra` 800 |
| Interlineado | `--interlineado` 1.5, `--interlineado-titulo` 1.15 |
| Espacios | `--esp-1` 4, `--esp-2` 8, `--esp-3` 12, `--esp-4` 16, `--esp-5` 24, `--esp-6` 32, `--esp-7` 48 |
| Esquinas | `--rad-ficha` 6, `--rad-control` 10, `--rad-tarjeta` 16, `--rad-ventana` 20, `--rad-pildora` 999 |
| Borde y sombras | `--borde` 2 px, `--sombra-relieve`, `--sombra-tarjeta`, `--sombra-flotante` |
| Tamaños | `--alto-control` 44, `--alto-control-chico` 36, `--ancho-pagina` 1100, `--avatar-chico` 32, `--avatar` 40, `--avatar-grande` 112, `--foto-negocio` 128, `--icono-chico` 16, `--icono` 20, `--icono-grande` 28, `--cuadricula` 24 |

**Lo único que cambió al refinar:** se agregaron `--foto-negocio` (la foto del listado y de la credencial ya medía distinto que el avatar grande) y `--c-blanco-transp` (pestañas sobre la barra morada).

---

## 3. Puntos de quiebre

Las media queries no aceptan variables, así que estos dos números se escriben tal cual y **solo estos**:

| Nombre | Regla | Ancho de referencia | Qué cambia |
|---|---|---|---|
| Móvil (base) | sin media query | 375 px | Estilo base: una columna en formularios y detalles, fichas en 2 columnas, filtros a todo lo ancho, pestañas en fila sin colgar, libreta de inicio sin espiral |
| Tableta | `@media (min-width: 600px)` | 768 px | Formularios y detalles en 2 columnas; pestañas de separador colgando de la barra; negocios en 3 columnas; indicadores de reporte en 2 |
| Escritorio | `@media (min-width: 1024px)` | 1280 px | Fichas en 3 columnas, negocios en 4, indicadores en 4, página centrada de 1100 px máximo |

> El boceto está escrito «de escritorio hacia abajo» (`max-width: 1023px` y `max-width: 599px`) porque partió del boceto anterior. En React se escribe **de móvil hacia arriba** con `min-width`, que da el mismo resultado.

---

## 4. Convención de nombres

| Regla | Ejemplo |
|---|---|
| Español, minúsculas, palabras separadas con guion; sin acentos ni ñ | `tarjeta-publicacion`, `pestanas`, `boton` |
| El nombre de la pieza es el del archivo y el del componente | `tarjeta-publicacion.css` ↔ `TarjetaPublicacion.jsx` |
| Parte interna de la pieza: `pieza__parte` | `tarjeta-publicacion__precio`, `ventana__acciones` |
| Variante: `pieza--variante` (se agrega a la clase base, no la reemplaza) | `boton boton--peligro`, `tarjeta-publicacion--nueva` |
| Estado: atributos o pseudoclases antes que clases | `:hover`, `:focus-visible`, `:disabled`, `[aria-invalid="true"]`, `[aria-selected="true"]` |
| Estado que no tiene atributo: `es-` | `es-activa` (pestaña), `es-elegida` (miniatura) |
| Variables: prefijo por grupo | `--c-` color, `--tam-` letra, `--esp-` espacio, `--rad-` esquina |

---

## 5. Catálogo de piezas

«Pantallas» usa la numeración del boceto. «Clase en el boceto» es lo que hay hoy en el HTML refinado.

### 5.1 Estructura de página

| Pieza | Para qué sirve | Variantes | Estados | Pantallas | Clase en el boceto |
|---|---|---|---|---|---|
| `pagina` | Fondo de papel cuadriculado, ancho máximo y relleno | `--portada` (fondo azul de Inicio de sesión) | — | Todas | `#screen`, `.wrap`, `.portada` |
| `barra-superior` | Franja superior con marca, navegación y acceso a la cuenta | `--catalogo` (marca + pestañas + sesión), `--detalle` (Volver + logo + sesión), `--perfil` (logo + «Mi perfil» + avatar), `--negocio` (foto y nombre del negocio + pestañas), `--administrador` (morada, con pestañas del panel), `--administrador-detalle` (morada, Volver + título + Salir) | — | 2, 4, 27, 28 · 20, 21 · 5, 7 · 9–12, 15–17, 41–43 · 29–34, 39, 44 · 38 | `.top`, `.top.adm`, `.top.adm.panel` |
| `marca` | Logo de libreta y nombre «Wissen» | `--tinta` (sobre papel, en la portada) | — | Barras, 1 | `.marca`, `.logo` |
| `pestanas` | Cambiar de sección | `--separador` (cuelgan de la barra azul, colores amarillo, menta y rosa), `--segmento` (dentro del contenido), `--administrador` (sobre la barra morada, con contador) | `es-activa`, `:hover`, `:focus-visible` | Separador: 2, 4, 27, 28, 9–17, 41–43 · Segmento: 41, 42 · Administrador: 29–34, 39, 44 | `.tabs`, `.top .tabs`, `.top.adm .tabs` |
| `ruta` | Ruta de navegación «Catálogo › Producto» | — | Enlaces con `:hover` y `:focus-visible` | 20, 21, 28, 38, 39, 43 | `.adm-crumb` |
| `barra-volver` | Botón «Volver» con la ruta a su lado | — | — | 28, 43 | `.barra-volver` |
| `encabezado-seccion` | Título con marcatextos, frase de apoyo y acción a la derecha | — | — | 2, 4, 9, 27, 29–34, 41, 42, 44 | `h2.t`, `.adm-head` |
| `hoja` | Hoja de cuaderno con margen rojo que agrupa galería y datos | — | — | 12, 20, 21, 38 | `.hoja` |
| `libreta` | Libreta abierta con espiral (solo Inicio de sesión) | — | — | 1 | `.libreta`, `.libreta-hoja`, `.libreta-espiral`, `.libreta-form` |

### 5.2 Controles

| Pieza | Para qué sirve | Variantes | Estados | Pantallas | Clase en el boceto |
|---|---|---|---|---|---|
| `boton` | Toda acción | `--primario` (azul), `--confirmar` (verde), `--peligro` (rojo), `--opciones` (morado), `--secundario` (amarillo), `--suave` (verde claro), `--contorno` (blanco, borde azul), `--contorno-peligro` (blanco, borde rojo), `--chico` (36 px), `--ancho` (100 %), `--texto` (sin fondo) | `:hover`, `:focus-visible`, `:active`, `:disabled` | Todas | `.btn`, `.green`, `.pink`, `.purple`, `.light`, `.lgreen`, `.out`, `.outpink`, `.sm`, `.lnk` |
| `campo` | Escribir o elegir un dato, con su etiqueta | `--texto`, `--contrasena`, `--numero`, `--area` (con renglones), `--lista` (desplegable) | `:hover`, `:focus-visible`, `:disabled`, `[aria-invalid="true"]` | 1, 3, 6, 8, 10, 15, 17, 19, 20, 24, 36, 37, 40 | `label.f`, `.in`, `textarea.in`, `select.in` |
| `buscador` | Cuadro de búsqueda con lupa | — | Igual que `campo` | 2, 4, 27, 29, 30, 31, 34 | `input.adm-search` |
| `barra-filtros` | Buscador + categoría + tipo + contador + «Limpiar filtros» | `--con-tipo` (agrega Todos/Productos/Servicios) | Filtro `es-activa` | 2, 4, 27, 29, 30 | `.bfbar`, `.chips` |
| `opcion` | Opción de una sola elección (radio) | `--accion` (azul: Producto/Servicio), `--peligro` (roja: elegir negocio a eliminar) | `[aria-checked="true"]`, `:focus-visible` | 10, 15, 18 | `.radio`, `.radio.blue` |
| `selector-imagenes` | Zona para añadir imágenes, cuadros de miniatura y botón «+» | — | Miniatura `es-elegida` | 10, 15 | `.drop`, `.slots`, `.slot` |
| `foto-editable` | Avatar grande con insignia de cámara que abre el menú de foto | — | `:focus-visible` | 17, 24 | `photoBtn()` |
| `menu-opciones` | Menú flotante pequeño | — | Opción `--peligro` en rojo | 7, 17, 24, 31, 33 | `.menu` |
| `estrellas` | Calificación con 5 estrellas | `--lectura`, `--seleccion` (se pulsan, 28 px) | Estrella rellena / vacía, `:focus-visible` | 12, 20, 21, 38 | `.stars`, `.stars.click` |
| `ordenar` | Lista «Más recientes / Más antiguos» | — | Igual que `campo` | 12, 20, 21, 38 | `.csort` |
| `selector-mes` | Lista del mes en reportes | — | Igual que `campo` | 41–44 | `monthSel()` |

### 5.3 Contenido

| Pieza | Para qué sirve | Variantes | Estados | Pantallas | Clase en el boceto |
|---|---|---|---|---|---|
| `tarjeta` | Recuadro blanco con borde y sombra sólida (contenedor general) | — | — | 5, 7, 8, 16, 17, 41–44 y formularios | `.card` |
| `tarjeta-publicacion` | Ficha con cinta: imagen, nombre y precio | `--nueva` (fondo amarillo claro y etiqueta «Nueva») | `:hover` (sube), `:focus-visible` | 2, 4, 9, 11, 28, 39 | `.pcard`, `.pcard.new`, `.badge` |
| `tarjeta-negocio` | Foto circular, nombre y categoría | — | `:hover`, `:focus-visible` | 27 | `.bcard` |
| `foto-negocio` | Foto del negocio o ícono de tienda | `--circulo` (listado), `--credencial` (rectangular) | — | 27, 28, 39 | `.bpic`, `.bpic.big` |
| `credencial` | Cabecera del negocio con forma de credencial escolar | — | — | 28, 39 | `.credencial` |
| `galeria` | Foto instantánea con cinta, contador «1 / 3» y miniaturas | — | Miniatura `es-elegida`, `:focus-visible` | 12, 20, 21, 38 | `.galeria`, `.thumbs`, `.counter` |
| `precio` | Precio rojo en círculo, como corrección del maestro | — | — | 12, 20, 21, 38 | `.precio` |
| `etiqueta` | Dato corto en recuadro | `--categoria` (menta), `--tipo` (azul claro), `--rol` (tinta o morado, píldora) | — | 12, 16, 20, 21, 28, 29, 30, 33, 38, 39, 43 | `.chip`, `.chip--menta`, `.tag` |
| `dato-contacto` | Ícono en cuadro + texto: «Vendido por», contacto, contacto automático | `--boton` (toda la fila se pulsa: «Ver negocio»), `--resaltado` (ícono en amarillo) | `:hover`, `:focus-visible` | 10, 15, 20, 21, 38 | `.info`, `.ic`, `.vbtn` |
| `comentario` | Avatar, nombre, estrellas, fecha y texto | `--propio` (con Editar y Eliminar), `--administrador` (con Eliminar) | — | 12, 20, 21, 38 | `.cmt` |
| `formulario-comentario` | Tu calificación + cuadro de comentario + «Comentar» | `--invitado` (bloqueado con candado) | — | 20, 21 | bloque de `detailPublic()` |
| `fila-negocio` | Fila con foto y nombre del negocio en el perfil | `--agregar` (borde punteado «Añadir un negocio») | `:hover`, `:focus-visible` | 7 | `.bizrow`, `.bizrow.add` |
| `fila-lista` | Fila de las listas del administrador | `--abre` (muestra «Ver detalle ›» al pasar el cursor) | `:hover`, `:focus-visible` | 29–34 | `.arow`, `.arow.click` |
| `zona-baja` | Recuadro rojo punteado con el botón de eliminar | — | — | 38, 39 | `.danger` |
| `bloque-red` | Grupo de hasta 3 enlaces de una red social | — | — | 17 | `.netblock` |
| `boton-red` | Botón circular de red social | sin enlace (transparente) | `:hover`, `:focus-visible` | 16 | `.social` |
| `avatar` | Foto o figura del usuario | `--chico` 32, normal 40, `--grande` 112 | — | 4, 5, 7, 12, 16, 17, 20, 24, 31, 33, 38 | `.avatar`, `.avatar.big` |
| `icono` | Ícono Lucide de trazo 2 px | `--chico` 16, normal 20, `--grande` 28 | Toma el color del texto | Todas | `.ico`, `ic()` |

### 5.4 Mensajes y estados

| Pieza | Para qué sirve | Variantes | Estados | Pantallas | Clase en el boceto |
|---|---|---|---|---|---|
| `alerta` | Error en línea, debajo del formulario | — | — | 1, 3, 6, 8, 10, 15, 17, 19, 20, 24, 36, 37, 40 | `.alert` |
| `aviso` | Mensaje breve de éxito que desaparece solo | — | — | 7, 29–33 | `.toast` |
| `sugerencia` | Ayuda con foco de idea | — | — | 29–33, 44 | `.adm-hint` |
| `estado-vacio` | Un solo diseño para «sin resultados» y «aún no hay…» | ícono de bandeja o de búsqueda, con o sin botón | — | 2, 4, 5, 9, 27, 28, 29, 30, 31, 34, 38, 39, 41, 42, 44 | `.vacio` |
| `cargando` | Indicador de carga sobre el contenido | — | Gira; se detiene si se pide reducir el movimiento | Todas (en el boceto, en «Estados» del panel lateral) | `.cargando` |
| `ventana` | Ventana emergente sobre un velo oscuro | `--confirmacion` (ícono + título + texto + 2 botones), `--campo` (con un campo), `--opciones` (3 botones), `--eleccion` (lista de opciones), `--foto`, `--camara` | Se cierra con Escape o pulsando el velo | 13, 14, 18, 19, 22, 23, 25, 26, 35, 36, 37, 40 | `.ov`, `.modal`, `.mic`, `.cam` |

### 5.5 Reportes

| Pieza | Para qué sirve | Variantes | Estados | Pantallas | Clase en el boceto |
|---|---|---|---|---|---|
| `indicador` | Cifra grande con su ícono y nombre | — | — | 41, 43, 44 | `.kpis`, `.kpi` |
| `grafica-barras` | Barras horizontales «más clics en WhatsApp» | — | — | 41, 44 | `.hbar` |
| `grafica-dias` | Barras por día con leyenda | — | — | 43 | `.dbars`, `.legend` |
| `tabla-reporte` | Tabla de publicaciones con desplazamiento propio | `--con-negocio` (columna extra en el administrador) | Fila `:hover`, `:focus-visible` | 42, 44 | `.tabla-scroll`, `.rtable` |

---

## 6. Cómo se organizan los archivos de estilo

Coincide con la carpeta `styles/` del [ADR-002](../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md).

```
src/styles/
├── 00-variables.css        Todas las variables del punto 2
├── 01-base.css             Fuentes, html/body, enlaces, títulos, foco, reducir movimiento
├── 02-estructura.css       pagina, rejillas de 1-2-3-4 columnas, puntos de quiebre de las rejillas
├── piezas/                 Un archivo por pieza del punto 5, mismo nombre
│   ├── boton.css
│   ├── campo.css
│   ├── tarjeta-publicacion.css
│   ├── ventana.css
│   └── ... (resto de piezas)
└── paginas/                Solo lo que no es reutilizable
    ├── iniciar-sesion.css   (libreta abierta)
    └── panel-administrador.css (solo si hace falta)
```

**Orden de carga** (en `main.jsx`): `00-variables` → `01-base` → `02-estructura` → piezas → páginas. Cada componente importa su propio archivo de `piezas/`; como las piezas no dependen entre sí, su orden no importa.

| Archivo | Puede usar | No puede usar |
|---|---|---|
| `00-variables.css` | Solo `:root` | Selectores de elementos |
| `01-base.css` | Etiquetas (`body`, `h1`, `a`, `:focus-visible`) | Clases de piezas |
| `02-estructura.css` | Clases de estructura | Colores de acción |
| `piezas/*.css` | Solo las clases de su pieza | Clases de otra pieza |
| `paginas/*.css` | Colocar piezas (márgenes, columnas) | Cambiar el aspecto de una pieza |

---

## 7. Reglas de modularidad

1. **Una pieza = un archivo.** `tarjeta-publicacion.css` solo tiene reglas que empiezan con `.tarjeta-publicacion`.
2. **Una pieza no depende de otra.** Si una ficha lleva un botón, el botón trae su propio estilo; la ficha no lo modifica.
3. **Las variantes se definen dentro de su pieza.** No se crea `boton-rojo.css`; se agrega `.boton--peligro` en `boton.css`.
4. **No se usa `!important`.** Si algo no gana, se revisa el orden de carga o la variante.
5. **No se estiliza por identificador** (`#algo`) **ni por etiqueta dentro de una pieza** (`.tarjeta div`); siempre por clase.
6. **Sin estilos en línea en React**, salvo valores que vienen de datos (por ejemplo, el ancho de una barra de reporte).
7. **La página coloca, la pieza decide su aspecto.** Una página puede decir «tres columnas»; no puede cambiar el color de un botón.
8. **Si una pieza aparece en 2 o más pantallas, se reutiliza.** Si cambia poco, es una variante; si cambia mucho, es otra pieza con otro nombre.

---

## 8. Qué no se hace

- Números sueltos de color, letra, espacio, esquina o sombra (todo sale de variables).
- Emojis como íconos (se usa Lucide).
- Librerías de estilos (Tailwind, Bootstrap) o modo oscuro.
- Animaciones largas: solo transiciones de 0.15 s y el giro de «Cargando».
- Quitar el contorno de foco (`outline: none`) sin poner otro igual de visible.
- Texto amarillo, menta o gris claro sobre blanco o papel.
- Elementos que se pulsan hechos con `div` sin `role`, `tabindex` y respuesta a Enter (mejor usar `button` o `a`).

---

## 9. Pendientes

| Pendiente | Detalle |
|---|---|
| Foco después de cada acción | El boceto redibuja toda la pantalla al pulsar, así que el foco del teclado vuelve al inicio. En React no pasa si cada componente conserva su estado; revisar al programar. |
| Fichas y filas como `div` | En el boceto se volvieron enfocables por código. En React deben ser `button` o `a`. |
| Nombres de clase del boceto | Siguen cortos (`.pcard`, `.btn`); el nombre definitivo es el de este documento. |
| Fotos reales | Las ilustraciones de colores son solo de ejemplo; falta definir cómo se ve una foto que todavía está subiendo. |
| Estados vacíos nuevos | Faltan textos para los errores que el boceto no muestra (ver documento de desarrollo, sección 6). |
| Indicador de carga | En el boceto solo se ve como demostración desde el panel lateral; en React se mostrará mientras responde Supabase. |
