# 00 · Manual de diseño de la base de datos

> **Material de apoyo, no una regla.** Este manual explica cómo pensar el diseño de la base de datos de Wissen Web y recomienda cómo dividir las tablas. No trae código: solo explicaciones. Si algo de aquí choca con un ADR, el glosario, el alcance o el documento de usuarios, **mandan esos documentos** (ver [README](../../README.md)).
>
> Va dirigido a quien diseña la base de datos por primera vez. No hace falta saber de bases de datos para seguirlo, solo leer con calma y en orden.

Documentos que acompañan a este manual:

- [01 · Guía para subir los diagramas](01-guia-de-diagramas-e-imagenes.md): qué imágenes entregar y cómo justificarlas.
- [02 · Relaciones entre tablas](02-relaciones-entre-tablas.md): cómo se conectan las tablas y por qué.

---

## 1. Qué se espera del diseño

El diseño de la base de datos responde a una pregunta: **¿qué información necesita guardar el sistema, dónde la guarda y cómo se conecta con el resto?**

Se entrega en tres partes, y cada una tiene su documento:

| Parte | Qué es | Dónde se explica |
|---|---|---|
| 1. Las tablas | Qué datos se guardan y cómo se agrupan | Este manual |
| 2. Las relaciones | Cómo se conectan las tablas y por qué | [02 · Relaciones](02-relaciones-entre-tablas.md) |
| 3. Los diagramas | El dibujo del sistema completo y de cada tabla, con su justificación | [01 · Diagramas](01-guia-de-diagramas-e-imagenes.md) |

Diseña **primero en papel o en un diagrama** y solo después crea las tablas en Supabase. Cambiar un dibujo cuesta minutos; cambiar tablas con datos y permisos ya hechos cuesta horas.

---

## 2. Lo que ya está decidido

Antes de dibujar nada, conoce lo que el equipo ya decidió. No se vuelve a discutir; el diseño debe respetarlo.

| Decisión | De dónde sale | Qué implica para las tablas |
|---|---|---|
| La base de datos es PostgreSQL en Supabase; no hay servidor propio | [ADR-001](../../03-arquitectura/decisiones/ADR-001-estilo-de-arquitectura.md) | Las reglas importantes se aplican en la propia base de datos |
| Los permisos se aplican en dos capas, y la seguridad real está en la base de datos (RLS) | [ADR-003](../../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md) | Cada tabla debe permitir saber **de quién es** cada fila, para poder escribir una regla de permiso |
| El rol se guarda **en la base de datos**, no en los metadatos de la cuenta | ADR-003, regla 2 | Hace falta un dato de rol en la tabla de usuarios |
| El usuario no puede cambiar su propio rol | ADR-003, regla 3 | El rol no es un dato que el formulario pueda editar |
| El registro lo resuelve un trigger de la base de datos | ADR-003, regla 4 | Al crear una cuenta, la base crea sola el registro del usuario |
| Hay un administrador maestro y administradores hijos; el maestro nunca se elimina | ADR-003, reglas 3 y 9 | Hay que poder distinguir al maestro de los hijos |
| Dar de baja es eliminar de forma permanente, y cada baja queda en una bitácora que sobrevive | ADR-003, reglas 6 y 7 | Las eliminaciones arrastran lo que depende de ellas; la bitácora no puede depender de lo eliminado |
| Las contraseñas nunca van en texto plano; la del negocio se guarda cifrada y se verifica en la base | ADR-003, regla 10 | La contraseña del negocio es un dato delicado |
| Las imágenes se guardan en Supabase Storage y la base conserva solo el texto de la dirección | [ADR-004](../../03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md) | Nunca hay una imagen dentro de una tabla, solo texto |
| Los reportes se arman con contadores: uno por publicación, tipo de interacción y día, sin identificar a nadie | [ADR-006](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md) | Hay una tabla de contadores y no guarda quién interactuó |
| No hay verificación de identidad | [ADR-005](../../03-arquitectura/decisiones/ADR-005-verificacion-de-identidad.md) (rechazada) | La matrícula no se comprueba; solo debe ser única |

---

## 3. Cómo sacar los datos del documento de componentes de pantallas

