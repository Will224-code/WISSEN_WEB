# 03 · Instrucciones: definir el diseño, refinar el boceto y documentarlo

**Para:** compañero encargado de la parte visual
**Tiempo estimado:** 1 día (≈ 7 h)
**Alcance:** solo diseño y documentos. **No se programa en React y no se escribe CSS nuevo ni modular.** El código se hace después, con estos documentos como guía.

## 1. Objetivo

Hoy el [boceto](02-boceto.html) mezcla estilos: unos 14 tamaños de letra distintos, medidas de espacio sueltas, íconos hechos con emojis y botones de alturas diferentes. Antes de pasar a código hay que **definir un solo diseño para toda la aplicación** y dejarlo por escrito.

### 1.1 El problema, con ejemplos

Cada pantalla del boceto se hizo sobre la marcha, y eso se nota:

- Los títulos miden 22, 26, 28 y 30 px según la pantalla, sin una razón.
- Algunos botones son más altos que otros.
- Los íconos son emojis, y cada sistema los dibuja distinto.
- Un mismo espacio entre elementos vale 10 px en un lugar y 12 o 14 px en otro.

Si el equipo de React programara así, copiaría el revoltijo y la aplicación parecería hecha de pantallas de apps distintas. Por eso el diseño se decide **una sola vez, por escrito, antes de programar**.

### 1.2 Cómo se conectan las cuatro entregas

| Entrega | Responde a la pregunta |
|---|---|
| Bases del diseño | ¿Cómo se ve **todo** en la aplicación? (colores, letras, espacios, formas) |
| Boceto refinado | ¿Cómo se ve **aplicado** a las 44 pantallas? Es la referencia final |
| Reglas de CSS | ¿En qué **piezas de estilo** se divide y cómo se llaman? (cómo modularizar los estilos) |
| Documento de desarrollo | ¿En qué **páginas y componentes** se divide? (cómo modularizar la aplicación) |

Si el equipo respeta los cuatro, nadie programa mirando el boceto y adivinando.

## 2. Qué se entrega y en qué orden

| Orden | Entrega | Dónde | Tiempo |
|:-:|---|---|---|
| 1 | **Bases del diseño** (documento corto) | `Docs/05-vista/04-bases-del-diseno.md` | 1 h |
| 2 | **Boceto refinado** (el mismo HTML, mejorado) | `Docs/05-vista/boceto-refinado/index.html` | 4 h |
| 3 | **Reglas de CSS** (documento detallado) | `Docs/05-vista/05-reglas-css.md` | 1 h |
| 4 | **Documento de desarrollo** (páginas y modularización) | `Docs/06-desarrollo/00-documento-de-desarrollo.md` | 1 h |

**Por qué este orden:** si se refina sin reglas, queda el mismo revoltijo pero más bonito. Primero se decide, después se aplica y al final se documenta lo que realmente quedó.

**Lo que no se toca:** las 44 pantallas, su numeración, sus textos y sus flujos. Si algo de un flujo no te convence, avisa al equipo. Si el boceto difiere de [alcance](../01-producto/03-alcance.md) o [usuarios](../01-producto/04-usuarios.md), prevalecen esos documentos.

**Lo que no se agrega:** modo oscuro, librerías de estilos (Tailwind, Bootstrap), animaciones largas ni pantallas nuevas.

---

## 3. Entrega 1 · `04-bases-del-diseno.md`

Documento de decisiones, con tablas y sin código. Cada decisión lleva **qué es, para qué se usa y dónde no se usa**. Partes de lo que ya tiene el boceto; la identidad (colores y fuente) ya está validada, no inventes una nueva.

| Sección | Qué decidir y escribir |
|---|---|
| Principios | 3 a 5 líneas: por ejemplo, claridad, consistencia, pensado para el celular |
| Colores | Tabla con nombre, valor, **para qué sirve** y **dónde no usarlo**. Valores de partida del boceto: azul marino `#17214D` (texto y fondos oscuros), azul `#2F4FE0` (acción principal), amarillo `#FFC23D` (acento), morado `#6B3FD8` (modo administrador), verde `#12845A` (confirmar, éxito), rosa `#E0305A` (eliminar, error), con su versión clara cada uno de azul, verde y rosa; neutros: fondo `#EEF0F8`, líneas `#D9DEEE`, texto suave `#6B7394`, blanco |
| Tipografía | La fuente (Figtree), sus pesos permitidos y **una escala cerrada de unos 8 tamaños**, cada uno con su uso (etiqueta, texto de apoyo, cuerpo, subtítulo, título de pantalla, título de detalle, cifra de reporte). El boceto usa 14 tamaños distintos: redúcelos a esos 8 |
| Espaciado | Una escala corta (por ejemplo múltiplos de 4: 4, 8, 12, 16, 24, 32, 48) y la regla «todo espacio sale de esta escala» |
| Formas | Radios de esquina por tipo de elemento (campos y botones, tarjetas, ventanas, píldoras) y 2 o 3 sombras con su uso |
| Tamaños de elementos | Altura única de botón y de campo (se sugieren 44 px, cómodo para el dedo), ancho máximo de la página, tamaño de avatares e imágenes de tarjeta |
| Íconos | **Un solo estilo** (mismo grosor de línea) para reemplazar los emojis. Anota qué juego usarás y la lista de íconos necesarios |
| Estados | Cómo se ve cada elemento interactivo: normal, al pasar el cursor, con foco del teclado, presionado, deshabilitado y con error |
| Responsivo | 3 anchos de referencia (móvil, tableta, escritorio) y qué cambia en cada uno |
| Accesibilidad | Contraste mínimo del texto (4.5 a 1; revisa `texto suave` sobre `fondo` y rosa sobre rosa claro, que podrían no cumplirlo) y tamaño táctil mínimo |

