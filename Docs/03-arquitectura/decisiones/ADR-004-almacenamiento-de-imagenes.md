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
2. **Validación en el navegador:** solo se aceptan JPEG, PNG y WebP; tamaño máximo de 2 MB.
3. **Compresión en el navegador:** antes de subir, se comprime a unos 150–300 KB (idealmente en formato WebP).
4. **Versiones reducidas en el navegador:** si hace falta una miniatura para el catálogo, se genera en el frontend al subir la imagen, no con transformaciones de Supabase.
5. **Nombre único:** cada archivo se renombra con un UUID, para que dos archivos con el mismo nombre no se sobrescriban.
6. **Ruta con el identificador del vendedor:** permite que la política de Storage compruebe que solo el dueño modifica sus archivos.
7. **Permisos (ADR-003):** lectura pública. Subir, editar y borrar solo el vendedor dueño.
8. **Limpieza:**
   - Al eliminar una publicación, se elimina también su imagen.
   - Se revisan periódicamente las imágenes subidas que nunca quedaron en una publicación (archivos huérfanos).

## Consecuencias

**A favor**

- La base de datos se mantiene ligera y las imágenes se entregan por CDN.
- No hay servidor que mantener.
- Con imágenes de unos 200 KB caben entre 3,000 y 6,000 fotos en 1 GB. Sin comprimir (4–8 MB) alcanzarían solo para 150–250.
- Al hacer el procesamiento en el navegador, no se depende de funciones de pago.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| La compresión usa el celular del vendedor y puede tardar en equipos lentos | Mostrar indicador de progreso y limitar el tamaño de entrada |
| Una validación solo en el navegador se puede saltar | Reforzar con los límites del bucket (tipos de archivo y tamaño máximo) |
| Pueden acumularse archivos huérfanos | Limpieza periódica, aunque sea manual antes de la entrega |
| Dependencia de la cuota del plan gratuito (1 GB y 5 GB al mes) | Compresión obligatoria y revisar el uso antes de la entrega |

## Puntos abiertos

- **Nombre del bucket:** por definir (debe quedar igual en las políticas y en el código).
- **¿Hacen falta miniaturas?** Si el catálogo se ve bien con la misma imagen comprimida, la regla 4 no se aplica.
