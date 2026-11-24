# 01 · Guía para subir los diagramas de la base de datos

> **Material de apoyo.** Explica qué imágenes hay que entregar del diseño de la base de datos, en qué orden, cómo nombrarlas y cómo justificar cada tabla. No trae código.
>
> Se lee junto con el [00 · Manual de diseño](00-manual-de-diseno-de-base-de-datos.md) (qué tablas y por qué) y el [02 · Relaciones](02-relaciones-entre-tablas.md) (cómo se conectan).

---

## 1. Qué hay que entregar

Una base de datos se entiende mejor **viéndola**. Por eso el diseño se entrega como imágenes con texto, en este orden:

| Orden | Entrega | Para qué sirve |
|---|---|---|
| 1 | **Un diagrama de todo el sistema** | Ver el panorama completo: todas las tablas y cómo se conectan |
| 2 | **Una imagen por cada tabla**, cada una con su justificación | Ver el detalle de cada tabla y entender por qué existe y por qué es así |

Se sube **primero el general y después las individuales**. Quien lea debe poder entender el sistema entero de un vistazo y luego bajar al detalle de la tabla que le interese.

---

## 2. Dónde guardar los archivos

Recomendación de carpeta y nombres. Las imágenes van en la carpeta `03-diagramas` de la carpeta de análisis:

```
04-analisis de base de datos/
└── 03-diagramas/
    ├── 00-diagrama-general.png
    ├── 01-usuario.png
    ├── 02-categoria.png
    ├── 03-negocio.png
    ├── 04-credencial-negocio.png
    ├── 05-red-social-negocio.png
    ├── 06-publicacion.png
    ├── 07-imagen-publicacion.png
    ├── 08-comentario.png
    ├── 09-calificacion.png
    ├── 10-contador-interacciones.png
    └── 11-bitacora-bajas.png
```

Reglas de nombre:

- **Empieza con un número** para que se ordenen solos. El `00` es siempre el general.
- **Minúsculas, sin espacios, sin acentos.** Usa guiones.
- **El nombre de la imagen es el nombre de la tabla**, tal como se llama en la base. Si la tabla cambia de nombre, se cambia también el archivo.
- Formato **PNG** (nítido para texto). Si la herramienta lo permite, guarda también el archivo editable para poder corregir después, en la misma carpeta.

> Si el equipo cambia los nombres de las tablas (ver el manual, sección 7), ajusta esta lista. Lo importante es que haya **un archivo por tabla** y que el general los incluya a todos.

---

## 3. El diagrama general

Es una sola imagen con **todas las tablas y todas las relaciones**.

### Qué debe mostrar

- **Todas las tablas propias** (las 11 de la propuesta) y, aparte y de un color distinto, la **cuenta de Supabase Auth**, que no se diseña pero sí se conecta.
- **Las líneas de relación**, con su tipo (uno a uno, uno a muchos, muchos a muchos). Se explican en el [documento 02](02-relaciones-entre-tablas.md).
- **El nombre de cada tabla**. Las columnas pueden ir resumidas aquí (solo la clave y los datos principales) porque el detalle va en cada imagen individual.
- **Una leyenda** pequeña que explique los símbolos y los colores usados.
- **La tabla de la bitácora separada**, sin líneas hacia las demás, y una nota que diga por qué (sobrevive a las bajas).

### Recomendaciones para que se lea bien

1. **Agrupa por tema** y colócalo de forma lógica, por ejemplo: cuentas y usuarios a la izquierda, negocios y categorías al centro, publicaciones y lo que cuelga de ellas a la derecha, y reportes y bitácora abajo.
2. **Evita que las líneas se crucen.** Si se cruzan, mueve las tablas hasta que dejen de hacerlo.
3. **Usa colores con sentido**, no por adorno. Una propuesta: un color para lo de personas (usuario), otro para lo del negocio, otro para el contenido (publicaciones, imágenes, comentarios, calificaciones), otro para los reportes y la bitácora, y otro para lo que gestiona Supabase.
4. **Letra legible**: si hay que acercar la imagen para leerla, está muy chica. Es mejor una imagen grande y limpia.
5. **Un solo estilo de notación** en todo el diagrama (el de «pata de gallo», que es el más común, sirve bien).
6. Pon un **título y la fecha** de la última actualización.

### Herramientas

El proyecto ya contempla draw.io, Mermaid y similares (ver [tecnologías](../../02-tecnologias/01-tecnologias.md)). Cualquiera sirve. Elige una y úsala para todas las imágenes, para que se vean como un solo conjunto.

---

## 4. La imagen de cada tabla

Cada tabla tiene **su propia imagen** y, junto a ella, **su justificación escrita**. La imagen enseña cómo es la tabla; la justificación explica por qué es así.

### Qué debe mostrar la imagen

- **El nombre de la tabla** arriba.
- **Todas sus columnas**, cada una con:
  - su nombre,
  - su tipo de dato (texto, número, fecha…),
  - si es **obligatoria** u opcional,
  - si es **única** (no se puede repetir),
  - cuál es la **clave** de la tabla.
- **Las tablas con las que se conecta**, dibujadas más pequeñas y solo con su nombre y la clave, con la línea de relación y su tipo. Así se ve de dónde viene cada vínculo sin repetir todo el diagrama general.
- **Una nota** con lo que pasa al eliminar (por ejemplo, «si se elimina el negocio, se eliminan sus publicaciones»).

### Qué debe responder la justificación

