# 02 · Relaciones entre tablas

> **Material de apoyo.** Explica cómo se conectan las tablas del proyecto y **por qué** se conectan así. No trae código.
>
> Se lee después del [00 · Manual de diseño](00-manual-de-diseno-de-base-de-datos.md), que explica qué tablas hay. Los nombres de las tablas son los de la propuesta del manual (sección 5); si el equipo los cambia, se cambian aquí también.

---

## 1. Conceptos básicos

Una **relación** es el vínculo entre dos tablas. Casi siempre funciona así: una tabla guarda, en una de sus columnas, el identificador de una fila de otra tabla. Esa columna se llama **clave foránea** (o «referencia»).

Ejemplo cotidiano: en la tabla de negocios, cada negocio guarda el identificador de su dueño. Así sabemos de quién es sin repetir el nombre, la matrícula y la carrera del dueño en cada negocio.

### Los tres tipos de relación

| Tipo | Se lee | Ejemplo del proyecto |
|---|---|---|
| **Uno a uno (1:1)** | Una fila de A se conecta con una sola de B, y al revés | Un negocio tiene una sola contraseña de negocio |
| **Uno a muchos (1:N)** | Una fila de A se conecta con varias de B, pero cada fila de B tiene una sola de A | Un usuario tiene varios negocios, pero cada negocio tiene un solo dueño |
| **Muchos a muchos (N:M)** | Varias filas de A se conectan con varias de B | Muchos usuarios califican muchas publicaciones |

Una relación **muchos a muchos** no se puede guardar directamente: se rompe en **una tabla intermedia** que tiene una relación uno a muchos con cada lado. En este proyecto, la calificación es esa tabla intermedia entre usuarios y publicaciones.

### Qué pasa al eliminar

Cada relación debe responder: **si se elimina la fila principal, ¿qué pasa con las filas que dependen de ella?** Hay tres respuestas posibles:

| Respuesta | Qué significa | Cuándo usarla |
|---|---|---|
| **Eliminar en cascada** | Las filas que dependían también se eliminan | Cuando lo que depende no tiene sentido sin la fila principal (las imágenes sin su publicación) |
| **Reasignar** | Las filas que dependían pasan a apuntar a otra fila | Cuando deben conservarse (los negocios de una categoría eliminada pasan a «Otro») |
| **Impedir** | No se permite eliminar mientras haya filas que dependan | Cuando eliminar sería un error (no se puede eliminar al administrador maestro) |

En este proyecto la baja es **permanente** y la hace una función de la base de datos (ADR-003, reglas 6 y 7), así que casi todas las relaciones son en cascada: al dar de baja algo, se va todo lo que cuelga de ello. Las tres excepciones (categorías, bitácora y administrador maestro) están en la sección 4.

---

## 2. Resumen de todas las relaciones

| # | Relación | Tipo | Quién guarda la referencia | Al eliminar la principal |
|---|---|---|---|---|
| R1 | Cuenta de Auth ⟷ usuario | 1:1 | usuario | Se elimina el usuario |
| R2 | usuario → negocio | 1:N | negocio | Se eliminan sus negocios |
| R3 | categoria → negocio | 1:N | negocio | **Reasigna**: los negocios pasan a «Otro» |
| R4 | negocio ⟷ credencial del negocio | 1:1 | credencial | Se elimina la credencial |
| R5 | negocio → red social del negocio | 1:N | red social | Se eliminan sus enlaces |
| R6 | negocio → publicacion | 1:N | publicacion | Se eliminan sus publicaciones |
| R7 | publicacion → imagen de la publicación | 1:N | imagen | Se eliminan sus imágenes (y sus archivos de Storage) |
| R8 | publicacion → comentario | 1:N | comentario | Se eliminan sus comentarios |
| R9 | usuario → comentario | 1:N | comentario | Se eliminan sus comentarios |
| R10 | publicacion → calificacion | 1:N | calificacion | Se eliminan sus calificaciones |
| R11 | usuario → calificacion | 1:N | calificacion | Se eliminan sus calificaciones |
| R12 | publicacion → contador de interacciones | 1:N | contador | Se eliminan sus contadores |
| — | usuario ⟷ publicacion (por calificación) | N:M | tabla intermedia | (ver R10 y R11) |
| — | bitácora de bajas | Sin relaciones | — | No se elimina |