**Ejemplos del tipo de decisión que se espera** (los valores son ilustrativos; tú decides):

- **Colores:** «El azul se usa solo para la acción principal de cada pantalla, nunca para decorar. El rosa se usa solo para eliminar y para errores».
- **Letra:** «Hay 8 tamaños y nada más. El título de pantalla mide siempre lo mismo; el texto gris de ayuda mide siempre lo mismo».
- **Espacios:** «Solo se permiten 4, 8, 12, 16, 24, 32 y 48 px».
- **Botones:** «Todos miden 44 px de alto; los chicos, 36 px».
- **Íconos:** «Todos con trazo de 2 px, esquinas redondeadas, del mismo juego».

Después de este documento, nadie elige un valor a ojo.

**Criterio de «terminado»:** cualquier elemento del boceto se puede describir con las decisiones de este documento, sin valores sueltos.

---

## 4. Entrega 2 · `boceto-refinado/index.html`

Se parte de una copia de `02-boceto.html` y se aplica el documento anterior a las 44 pantallas. Sigue funcionando igual: navegable con el panel lateral.

Orden de prioridad (si falta tiempo, termina en el punto 4):

1. **Aplicar las bases:** colores, tamaños de letra, espaciado y radios, sin valores sueltos.
2. **Íconos:** reemplazar los emojis por un solo juego de íconos con el mismo trazo.
3. **Estados** de botones, campos y enlaces (sobre todo el foco con teclado, que casi no existe hoy).
4. **Móvil:** las pantallas de publicar (10 y 15), cámara (26), inicio de sesión (1), registro (3) e ingreso al negocio (8) deben verse bien a 375 px, porque los vendedores suben fotos desde el celular.
5. **Estados vacíos y de carga:** un solo diseño para «sin resultados» y «aún no hay…», y un indicador de carga.
6. **Alineación:** mismas alturas y esquinas en elementos del mismo tipo.

**Revisión antes de entregar**

 > La navegacion y las pantallas se respetan, los unicos cambios son visuales para normalizar y enfocar que el diseño tenga un orden y razón de ser.

- [ ] Las 44 pantallas se navegan igual que en el boceto original.
- [ ] No hay colores, tamaños de letra ni espacios fuera de lo definido en las bases.
- [ ] Ningún emoji queda como ícono.
- [ ] A 375 px no hay desplazamiento horizontal.
- [ ] Todo botón, campo y enlace muestra su foco con la tecla Tab.
- [ ] El texto cumple el contraste definido.

---

## 5. Entrega 3 · `05-reglas-css.md`

Se escribe con el boceto ya terminado. **Describe lo que quedó**, para que quien programe sepa cómo modularizar. Tablas antes que párrafos.

**Idea central:** el boceto repite piezas. Por ejemplo, la tarjeta de publicación aparece en 4 pantallas (catálogo, perfil del negocio, panel del negocio y perfil del negocio del administrador). Este documento debe dejar claro que es **una sola pieza, con un solo archivo de estilo y varias apariciones**. Lo mismo para el botón (con sus variantes: azul, verde, rosa, de contorno, chico, deshabilitado), la ventana emergente, la alerta y las barras superiores.

