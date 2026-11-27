# Documentación de Wissen Web

Página web de publicaciones de ventas para estudiantes emprendedores de la UMT.

## Índice

| Carpeta | Contenido |
|---|---|
| [01-producto](01-producto/) | Qué se va a hacer: idea inicial, glosario, objetivos, alcance y usuarios |
| [02-tecnologias](02-tecnologias/) | Con qué se va a hacer: tecnologías y herramientas, y el PDF de referencia sobre Supabase |
| [03-arquitectura](03-arquitectura/) | Cómo está armado el sistema: decisiones de arquitectura (ADR) y diagramas |
| [04-analisis de base de datos](04-analisis%20de%20base%20de%20datos/) | Análisis de la base de datos: normalización (primera pasada) y material de apoyo |
| [00-manual-de-diseno-de-base-de-datos.md](04-analisis%20de%20base%20de%20datos/incicaciones/00-manual-de-diseno-de-base-de-datos.md) | Manual de apoyo para diseñar la base de datos: método para sacar datos de las pantallas, división de tablas recomendada y decisiones aclaradas (sin código) |
| [01-guia-de-diagramas-e-imagenes.md](04-analisis%20de%20base%20de%20datos/incicaciones/01-guia-de-diagramas-e-imagenes.md) | Cómo entregar el diagrama general y una imagen por tabla con su justificación |
| [02-relaciones-entre-tablas.md](04-analisis%20de%20base%20de%20datos/incicaciones/02-relaciones-entre-tablas.md) | Relaciones entre las tablas, su porqué y qué pasa al eliminar |
| [05-vista](05-vista/) | Cómo se ve y se recorre la aplicación: componentes de las pantallas, secuencias de navegación (Word) y el boceto navegable |
| [02-boceto-no-refinado.html](05-vista/02-boceto-no-refinado.html) | Primer boceto navegable (versión anterior; se conserva solo como referencia) |
| [03-bases-del-diseno.md](05-vista/03-bases-del-diseno.md) | Bases del diseño «Cuaderno»: colores, tipografía, espacios, formas, íconos, estados, responsivo y accesibilidad |
| [04-reglas-css.md](05-vista/04-reglas-css.md) | Reglas de CSS: catálogo de piezas, nombres, archivos de estilo y modularidad |
| [boceto-refinado](05-vista/boceto-refinado/boceto-refinado.html) | Boceto refinado navegable (44 pantallas). Es el que sigue el equipo de React; la aplicación real se hace en React |
| [06-desarrollo](06-desarrollo/00-documento-de-desarrollo.md) | Documento de desarrollo: páginas, piezas que se reutilizan, ventanas, alertas y decisiones sobre los pendientes |

## Orden de lectura recomendado

1. [Idea inicial](01-producto/00-idea-inicial.md)
2. [Glosario](01-producto/01-glosario.md)
3. [Descripción y objetivos](01-producto/02-descripcion-objetivos.md)
4. [Alcance](01-producto/03-alcance.md)
5. [Usuarios](01-producto/04-usuarios.md)
6. [Tecnologías](02-tecnologias/01-tecnologias.md)
7. [Decisiones de arquitectura](#decisiones-de-arquitectura-adr)

## 03-arquitectura

### Decisiones de arquitectura (ADR)

Cada ADR registra una decisión, las opciones que se compararon y por qué se eligió una. El estado de cada una (Aceptada, Rechazada, etc.) está al inicio de su archivo.

| ADR | Decisión |
|---|---|
| [ADR-001](03-arquitectura/decisiones/ADR-001-estilo-de-arquitectura.md) | Estilo de arquitectura: SPA con React y Supabase como backend (BaaS) |
| [ADR-002](03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md) | Organización del frontend por capas |
| [ADR-003](03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md) | Seguridad y permisos en dos capas (rutas protegidas y RLS) |
| [ADR-004](03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md) | Imágenes en Supabase Storage, con validación y compresión en el frontend |
| [ADR-005](03-arquitectura/decisiones/ADR-005-verificacion-de-identidad.md) | Verificación de identidad de los usuarios (**Rechazada**: alargaría el proyecto y añadiría complejidad) |
| [ADR-006](03-arquitectura/decisiones/ADR-006-reportes-con-contadores.md) | Reportes con contadores de interacciones por día: solo cantidades, sin identificar a nadie |
| [ADR-007](03-arquitectura/decisiones/ADR-007-despliegue-y-entornos.md) | Despliegue en Vercel, backend en Supabase y variables de entorno en `.env` fuera de Git |

### Diagramas

Carpeta [diagramas](03-arquitectura/diagramas/): pendiente (contexto, contenedores, componentes, secuencias y despliegue).

## Estado de la documentación

| Parte | Estado |
|---|---|
| Producto (idea, glosario, objetivos, alcance, usuarios) | Listo |
| Vista (componentes de las pantallas y secuencias) y boceto | Listo, alineados con el alcance |
| Tecnologías | Listo |
| ADR 001 a 004 | Aceptadas |
| ADR 005 | Rechazada (se deja como registro de las opciones y del motivo) |
| ADR 006 y 007 | Propuesta, en revisión |
| Refinado visual, reglas de CSS y documento de desarrollo | Instrucciones listas; entrega pendiente del compañero de la vista |
| Diagramas | Pendiente |
| Análisis de base de datos | Primera pasada hecha; falta una segunda para alinearla con los documentos |

## Notas

- Los ADR describen reglas y principios, **no definen tablas ni columnas**. El modelo de datos lo define el análisis de base de datos.
- Puntos abiertos: nombre del bucket de imágenes y si hacen falta miniaturas (ADR-004).