Son **12 relaciones** y **una tabla sin relaciones**. Cada una se explica a continuación.

---

## 3. Cada relación y su porqué

### R1 · Cuenta de Auth ⟷ usuario (uno a uno)

**Qué dice.** Cada cuenta de Supabase Auth tiene exactamente un usuario en la tabla propia, y cada usuario tiene exactamente una cuenta.

**Por qué.** Auth guarda cómo se entra (credenciales). La tabla de usuario guarda quién es la persona y qué rol tiene. Son dos mitades de la misma persona, y el vínculo debe ser exacto para que la regla de permisos pueda decir «este usuario es administrador». Por eso el usuario usa **el mismo identificador** que su cuenta.

**Cómo nace.** Un trigger de la base de datos crea la fila del usuario al registrarse, siempre con el rol de usuario (ADR-003, regla 4).

**Al eliminar.** La baja de una cuenta la hace la función de la base de datos (ADR-003, regla 6), porque eliminar una cuenta de Auth exige la clave de servicio que nunca va en el navegador. Esa función elimina la cuenta y todo lo que dependa del usuario.

**Pantallas.** (1) Inicio de sesión, (3) Crear una cuenta, (31) Panel: Usuarios.

---

### R2 · usuario → negocio (uno a muchos)

**Qué dice.** Un usuario puede tener **varios negocios**; cada negocio pertenece a **un solo** usuario.

**Por qué.** Lo dicen los documentos: «Un usuario puede tener varios negocios» ([usuarios](../../01-producto/04-usuarios.md), sección 5). El dueño debe estar registrado en el negocio para saber de quién es: las reglas de permiso (solo el dueño modifica) lo necesitan.

**Consecuencia.** Aquí no existe una tabla de «vendedores». Un vendedor es un usuario que tiene negocios (ver el punto abierto 1 del manual, sección 8).

**Al eliminar.** Si se da de baja al usuario, se eliminan sus negocios (y con ellos todo lo que cuelga de ellos: ver sección 5). Si solo se elimina un negocio, el usuario permanece.

**Pantallas.** (5) y (7) Perfil del usuario, (6) Añadir negocio, (18) y (19) Eliminar negocio, (30) Panel: Negocios.

---

### R3 · categoria → negocio (uno a muchos)

**Qué dice.** Cada negocio tiene **una** categoría; una categoría puede tener **muchos** negocios.

**Por qué.** Las categorías son una lista que el administrador administra. Guardar el nombre de la categoría escrito en cada negocio (en lugar de apuntar a ella) causaría dos problemas: cambiar el nombre de una categoría obligaría a corregir todos los negocios, y se podrían escribir variantes de lo mismo («Postres» y «postres y repostería»). Apuntando a la tabla de categorías, el nombre se guarda **una sola vez**: el administrador lo edita y el cambio llega a todos los negocios, que es justo lo que dice el alcance («al editar, el nombre cambia también en los negocios que la usan»).

**Al eliminar (excepción).** Aquí **no** se elimina en cascada: al eliminar una categoría, sus negocios **pasan a «Otro»**. Por eso «Otro» es una categoría especial que no se puede editar ni eliminar. Esta regla se implementa dentro de la función de eliminación de categorías.

**Pantallas.** (6) Añadir negocio y (17) Modificar información del negocio (lista de categorías), (32) Panel: Categorías, (36) y (40) Agregar y Editar categoría.

---

### R4 · negocio ⟷ credencial del negocio (uno a uno)

**Qué dice.** Cada negocio tiene una sola contraseña, guardada en su propia tabla.

**Por qué.** Los negocios son públicos: cualquiera ve el perfil de un negocio (ADR-003). Si la contraseña estuviera en la misma tabla, aunque esté cifrada, viajaría junto con los datos públicos y habría que protegerla columna por columna. Con una tabla aparte la regla es simple: **nadie la lee**; solo la función de la base que verifica la contraseña. Esa verificación se hace en la base de datos y no en el navegador, porque si solo se comprobara en pantalla se podría saltar pidiendo los datos directamente (ADR-003, regla 10).