El documento [Componentes de las pantallas](../../05-vista/00-Componentes-de-pantallas.docx) es tu **mejor fuente** para saber qué datos necesita el sistema. Cada pantalla describe cada cuadro de texto, lista, etiqueta y texto. De ahí sale casi toda la información que hay que guardar.

### El método, pantalla por pantalla

1. Abre el documento y busca la pantalla (están numeradas).
2. Lee la tabla **«Botones y elementos»**.
3. Para cada elemento que muestre o pida información, hazte estas cuatro preguntas:

| Pregunta | Si la respuesta es sí… |
|---|---|
| ¿Es un dato que alguien **escribe o elige** y que debe recordarse? | Es un campo que se guarda |
| ¿Es un texto que la pantalla **calcula** a partir de otros datos (un total, un promedio, un contador de resultados)? | **No se guarda**; se calcula cuando se pide |
| ¿Es un texto fijo de la pantalla (títulos, ayudas, mensajes de alerta)? | No tiene que ver con la base de datos |
| ¿Es una imagen? | Se guarda **la dirección en texto**; el archivo va a Storage |

4. Anota cada dato guardado con: **de qué cosa es** (de un usuario, de un negocio, de una publicación), **si es obligatorio** y **si se repite**.
5. Marca también los **mensajes de alerta**. Dicen mucho: «Este usuario ya está registrado» significa que la matrícula no se puede repetir; «Esa categoría ya existe» significa que el nombre de categoría tampoco.

### Qué sale de cada pantalla (guía de lectura)

| Pantalla | Qué datos revela |
|---|---|
| (3) Crear una cuenta y (24) Editar información del usuario | Los datos personales: nombre(s), apellido paterno, apellido materno, matrícula, edad, licenciatura (5 opciones fijas), semestre. La contraseña la guarda Supabase Auth, no se diseña aquí |
| (24), (25) y (26) Ver foto y Tomar foto | El usuario tiene una foto de perfil, que es opcional |
| (6) Añadir negocio | Nombre, teléfono con lada, categoría (una de la lista), contraseña propia del negocio. Un usuario puede tener varios negocios |
| (16) y (17) Configuración y Modificar información del negocio | Además: descripción, foto del negocio y enlaces a redes sociales (Facebook, Instagram y Twitter, hasta 3 enlaces por red) |
| (10) Agregar publicación y (15) Modificar publicación | Nombre, precio, tipo (Producto o Servicio), descripción y **hasta 10 imágenes**. El contacto **no** se escribe: viene del teléfono del negocio |
| (20) y (21) Detalle de la publicación | Calificación (de 1 a 5, una por usuario), comentarios con fecha. El promedio y el número de calificaciones se calculan |
| (27) y (28) Listado y perfil público del negocio | Sin datos nuevos. Muestra lo anterior. «Publicaciones (n)» se calcula |
| (29) a (33) Panel del administrador | Sin datos nuevos, salvo el tipo de administrador (maestro o hijo) y el número de negocios por categoría (calculado) |
| (34) Bitácora | Qué se eliminó, qué administrador y cuándo |
| (36), (37) y (40) Categorías y administradores | Nombre de la categoría (único) y la matrícula con la que se otorga el rol |
| (41) a (44) Reportes | Vistas y clics por publicación y día. Todo lo demás se calcula |

### Qué NO se guarda (se calcula)

Es el error más común. Estos datos se ven en pantalla, pero **no necesitan columna**:

- La calificación promedio y cuántas calificaciones hay.
- «6 en total» y «2 de 6 resultados» del catálogo.
- El número de publicaciones de un negocio y el de negocios de una categoría.
- Los contadores de las pestañas del panel del administrador.
- Los totales del mes, las publicaciones con más clics y el detalle día por día de los reportes (salen de sumar los contadores).
- Si un usuario es vendedor o no, cuando se decide por tener negocios (ver el punto abierto 1 de la sección 8).

**Regla práctica:** si el dato se puede obtener contando o sumando otros datos, no lo guardes. Guardarlo obliga a mantenerlo al día y tarde o temprano se desajusta.

---

## 4. Cómo decidir cuándo separar en tablas

