# ADR-004 · Almacenamiento de imágenes: Supabase Storage con procesamiento en el frontend

- **Estado:** Propuesta (pendiente de revisión del equipo)
- **Fecha:** 2026-11-22
- **Relacionada con:** [ADR-001](ADR-001-estilo-de-arquitectura.md), [ADR-003](ADR-003-seguridad-y-permisos.md)

> Este ADR describe **reglas y principios**, no el diseño de tablas. La estructura de la base de datos la define el análisis de base de datos.

## Contexto

Cada publicación lleva imagen. Las fotos las suben los vendedores desde sus celulares, y las fotos de celular pesan entre 4 y 8 MB.

Condiciones que limitan la decisión:

- **Costo cero:** el plan gratuito de Supabase da 1 GB de archivos y 5 GB de ancho de banda al mes.
- **Sin servidor propio** (ADR-001).
- **Catálogo público:** cualquier visitante debe poder ver las imágenes.
- **Proyecto escolar:** solo se subirán unas cuantas imágenes, a modo de ejemplo. El volumen real es pequeño, por lo que no se justifica imponer límites estrictos al usuario.
- **Principio rector:** facilitar el desarrollo y evitar la complejidad.

## Opciones consideradas

| Opción | Descripción | Resultado |
|---|---|---|
| A. Dentro de la base de datos | La imagen se guarda como dato binario junto al resto | Descartada: infla la base de datos, hace lentas las consultas y no hay entrega por CDN |
| **B. Storage con URL pública y subida directa** | El navegador sube el archivo al bucket; la publicación guarda solo la URL | **Elegida** |
| C. Storage con URL firmada | Cada imagen se pide con un enlace temporal | Descartada: el catálogo es público; solo añade complejidad |
| D. Servicio externo o servidor intermedio | Otra plataforma o un servidor recibe las imágenes | Descartada: añade una pieza más y contradice el ADR-001 |

## Decisión

Las imágenes se guardan en **Supabase Storage** y la base de datos conserva solo el **texto de la URL**. El archivo nunca se guarda en la base de datos.

**El frontend se encarga de preparar las imágenes** antes de subirlas, para no depender de funciones de pago de la plataforma y mantenernos dentro del plan gratuito.

### Reglas

1. **Subida directa:** el navegador sube el archivo al bucket, sin pasar por un servidor propio.
2. **Validación de tipo en el navegador:** solo se aceptan JPEG, PNG y WebP.
3. **Sin límite de tamaño para el usuario:** al ser un proyecto escolar con pocas imágenes de ejemplo, no se rechaza ninguna foto por pesar demasiado. El programa se encarga de reducirla.
4. **Compresión automática en el navegador:** antes de subir, el programa comprime cada imagen a unos 150–300 KB (idealmente en formato WebP). Es la razón por la que no hace falta limitar el tamaño: la compresión mantiene el uso dentro del plan gratuito.
5. **Versiones reducidas en el navegador:** si hace falta una miniatura para el catálogo, se genera en el frontend al subir la imagen, no con transformaciones de Supabase.
6. **Nombre único:** cada archivo se renombra con un UUID, para que dos archivos con el mismo nombre no se sobrescriban.
7. **Ruta con el identificador del vendedor:** permite que la política de Storage compruebe que solo el dueño modifica sus archivos.
8. **Permisos (ADR-003):** lectura pública. Subir, editar y borrar solo el vendedor dueño.
9. **Limpieza:**
   - Al eliminar una publicación, se elimina también su imagen.
   - Se revisan periódicamente las imágenes subidas que nunca quedaron en una publicación (archivos huérfanos).

## Recomendación para el código

Esta es una guía para quien implemente la subida, no una regla estricta. Los valores son iniciales y se ajustan al probar con imágenes reales.

**Proceso de compresión.** Cualquier imagen aceptada (JPEG, PNG o WebP) pasa por los mismos pasos antes de subirse:

1. **Reducir las dimensiones** a un máximo de unos 1200 px de ancho o alto, manteniendo la proporción. Es lo que más reduce el peso: una foto de celular de 4000×3000 px es innecesaria para una tarjeta de catálogo.
2. **Re-codificar como WebP** con calidad media-alta (alrededor de 0.8). Con esto un PNG de varios MB baja a cientos de KB.
3. **Subir el resultado**, con extensión `.webp` y nombre único (UUID).

**Detalles a tener en cuenta**

- **PNG:** pesan mucho porque guardan todo sin pérdida, pero se comprimen igual que el resto. WebP conserva la transparencia, así que un logo con fondo transparente sigue igual.
- **Compatibilidad:** algunos navegadores antiguos no crean WebP desde el `canvas` y devuelven otro formato. Hay dos formas de cubrirlo: usar una librería que lo maneje (por ejemplo `browser-image-compression`) o comprobar el tipo del resultado y usar JPEG como alternativa. Ojo: con JPEG la transparencia se pierde.
- **Ubicación:** esta lógica va en `services/` (ADR-002), en una función que reciba el archivo y devuelva el archivo ya comprimido. Las páginas no la implementan por su cuenta.
- **Experiencia:** mostrar un indicador de progreso mientras se comprime.

## Consecuencias

**A favor**

- La base de datos se mantiene ligera y las imágenes se entregan por CDN.
- No hay servidor que mantener.
- Con imágenes de unos 200 KB caben entre 3,000 y 6,000 fotos en 1 GB. Sin comprimir (4–8 MB) alcanzarían solo para 150–250.
- Al hacer el procesamiento en el navegador, no se depende de funciones de pago.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| La compresión usa el celular del vendedor y puede tardar en equipos lentos | Mostrar un indicador de progreso |
| Sin límite de tamaño, una foto muy pesada tardaría más en comprimirse | Aceptable: el volumen es pequeño. Si el proyecto creciera, se añadiría un límite |
| Una validación solo en el navegador se puede saltar | Reforzar con la restricción de tipos de archivo del bucket |
| Pueden acumularse archivos huérfanos | Limpieza periódica, aunque sea manual antes de la entrega |
| Dependencia de la cuota del plan gratuito (1 GB y 5 GB al mes) | Compresión obligatoria y revisar el uso antes de la entrega |

## Puntos abiertos

- **Nombre del bucket:** por definir (debe quedar igual en las políticas y en el código).
- **¿Hacen falta miniaturas?** Si el catálogo se ve bien con la misma imagen comprimida, la regla 5 no se aplica.