**Al eliminar.** Si se elimina el negocio, se elimina la credencial.

**Pantallas.** (6) Añadir negocio, (8) Ingreso al negocio, (19) Confirmar eliminación del negocio.

---

### R5 · negocio → red social del negocio (uno a muchos)

**Qué dice.** Un negocio puede tener varios enlaces de redes sociales (Facebook, Instagram y Twitter, hasta 3 por red).

**Por qué.** Es la señal «varios valores por cosa» del manual: sin tabla aparte, habría que crear columnas repetidas («instagram1, instagram2, instagram3») o juntar los enlaces en una sola celda. Con tabla aparte, cada enlace es una fila y agregar uno no pide cambiar la estructura. El límite de 3 por red es una regla que se aplica además en la base de datos.

**Al eliminar.** Si se elimina el negocio, se eliminan sus enlaces.

**Pantallas.** (16) Configuración, (17) Modificar información del negocio, (28) Perfil público.

---

### R6 · negocio → publicacion (uno a muchos)

**Qué dice.** Un negocio tiene **varias** publicaciones; cada publicación pertenece a **un** negocio.

**Por qué.** El glosario lo define así: el negocio «agrupa sus publicaciones». Además, es lo que permite que la publicación **no guarde teléfono ni contacto**: se obtienen a través del negocio (pantalla (10): «el contacto se toma del teléfono del negocio»). Y es el eslabón que enlaza una publicación con su dueño: publicación → negocio → usuario. Sin esa cadena no se podría escribir la regla «solo el vendedor dueño modifica sus publicaciones» (ADR-003).

**Al eliminar.** Si se elimina el negocio, se eliminan sus publicaciones («Eliminar un negocio… borra también sus publicaciones», pantalla (19) y [usuarios](../../01-producto/04-usuarios.md)).

**Pantallas.** (9) Panel inicial, (10) Agregar publicación, (12) Detalle (negocio), (28) Perfil público, (29) Panel: Publicaciones.

---

### R7 · publicacion → imagen de la publicación (uno a muchos)

**Qué dice.** Una publicación tiene de **1 a 10 imágenes**.

**Por qué.** Varios valores por cosa. Además, cada imagen lleva su **orden**, para que la primera sea la principal y la galería se muestre ordenada.

**Al eliminar.** Si se elimina la publicación, se eliminan las filas de imágenes. **Atención:** eso no borra los archivos de Storage. La eliminación de la publicación debe encargarse también de los archivos (ADR-004, regla 9), o quedarían archivos huérfanos ocupando espacio del plan gratuito.

**Pantallas.** (10) y (15) subir imágenes, (12), (20) y (21) galería.

---

### R8 y R9 · comentario, publicación y usuario (dos relaciones uno a muchos)

**Qué dicen.** Un comentario pertenece a **una** publicación (R8) y lo escribió **un** usuario (R9). Una publicación tiene muchos comentarios y un usuario puede escribir muchos.

**Por qué.** El comentario es un hecho que **une** a un usuario con una publicación (señal F del manual). Necesita las dos referencias: la primera para saber en qué publicación se muestra; la segunda para mostrar el nombre del autor, y para que solo el autor pueda editar o eliminar el suyo (ADR-003). No se limita a uno por pareja, porque un usuario puede comentar varias veces.

**Al eliminar.**
- Si se elimina la publicación, se eliminan sus comentarios.
- Si se da de baja al usuario, se eliminan sus comentarios.
- El administrador también puede eliminar comentarios sueltos.

**Pantallas.** (20) y (21) comentarios con fecha y «Ordenar por», (38) Detalle de la publicación (administrador).

---

### R10 y R11 · calificacion, publicación y usuario (dos relaciones uno a muchos que forman una muchos a muchos)

**Qué dicen.** Muchos usuarios califican muchas publicaciones, pero **cada usuario califica cada publicación una sola vez**.