Una tabla debe hablar de **una sola cosa**: usuarios, negocios, publicaciones. Cuando una tabla empieza a mezclar cosas distintas, hay que dividirla. Estas seis señales te ayudan a decidirlo:

| Señal | Qué hacer | Ejemplo del proyecto |
|---|---|---|
| **A.** Un mismo dato puede tener **varios valores** para una sola cosa | Tabla aparte, con una fila por valor | Una publicación tiene hasta 10 imágenes. Un negocio tiene hasta 3 enlaces por red social |
| **B.** El dato es una **lista que cambia** y alguien la administra | Tabla aparte, y las demás tablas apuntan a ella | Las categorías: el administrador las agrega, edita y elimina |
| **C.** El dato **pertenece a otra cosa** | No lo repitas: guárdalo donde pertenece y consúltalo desde ahí | El teléfono de contacto de la publicación es el del negocio. Si se copiara a cada publicación, cambiarlo obligaría a corregir todas |
| **D.** El dato es **delicado** y no debe viajar junto con datos públicos | Tabla aparte con permisos más estrictos | La contraseña del negocio |
| **E.** El registro debe **sobrevivir** aunque lo demás se elimine | Tabla sin dependencia de las demás | La bitácora de bajas |
| **F.** Es un **hecho que une** a dos cosas | Tabla propia que las conecte | La calificación une a un usuario con una publicación |

Y una señal para **no** separar: si dos datos siempre se piden juntos, solo hay uno por cosa y nadie los administra por separado, **van en la misma tabla**. Dividir de más complica las consultas sin ningún beneficio (principio rector del proyecto: facilitar el desarrollo y evitar la complejidad).

### Comprobación de normalización (versión sencilla)

Los PDFs de esta carpeta hablan de 1FN, 2FN y 3FN. En palabras sencillas, antes de dar una tabla por buena revisa:

- **1FN, un valor por celda.** Ninguna celda guarda una lista («foto1, foto2, foto3»). Si hay varios valores, se necesita otra tabla (señal A).
- **2FN, cada dato depende de la tabla completa.** Si la fila se identifica con dos datos (por ejemplo, usuario y publicación), cada dato de la fila debe depender de **los dos**, no solo de uno.
- **3FN, nada depende de un dato que no sea la clave.** Si un dato se puede deducir de otro dato de la misma fila (por ejemplo, el nombre de la categoría deducido de su identificador), no debe estar repetido ahí. Se guarda una vez en su tabla y se consulta.

---

## 5. División de tablas recomendada

Esta es una **propuesta**, no una orden. Los nombres son sugerencias. Si al revisarla encuentras un motivo para cambiar algo, anótalo en la justificación de la tabla (ver [01 · Diagramas](01-guia-de-diagramas-e-imagenes.md)).

| # | Tabla | Qué guarda | Por qué es una tabla aparte | Pantallas de origen |
|---|---|---|---|---|
| — | Cuentas de Supabase Auth | Credenciales de inicio de sesión (la matrícula y la contraseña cifrada) | **No se diseña**: la crea y gestiona Supabase. Solo hay que enlazarla con la tabla de usuarios | (1), (3) |
| 1 | **usuario** | Datos personales, matrícula, rol, tipo de administrador y foto | Es la persona. Casi todo lo demás pertenece a un usuario | (3), (5), (24), (31) |
| 2 | **categoria** | Nombre de cada categoría | Es una lista administrada (señal B) | (6), (32), (36), (40) |
| 3 | **negocio** | Nombre, teléfono, descripción, foto y categoría | Un usuario puede tener varios negocios | (6), (16), (17), (27), (28) |
| 4 | **credencial del negocio** | Contraseña del negocio, cifrada | Es un dato delicado (señal D): así, quien lea los negocios nunca toca la contraseña | (6), (8), (19) |
| 5 | **red social del negocio** | Una fila por enlace: qué red y cuál enlace | Hay varios valores por negocio (señal A) | (16), (17), (28) |
| 6 | **publicacion** | Nombre, precio, tipo, descripción y fecha de creación | Es lo que se vende y lo que más se consulta | (9), (10), (12), (15), (20) |
| 7 | **imagen de la publicación** | Una fila por imagen: dirección y orden | Hay hasta 10 por publicación (señal A) | (10), (12), (15), (20) |
| 8 | **comentario** | Texto y fecha, quién lo escribió y a qué publicación | Une usuario con publicación (señal F) y un usuario puede comentar varias veces | (20), (21), (38) |
| 9 | **calificacion** | Estrellas (1 a 5) y fecha, quién y a qué publicación | Une usuario con publicación; solo una por pareja | (20), (21), (41) a (44) |
| 10 | **contador de interacciones** | Cuántas vistas o clics en WhatsApp hubo, por publicación, tipo y día | Es la base de los reportes ([ADR-006](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md)) | (41) a (44) |
| 11 | **bitácora de bajas** | Qué se eliminó, quién lo eliminó y cuándo | Debe sobrevivir a las bajas (señal E) | (34), (35) |

