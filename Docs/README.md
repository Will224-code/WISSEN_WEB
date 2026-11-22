# Documentación de Wissen Web

Página web de publicaciones de ventas para estudiantes emprendedores de la UMT.

## Índice

| Carpeta | Contenido |
|---|---|
| [01-producto](01-producto/) | Qué se va a hacer: idea inicial, glosario, objetivos y alcance |
| [02-tecnologias](02-tecnologias/) | Con qué se va a hacer: tecnologías y herramientas, y el PDF de referencia sobre Supabase |
| [03-arquitectura](03-arquitectura/) | Cómo está armado el sistema: decisiones de arquitectura (ADR), diagramas y documento principal |
| [04-analisis de base de datos](04-analisis%20de%20base%20de%20datos/) | Análisis de la base de datos: normalización (primera pasada) y material de apoyo |

## Orden de lectura recomendado

1. [Idea inicial](01-producto/00-idea-inicial.md)
2. [Glosario](01-producto/01-glosario.md)
3. [Descripción y objetivos](01-producto/02-descripcion-objetivos.md)
4. [Alcance](01-producto/03-alcance.md)
5. [Tecnologías](02-tecnologias/04-tecnologias.md)
6. [Decisiones de arquitectura](#decisiones-de-arquitectura-adr)

## 03-arquitectura

### Decisiones de arquitectura (ADR)

Cada ADR registra una decisión, las opciones que se compararon y por qué se eligió una. Todas están en estado **Propuesta**, pendientes de aprobación del equipo.

| ADR | Decisión |
|---|---|
| [ADR-001](03-arquitectura/decisiones/ADR-001-estilo-de-arquitectura.md) | Estilo de arquitectura: SPA con React y Supabase como backend (BaaS) |
| [ADR-002](03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md) | Organización del frontend por capas |
| [ADR-003](03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md) | Seguridad y permisos en dos capas (rutas protegidas y RLS) |
| [ADR-004](03-arquitectura/decisiones/ADR-004-almacenamiento-de-imagenes.md) | Imágenes en Supabase Storage, con validación y compresión en el frontend |

### Diagramas

Carpeta [diagramas](03-arquitectura/diagramas/): pendiente (contexto, contenedores, componentes, secuencias y despliegue).

### Documento principal

[05-arquitectura-software.md](03-arquitectura/05-arquitectura-software.md) es un **borrador anterior que se va a reescribir** a partir de los ADR y los diagramas. No debe tomarse como fuente.

## Estado de la documentación

| Parte | Estado |
|---|---|
| Producto (idea, glosario, objetivos, alcance) | Listo |
| Tecnologías | Listo |
| ADR 001 a 004 | Propuesta, en revisión |
| Diagramas | Pendiente |
| Documento principal de arquitectura | Pendiente de reescribir |
| Análisis de base de datos | Primera pasada hecha; falta una segunda para alinearla con los documentos |

## Notas

- Los ADR describen reglas y principios, **no definen tablas ni columnas**. El modelo de datos lo define el análisis de base de datos.
- Puntos abiertos: nombre del bucket de imágenes y si hacen falta miniaturas (ADR-004).