**Por qué es una tabla intermedia.** Es la relación muchos a muchos entre usuarios y publicaciones. La tabla guarda quién, cuál publicación, cuántas estrellas y la fecha. La pareja **usuario + publicación debe ser única**: es la regla «una calificación por usuario, que puede cambiar» (alcance). Cuando el usuario cambia su calificación, se actualiza la misma fila y se guarda la fecha nueva.

**Por qué la fecha.** Para que el reporte calcule el promedio de las calificaciones **de cada mes**.

**Por qué no hay un «promedio» guardado.** Se calcula al consultar. Guardarlo exigiría actualizarlo con cada calificación nueva.

**Al eliminar.**
- Si se elimina la publicación, se eliminan sus calificaciones.
- Si se da de baja al usuario, se eliminan sus calificaciones. **Efecto a tener presente:** el promedio de esa publicación cambia. Es aceptable: la baja es permanente y la baja de un usuario debe llevarse todo lo suyo.

**Pantallas.** (20) y (21) estrellas, (41) a (44) Reportes (calificación promedio del mes).

---

### R12 · publicacion → contador de interacciones (uno a muchos)

**Qué dice.** Una publicación tiene muchos contadores: uno por **tipo de interacción** (vista o clic en WhatsApp) y por **día**.

**Por qué.** Es la decisión del [ADR-006](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md): un contador por publicación, tipo y día basta para armar el reporte del mes y el detalle día por día, y crece poco sin importar cuánta gente entre.

**Por qué no apunta al usuario.** A propósito. El contador **no guarda quién** interactuó (ADR-006, regla 1). La única referencia es la publicación.

**Por qué la combinación debe ser única.** Para que en un día haya **una sola fila** por publicación y tipo, y la función que suma uno sepa qué fila actualizar (o crear si es la primera del día).

**Al eliminar.** Si se elimina la publicación, se eliminan sus contadores. Si se elimina el negocio, la cadena negocio → publicación → contador los arrastra.

**Pantallas.** (41) a (44) Reportes.

---

## 4. Los tres casos especiales

### 4.1 Bitácora de bajas: no tiene relaciones, a propósito

La bitácora guarda qué se eliminó, qué administrador y cuándo. **No apunta a ninguna otra tabla.**

**Por qué.**
- Si apuntara a un usuario y ese usuario se eliminara (justo lo que la bitácora registra), la fila de la bitácora desaparecería o impediría la baja. La bitácora tiene que **sobrevivir a lo que registra** (ADR-003, regla 7).
- Por eso guarda **texto**: la matrícula, el nombre del negocio, el nombre de la publicación y el nombre o matrícula del administrador. Es una «fotografía» de lo que pasó, no un vínculo.
- Escribe en ella **solo la función de baja**, y antes de eliminar. Nadie la edita ni la borra desde la aplicación.

**Qué no guarda.** El contenido de lo eliminado (ADR-003, regla 7: los datos mínimos para saber qué se eliminó).

**Pantallas.** (34) Panel: Bitácora y (35) Confirmar baja.

### 4.2 Categorías: se reasigna, no se elimina en cascada

Descrito en R3. Es la única relación en la que eliminar la principal **no** arrastra a las que dependen, sino que las **cambia de lugar**. Sin esta regla, eliminar una categoría eliminaría todos sus negocios y sus publicaciones, que es lo contrario de lo que pide el alcance.

### 4.3 Administrador maestro: protegido

El maestro **nunca se elimina ni pierde su rol** (ADR-003, regla 9). No es una relación entre tablas, sino una **regla de eliminación**: la función de baja debe negarse si la cuenta a eliminar es la del maestro, venga de quien venga, incluido él mismo. Así el sistema nunca se queda sin administrador.

---

## 5. La cadena de la baja

Al dar de baja algo, lo que cuelga de ello se va también. Así se ve la cadena completa. Léela de arriba hacia abajo:

**Baja de un usuario** elimina:
- su cuenta de Auth y su fila de usuario
- sus **comentarios** y sus **calificaciones** (en publicaciones de cualquiera)
- sus **negocios**, y con cada negocio:
  - su credencial
  - sus redes sociales
  - sus **publicaciones**, y con cada publicación:
    - sus imágenes (y los archivos de Storage)
    - sus comentarios y calificaciones
    - sus contadores