Son **11 tablas propias** más la de cuentas que ya trae Supabase. Es un tamaño razonable para una semana: ninguna es difícil, pero juntas cubren todo el alcance.

### Qué guarda cada tabla, en palabras

Esto es una guía de **contenido**, no la lista final de columnas. Tú defines los nombres y los tipos.

**usuario**
- Identificador (el mismo de la cuenta de Supabase Auth, ver sección 6).
- Nombre(s), apellido paterno y apellido materno.
- Matrícula (única).
- Edad y semestre (números).
- Licenciatura (una de las cinco: Enfermería, Educación, Software, Veterinaria, Contaduría).
- Foto de perfil (dirección en texto; opcional).
- Rol (usuario o administrador).
- Si es administrador maestro (solo uno).

**categoria**
- Identificador y nombre (único).
- Hay 15 categorías iniciales y una especial, «Otro», que no se edita ni se elimina. Recomendación: que la tabla permita distinguirla, para que la regla «no se puede eliminar» se pueda aplicar.

**negocio**
- Identificador, dueño (el usuario), categoría.
- Nombre y teléfono (con lada; recomendado guardarlo como **texto**, no como número, para no perder ceros ni signos).
- Descripción y foto (ambas opcionales).

**credencial del negocio**
- Identificador del negocio y contraseña cifrada. Nada más.

**red social del negocio**
- Negocio, qué red (Facebook, Instagram o Twitter) y el enlace. El límite de 3 por red es una regla que se debe aplicar también en la base.

**publicacion**
- Identificador y negocio al que pertenece.
- Nombre, precio, tipo (Producto o Servicio), descripción.
- Fecha de creación (el perfil del negocio las ordena «de la más reciente a la más antigua»).
- **No lleva teléfono ni contacto**: sale del negocio.

**imagen de la publicación**
- Publicación, dirección de la imagen y número de orden (la primera es la principal, y el orden es el de la galería). Máximo 10 por publicación.

**comentario**
- Publicación, autor, texto y fecha (permite «Más recientes» y «Más antiguos»).

**calificacion**
- Publicación, autor, estrellas y fecha. Solo una por usuario y publicación; si el usuario cambia su calificación, se actualiza la misma fila con la fecha nueva (ADR-003).

**contador de interacciones**
- Publicación, tipo de interacción (vista o clic en WhatsApp), día y cantidad. Una sola fila por publicación, tipo y día.
- **No guarda quién.** Ni matrícula ni cuenta.

**bitácora de bajas**
- Tipo de baja (usuario, negocio, publicación, comentario, categoría, administrador).
- Datos básicos para identificar lo eliminado, como texto (matrícula, nombre del negocio, nombre de la publicación…).
- Quién lo hizo (también como texto) y cuándo.
- Sin vínculos con las demás tablas (ver [02 · Relaciones](02-relaciones-entre-tablas.md)).

---

## 6. Decisiones aclaradas

Preguntas que seguramente te harás, con su respuesta.

**¿Por qué hay una tabla «usuario» si Supabase ya tiene cuentas?**
Supabase Auth guarda cómo entrar (credenciales). Pero la aplicación necesita más datos de cada persona (carrera, semestre, rol) y las reglas de permisos necesitan consultar el rol. Por eso se crea una tabla propia, ligada **uno a uno** con la cuenta de Auth. El ADR-003 lo exige así: el rol vive en la base de datos y no en los metadatos de la cuenta, porque el usuario podría modificarlos.