| # | Sección | Contenido |
|---|---|---|
| 1 | Principios | 5 líneas máximo |
| 2 | Colores, tipografía, tamaños, espacios, formas | Se toma de las bases (Entrega 1), corregido con lo que cambió al refinar |
| 3 | Puntos de quiebre | Anchos y qué cambia en cada uno |
| 4 | Convención de nombres | Una sola regla para nombrar las piezas (se sugiere español, minúsculas, con guiones: `tarjeta-publicacion`, y sus variantes como `tarjeta-publicacion--nueva`) y que coincida con el nombre del componente |
| 5 | **Catálogo de piezas** | Una fila por pieza reutilizable: nombre, para qué sirve, **variantes**, estados, y en qué pantallas del boceto aparece. Ejemplos: botón, campo, tarjeta de publicación, ventana emergente, alerta, barra superior, pestañas |
| 6 | Cómo se organizan los archivos de estilo | Una carpeta de estilos con: variables generales, estilos base, estructura de página, **un archivo por pieza** y, solo si hace falta, uno por página. Indicar el orden en que se cargan (variables → base → estructura → piezas → páginas). Debe coincidir con la carpeta `styles/` del [ADR-002](../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md) |
| 7 | Reglas de modularidad | Una pieza = un archivo; una pieza no depende de otra; las variantes se definen dentro de su propia pieza; no se usa `!important`; no se estila por identificador |
| 8 | Qué no se hace | Lista corta de prohibiciones |
| 9 | Pendientes | Lo que no alcanzó a pulirse |

**Criterio de «terminado»:** alguien que no vio el boceto puede crear una pieza nueva que se vea igual al resto usando solo este documento.

---

## 6. Entrega 4 · `00-documento-de-desarrollo.md`

Explica **qué páginas hay y cuáles se modularizan**, siguiendo la organización por capas del ADR-002. Tú no programas: inventarías y decides cómo se divide. **Cuenta las cifras tú mismo.** El equipo de React lo usará como su lista de trabajo.

**Ejemplos de lo que debe quedar claro:**

- Las 44 pantallas se reducen a menos páginas porque varias son la misma. El catálogo del invitado y el del usuario es **una página** con una pequeña diferencia en la barra superior.
- Todo lo que se repite en 2 o más pantallas es una **pieza reutilizable**: por ejemplo, la galería de imágenes aparece en el detalle del invitado, del vendedor y del administrador.
- Varias ventanas de confirmación («¿eliminar publicación?», «¿salir del negocio?», «¿dar de baja?») pueden ser **la misma ventana con distinto texto**.
- Se cuentan también las alertas que **faltan** en el boceto y que el alcance exige (por ejemplo, el error de conexión).

**Cómo hacer el inventario en el boceto**

| Qué buscar | Dónde mirar |
|---|---|
| Las pantallas y su numeración | Listas `CATEG` y `CATEG_ADM` al inicio del script (44 números) |
| Pantallas que en realidad son la misma | Números que apuntan a la misma función (por ejemplo, 2 y 4; 9 y 11) |
| Ventanas emergentes | El objeto `M` y la tabla `MODALS` |
| Alertas | Buscar `S.err=` (errores en línea) y `toast(` (avisos que desaparecen); cada texto distinto cuenta una vez |
| Sugerencias y estados vacíos | Los recuadros morados de ayuda y las tarjetas «Todavía no…» |
| Piezas que se repiten | Las funciones de «piezas comunes» y los bloques copiados entre pantallas |

**Secciones obligatorias**

| # | Sección | Contenido |
|---|---|---|
| 1 | Resumen en cifras | Pantallas del boceto, páginas resultantes, ventanas emergentes, alertas, avisos de éxito, sugerencias, estados vacíos y piezas reutilizables |
| 2 | De pantallas a páginas | Número(s) del boceto → página, y **por qué se unen** cuando varias son la misma |
| 3 | Páginas | Nombre, pantallas del boceto, **quién puede entrar** (visitante, usuario, vendedor con el negocio abierto, administrador) y las piezas que usa |
| 4 | Estructuras de página | Las barras superiores y esquemas que se repiten y qué páginas usa cada uno |
| 5 | Ventanas emergentes | Cuáles hay, desde dónde se abren y cuáles **comparten pieza** (varias confirmaciones son la misma ventana con otro texto) |
| 6 | Alertas y avisos | Código, texto exacto, dónde aparece y tipo. Aparte, las que **faltan en el boceto** pero que el alcance y los ADR exigen (error de conexión, tipo de imagen no válido, sesión expirada, página no encontrada, cámara no disponible) |
| 7 | Piezas y reciclaje | Pieza, qué hace, variantes y **en qué páginas se reutiliza**. Esta tabla responde «qué se modulariza» |
| 8 | Dónde cae cada cosa | Qué va en cada carpeta del ADR-002 (páginas, componentes, servicios, contexto, rutas, estilos) |
| 9 | Pendientes | Lo que viste en el boceto y ningún documento define |

**Reglas para decidir qué se modulariza**

1. Misma pantalla con pequeñas diferencias = **una sola página** con una condición.
2. Una pieza que aparece en 2 o más pantallas = **un componente**; si cambia poco, es una variante, no otro componente.
3. Un patrón de navegación repetido (barra superior, migas de pan, pestañas) = componente de estructura.
4. Un texto de alerta se escribe **una sola vez** y se identifica por su código.
5. Los nombres de las piezas coinciden con los del catálogo de `05-reglas-css.md`.

**Criterio de «terminado»:** otra persona puede contar las páginas y componentes que hay que crear sin abrir el boceto.