**Baja de un negocio** elimina lo mismo que arriba desde «sus negocios» hacia abajo.

**Baja de una publicación** elimina sus imágenes (y archivos), comentarios, calificaciones y contadores.

**Baja de un comentario** elimina solo ese comentario.

**Baja de una categoría** no elimina nada: pasa sus negocios a «Otro».

**Baja de un administrador hijo** quita su cuenta y su acceso (y lo que haya creado como usuario común). El maestro no se puede dar de baja.

En **todas** se escribe antes una fila en la bitácora.

> **Consejo para quien diseña:** al terminar el diseño, recorre esta cadena con un caso real. Por ejemplo, «doy de baja a un usuario con 2 negocios y 5 publicaciones»: ¿queda alguna fila huérfana apuntando a algo que ya no existe? Si la respuesta es sí, falta definir alguna eliminación en cascada.

---

## 6. Cómo se llega al dueño desde cualquier tabla

Las reglas de permiso (RLS) necesitan saber **de quién es** cada fila. Estos son los caminos para llegar al usuario dueño desde cada tabla. Si algún camino no se puede recorrer, falta una relación.

| Tabla | Camino hasta el usuario dueño |
|---|---|
| negocio | Directo (R2) |
| credencial del negocio | negocio → usuario |
| red social del negocio | negocio → usuario |
| publicacion | negocio → usuario |
| imagen de la publicación | publicación → negocio → usuario |
| contador de interacciones | publicación → negocio → usuario (solo lectura para el dueño, ADR-006) |
| comentario | Directo (el autor, R9) |
| calificacion | Directo (el autor, R11) |
| usuario | Es el propio usuario |
| categoria | No tiene dueño: la administra el administrador |
| bitácora de bajas | No tiene dueño: solo la lee un administrador |

---

## 7. Qué NO se relaciona (y por qué)

| Posible vínculo | Por qué no existe |
|---|---|
| Contador → usuario | El reporte solo muestra cantidades, no quién (ADR-006) |
| Bitácora → cualquier tabla | Debe sobrevivir a las bajas |
| Publicación → teléfono propio | El contacto es el teléfono del negocio |
| Usuario → «vendedor» como tabla | Un vendedor es un usuario con negocios |
| Administrador hijo → tabla propia | Es un usuario con rol de administrador. El maestro se distingue con un dato del usuario |
| Calificación → comentario | Son independientes: se puede calificar sin comentar y comentar sin calificar |
| Publicación → categoría | La categoría pertenece al **negocio**. Las publicaciones se filtran por la categoría de su negocio (pantallas (2) y (4)) |
| Cualquier tabla → carrito, pedido, favorito, chat | Quedan fuera de esta versión ([alcance](../../01-producto/03-alcance.md)) |

---

## 8. Lista de verificación de las relaciones

- [ ] Las 12 relaciones están dibujadas en el diagrama general, con su tipo.
- [ ] Cada relación indica qué pasa al eliminar.
- [ ] La relación de categorías es de reasignación, no de cascada.
- [ ] La bitácora no tiene líneas hacia otras tablas.
- [ ] La calificación es única por usuario y publicación.
- [ ] El contador es único por publicación, tipo y día, y no guarda quién.
- [ ] Desde cada tabla se puede llegar al usuario dueño (sección 6).
- [ ] La eliminación de publicaciones considera los archivos de Storage.
- [ ] Se recorrió la cadena de la baja con un caso de ejemplo.
- [ ] Cada relación está nombrada igual en el diagrama general, en la imagen de su tabla y en este documento.

---

## 9. Documentos relacionados

- [00 · Manual de diseño de la base de datos](00-manual-de-diseno-de-base-de-datos.md)
- [01 · Guía para subir los diagramas](01-guia-de-diagramas-e-imagenes.md)
- [ADR-003 Seguridad y permisos](../../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md) · [ADR-004 Imágenes](../../03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md) · [ADR-006 Reportes](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md)
- [Usuarios](../../01-producto/04-usuarios.md) · [Glosario](../../01-producto/01-glosario.md)