**¿Quién crea la fila del usuario?**
No el formulario. Un trigger de la base de datos la crea al registrarse, siempre con el rol de usuario, sin importar lo que envíe el navegador (ADR-003, regla 4). Piensa la tabla de usuarios sabiendo que **nadie la llena a mano**.

**¿Cómo se inicia sesión con matrícula si Supabase Auth normalmente usa correo?**
Es un detalle técnico que hay que resolver con el equipo antes de implementar el registro (ver punto abierto 4 de la sección 8). Para el diseño de tablas solo importa que la matrícula **sea única** y que cada cuenta de Auth tenga su usuario correspondiente.

**¿Por qué la contraseña del negocio va en otra tabla?**
Porque los negocios son de lectura pública (cualquiera ve el perfil de un negocio). Si la contraseña, aunque cifrada, estuviera en la misma tabla, viajaría junto con los datos públicos y habría que proteger columna por columna. Con una tabla aparte, la regla es simple: **nadie la lee; solo la función que verifica la contraseña**. Esa verificación ocurre en la base de datos y no en el navegador (ADR-003, regla 10).

**¿Por qué el teléfono no se guarda en cada publicación?**
Porque el teléfono es del negocio, y la pantalla (10) lo dice: «El contacto se toma del teléfono del negocio». Guardarlo en un solo lugar evita datos contradictorios y permite cambiarlo una sola vez desde (17).

**¿Dónde se guarda que alguien es vendedor?**
Es una pregunta abierta (ver sección 8, punto 1). Hay dos caminos válidos y ninguno cambia las demás tablas.

**¿Cómo se guarda una imagen?**
Nunca dentro de una tabla. El archivo va a Supabase Storage y la tabla guarda **solo el texto de la dirección** (ADR-004). Esto aplica a tres lugares: la foto del usuario, la foto del negocio y las imágenes de cada publicación (estas, en su propia tabla porque son varias).

**¿Por qué los comentarios y las calificaciones son tablas distintas?**
Porque son cosas distintas con reglas distintas. Un usuario puede escribir **varios** comentarios en una publicación; en cambio, solo tiene **una** calificación por publicación, que puede cambiar. Si se mezclaran, esa regla de «una sola» sería imposible de aplicar limpiamente.

**¿Por qué los contadores no guardan una fila por cada vista?**
Porque crecería sin parar y guardaría más de lo que hace falta ([ADR-006](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md), opción C descartada). Una fila por publicación, tipo y día basta para el reporte mensual y para el detalle día por día.

**¿Por qué la bitácora no se conecta con nada?**
Porque tiene que **sobrevivir** a lo que registra. Si la bitácora apuntara a un usuario y ese usuario se eliminara, la fila de la bitácora desaparecería o impediría la baja. Por eso guarda **texto** (matrícula, nombre) y no vínculos.

**¿Qué pasa con las categorías cuando se elimina una?**
Sus negocios pasan a «Otro», no se eliminan. Es lo opuesto a una baja normal, así que se debe diseñar como una regla especial (ver [02 · Relaciones](02-relaciones-entre-tablas.md)).

---

## 7. Recomendaciones para quien diseña

### Antes de dibujar

1. **Lee en este orden:** [glosario](../../01-producto/01-glosario.md), [usuarios](../../01-producto/04-usuarios.md), [alcance](../../01-producto/03-alcance.md), [ADR-003](../../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md), [ADR-004](../../03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md), [ADR-006](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md) y, por último, el documento de componentes de pantallas.
2. **Recorre todas las pantallas** con el método de la sección 3 y arma una lista de datos. No empieces por las tablas: empieza por los datos.
3. **Agrupa los datos por «de qué cosa son»** y luego aplica las señales de la sección 4.

### Al diseñar cada tabla