Escribe, debajo de cada imagen, un texto corto (unas 8 a 15 líneas) que conteste **estas siete preguntas**. Usa siempre el mismo orden, para que todas las tablas se lean igual:

| # | Pregunta | Qué se espera |
|---|---|---|
| 1 | **¿Qué es?** | Una frase: qué cosa del sistema representa |
| 2 | **¿Por qué existe como tabla aparte?** | Cuál de las señales del [manual, sección 4](00-manual-de-diseno-de-base-de-datos.md) aplica (varios valores, lista administrada, dato delicado, etc.) |
| 3 | **¿De dónde salen sus datos?** | Qué pantallas del documento de componentes los piden (por ejemplo, «pantallas 6 y 17») y qué decisiones (ADR) la afectan |
| 4 | **¿Qué guarda y por qué cada dato?** | Agrupa las columnas por tema y explica las menos obvias |
| 5 | **¿Qué NO guarda a propósito y por qué?** | Datos que podrían estar aquí, pero se calculan o viven en otra tabla |
| 6 | **¿Con qué se conecta?** | Las relaciones, resumidas (el detalle va en el documento 02) |
| 7 | **¿Qué pasa cuando se elimina?** | Qué arrastra y qué conserva |

Si una pregunta no aplica, escribe «No aplica» y una línea de motivo. **No la dejes en blanco**: quien lee debe saber que lo pensaste.

### Ejemplo de redacción (solo para ver el tono)

Este es un ejemplo con la tabla de imágenes de la publicación. No es texto obligatorio, solo muestra cómo debe sonar una justificación completa:

> **imagen de la publicación**
>
> 1. **Qué es.** Cada una de las fotos de una publicación.
> 2. **Por qué es una tabla aparte.** Una publicación puede tener hasta 10 imágenes. Si estuvieran dentro de la publicación, habría que repetir columnas o juntar varias direcciones en una celda, y las dos cosas rompen la primera forma normal.
> 3. **De dónde salen sus datos.** Pantallas 10 y 15 (zona «Añadir imágenes», hasta 10) y 12 y 20 (galería con miniaturas). ADR-004: solo se guarda la dirección en texto.
> 4. **Qué guarda.** La publicación a la que pertenece, la dirección de la imagen en Storage y su orden dentro de la galería. El orden permite que la primera sea la imagen principal.
> 5. **Qué no guarda.** El archivo de la imagen (va en Storage). Tampoco el tamaño ni el formato: la aplicación ya los controla al comprimir.
> 6. **Con qué se conecta.** Con la publicación (muchas imágenes por publicación).
> 7. **Al eliminar.** Si se elimina la publicación, se eliminan sus filas de imágenes, y la aplicación debe borrar también los archivos de Storage.

---

## 5. Cómo presentar cada tabla en el documento

Recomendación: un solo archivo de texto, **`tablas-con-justificacion.md`**, dentro de la carpeta `03-diagramas`, junto a las imágenes. Para cada tabla repite este esquema:

1. **Título** con el número y el nombre de la tabla.
2. **La imagen** de la tabla.
3. **La justificación** con las siete respuestas.

Y arriba de todo, la imagen del **diagrama general** con un párrafo que explique en tres o cuatro líneas cómo está organizado el sistema.

El orden del archivo es el mismo de las imágenes: general primero y luego tabla por tabla, de la 01 a la 11.

Así, quien quiera ver solo una tabla, la encuentra rápido; y quien quiera entender el sistema, lo lee de arriba abajo.

---

## 6. Cómo mantener el diseño al día

- **Si cambia una tabla, cambian tres cosas:** su imagen, su justificación y el diagrama general. Revisa también el [documento 02](02-relaciones-entre-tablas.md) si cambia alguna relación.
- **Si cambia el alcance** (por ejemplo, una pantalla nueva que pide un dato nuevo), revisa qué tabla recibe el dato y actualiza.
- **Anota la fecha** de la última revisión en el diagrama general.
- **Antes de entregar**, compara cada imagen con lo que realmente existe en Supabase. Deben coincidir; si no, el diagrama miente.

---

## 7. Lista de verificación de las imágenes

- [ ] Existe `00-diagrama-general` con todas las tablas y relaciones.
- [ ] El general distingue la cuenta de Supabase Auth del resto.
- [ ] El general muestra la bitácora sin conexiones y explica por qué.
- [ ] Hay una imagen por cada tabla, con el nombre de la tabla en el nombre del archivo.
- [ ] Cada imagen muestra todas las columnas con tipo, obligatoriedad, unicidad y clave.
- [ ] Cada imagen muestra las tablas con las que se conecta y el tipo de relación.
- [ ] Cada imagen tiene la nota de qué pasa al eliminar.
- [ ] Cada tabla tiene su justificación con las siete preguntas respondidas.
- [ ] Todas las imágenes usan la misma herramienta y el mismo estilo.
- [ ] El texto de las imágenes se lee sin acercar.
- [ ] Imágenes y justificaciones coinciden con lo que está en Supabase.
- [ ] Todo está enlazado desde el [README de Docs](../../README.md).

---

## 8. Documentos relacionados

- [00 · Manual de diseño de la base de datos](00-manual-de-diseno-de-base-de-datos.md)
- [02 · Relaciones entre tablas](02-relaciones-entre-tablas.md)
- [Componentes de las pantallas](../../05-vista/00-Componentes-de-pantallas.docx) (para citar las pantallas de origen de cada dato)
- [Tecnologías](../../02-tecnologias/01-tecnologias.md) (herramientas de diagramas)