- **Nombres:** elige un estilo y úsalo en todas. Recomendado: nombres en español, en minúsculas, con guion bajo para separar palabras y **sin acentos ni eñes** (la base de datos y el código se llevan mejor así). Singular para las tablas (una tabla describe una fila).
- **Identificador propio:** cada tabla tiene su identificador único. Para las tablas ligadas a la cuenta del usuario, usa el mismo identificador de la cuenta de Auth.
- **Obligatorio u opcional:** decide para cada columna si puede quedar vacía. Por ejemplo, la foto y la descripción del negocio son opcionales (el documento dice «si no tiene…»); el nombre y el precio de la publicación son obligatorios («Todos los campos son obligatorios»).
- **Valores únicos:** matrícula, nombre de categoría, y la pareja usuario-publicación en las calificaciones. Las alertas del documento de componentes te dicen cuáles son.
- **Valores permitidos:** donde solo valen unas pocas opciones (tipo Producto o Servicio, estrellas de 1 a 5, tipo de interacción, red social), restringe los valores en la base para que ningún dato inválido entre aunque se salten la pantalla.
- **Tipos de dato, en palabras:** texto para nombres, teléfonos, enlaces y descripciones; número entero para edad, semestre, estrellas y contadores; número con decimales para el precio; fecha con hora para comentarios y calificaciones; solo fecha para los contadores por día.
- **Fechas:** guarda siempre la fecha de creación de lo que se ordena o se reporta (publicaciones, comentarios, calificaciones). Sin ella no hay «más recientes» ni reporte mensual.
- **Qué hacer al eliminar:** define para cada relación qué pasa con lo que depende (ver [02 · Relaciones](02-relaciones-entre-tablas.md)). Una baja de usuario no debe dejar negocios huérfanos.
- **Imágenes y Storage:** al eliminar una fila con imagen, la base de datos **no borra el archivo** de Storage por sí sola. La función de baja y la eliminación de publicaciones deben encargarse también de los archivos (ADR-004, regla 9).

### Seguridad desde el diseño

- **Cada fila debe poder decir de quién es.** Si no se puede saber quién es el dueño de una fila (por ejemplo, de una imagen, que pertenece a una publicación, que pertenece a un negocio, que pertenece a un usuario), no se podrá escribir la regla de permiso. Al diseñar, comprueba que desde cualquier tabla se pueda **llegar hasta el usuario dueño**.
- **Activa la seguridad por filas (RLS) en cada tabla nueva** y no dejes ninguna sin reglas (ADR-003).
- **Nunca confíes solo en la pantalla.** Que el botón no se vea no impide que alguien pida los datos directamente.

### Al construir en Supabase

- **Orden recomendado de creación:** primero lo que no depende de nada (categorías), luego usuarios, negocios, credenciales y redes, publicaciones e imágenes, comentarios y calificaciones, y al final los contadores y la bitácora. Si creas una tabla que apunta a otra que todavía no existe, no se podrá.
- **Datos de ejemplo:** carga las 15 categorías y «Otro», un administrador maestro y unos pocos usuarios, negocios y publicaciones. Probar con datos reales de ejemplo descubre errores de diseño pronto.
- **Prueba con cada rol:** visitante, usuario, vendedor y administrador. Un permiso mal escrito deja datos abiertos o bloquea a quien sí debería poder.
- **Guarda tu trabajo seguido** en Git, y el diseño (diagramas) junto con las tablas.

---

## 8. Puntos abiertos para decidir con el equipo

Estas decisiones **no** están resueltas en los documentos. Anótalas, propón una opción y valídala antes de crear las tablas.

| # | Pregunta | Opciones | Recomendación |
|---|---|---|---|
| 1 | ¿Cómo se sabe que un usuario es vendedor? | **a)** El rol cambia a «vendedor» cuando crea su primer negocio (lo dice ADR-003 regla 4). **b)** El rol no cambia; se es vendedor si tiene al menos un negocio | Es lo que más conviene decidir cuanto antes. La opción **b** evita tener que actualizar el rol al crear o eliminar negocios. La **a** sigue al pie de la letra el ADR. Elijan una y documenten el motivo |
| 2 | ¿Cómo se marca al administrador maestro? | **a)** Un dato extra en el usuario. **b)** Una tabla de administradores aparte | **a**: es más simple y solo hay uno |
| 3 | ¿La licenciatura es tabla o lista fija? | **a)** Tabla. **b)** Valores permitidos en la propia columna | **b**: son 5, las fija la universidad y la aplicación no las administra. Las categorías, en cambio, **sí** son tabla porque el administrador las modifica |
| 4 | ¿Cómo entra la matrícula en Supabase Auth, que normalmente pide correo? | Resolverlo con el equipo antes del registro | Lo común es construir un correo interno a partir de la matrícula, sin enviar ningún correo (ADR-005 rechazó la verificación). Debe quedar documentado |
| 5 | ¿El límite de 10 imágenes y de 3 enlaces por red se aplica solo en pantalla? | Pantalla y base de datos | En **los dos lugares**: en pantalla para la experiencia y en la base para la seguridad (ADR-003) |
| 6 | ¿Qué datos exactos guarda la bitácora? | Lo mínimo para identificar | Tipo, nombre o matrícula, administrador y fecha. Sin el contenido eliminado (ADR-003, regla 7) |

---

## 9. Errores comunes que conviene evitar

1. **Guardar totales y promedios.** Se desajustan. Se calculan.
2. **Una columna con varias cosas** («foto1, foto2…» o «instagram1, instagram2, instagram3»). Eso pide otra tabla.
3. **Repetir datos de otra cosa** (el teléfono del negocio dentro de cada publicación).
4. **Guardar el archivo de la imagen** en la base de datos.
5. **Dejar el rol en un dato que el usuario puede editar.**
6. **Olvidar qué pasa al eliminar.** Se queda basura que apunta a algo que ya no existe, o la baja falla.
7. **Que la bitácora dependa de lo que registra.**
8. **Inventar tablas «por si acaso»** (favoritos, pedidos, carrito, chat). El alcance dice que **no** están en esta versión.
9. **Cambiar el diseño sin avisar.** Si se cambia una tabla, se actualizan los diagramas y la justificación en el mismo momento.
10. **Diseñar sin mirar las pantallas.** Si una pantalla pide un dato que ninguna tabla guarda, el diseño está incompleto.

---

## 10. Lista de verificación antes de entregar

- [ ] Cada dato guardado de cada pantalla está en alguna tabla.
- [ ] Ningún dato calculable (promedios, totales, contadores de resultados) está guardado.
- [ ] Cada tabla habla de una sola cosa.
- [ ] Ninguna columna guarda una lista de valores.
- [ ] Las columnas únicas están identificadas (matrícula, nombre de categoría, calificación por usuario y publicación, contador por publicación, tipo y día).
- [ ] Cada columna dice si es obligatoria u opcional.
- [ ] Cada relación tiene definido qué pasa al eliminar (ver [02](02-relaciones-entre-tablas.md)).
- [ ] La contraseña del negocio está aislada y cifrada.
- [ ] Las imágenes son solo texto de dirección.
- [ ] La bitácora no depende de ninguna otra tabla.
- [ ] Desde cualquier tabla se puede llegar al usuario dueño.
- [ ] Hay un diagrama general y una imagen por tabla con su justificación ([01](01-guia-de-diagramas-e-imagenes.md)).
- [ ] Los puntos abiertos de la sección 8 tienen una decisión escrita.
- [ ] Se respetó el alcance: nada de carrito, pagos, chat ni favoritos.

---

## 11. Documentos relacionados

- [Glosario](../../01-producto/01-glosario.md) · [Alcance](../../01-producto/03-alcance.md) · [Usuarios](../../01-producto/04-usuarios.md)
- [ADR-003 Seguridad y permisos](../../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md) · [ADR-004 Imágenes](../../03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md) · [ADR-006 Reportes](../../03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md)
- [Componentes de las pantallas](../../05-vista/00-Componentes-de-pantallas.docx) y [Secuencias y navegación](../../05-vista/01-Secuencias-y-navegacion.docx)
- PDFs de esta carpeta: `01-Análisis_de_Requerimientos_y_Planeación_de_Datos` y `02-Abstracción_de_Datos_y_Normalización_Conceptual` (versiones actualizadas; las anteriores están en `primer analisis/`). Sirven de apoyo, pero **si difieren de los documentos del proyecto, prevalecen estos**.
