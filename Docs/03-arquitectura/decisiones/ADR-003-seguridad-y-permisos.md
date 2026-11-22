# ADR-003 · Seguridad y permisos: control en dos capas

- **Estado:** Propuesta (pendiente de revisión del equipo)
- **Fecha:** 2026-11-22
- **Relacionada con:** [ADR-001](ADR-001-estilo-de-arquitectura.md), [ADR-002](ADR-002-organizacion-del-frontend.md)

> Este ADR describe **reglas y principios**, no el diseño de tablas. La estructura de la base de datos (tablas, columnas y relaciones) la define el análisis de base de datos.

## Contexto

El sistema tiene tres roles: visitante, usuario registrado y vendedor (ver [glosario](../../01-producto/01-glosario.md)). Los permisos más importantes son:

- Cualquiera puede ver el catálogo.
- Solo quien tiene sesión puede ver los datos de contacto de un vendedor.
- Solo el vendedor puede crear, editar y eliminar sus propias publicaciones.

Como no hay servidor propio (ADR-001), el navegador habla directamente con Supabase. Cualquier persona puede saltarse la interfaz y pedir datos a la API, así que los permisos no pueden vivir solo en la pantalla.

## Opciones consideradas

| Opción | Descripción | Resultado |
|---|---|---|
| A. Solo en el frontend | Rutas protegidas y botones ocultos | Descartada: se puede saltar pidiendo los datos directamente a la API |
| B. Solo en la base de datos | Solo RLS | Descartada: segura, pero la experiencia es mala (pantallas vacías o con error) |
| **C. Dos capas** | Rutas protegidas para la experiencia y RLS para la seguridad real | **Elegida** |
| D. Backend propio que valide | Servidor intermedio | Descartada: contradice el ADR-001 |

## Decisión

Los permisos se aplican en **dos capas**:

1. **Frontend (`RutaProtegida`):** redirige y oculta lo que el rol no debe ver. Mejora la experiencia, pero **no se considera seguridad**.
2. **Base de datos (RLS en PostgreSQL y Storage):** es la seguridad real. Niega la operación aunque alguien se salte la interfaz.

### Reglas

1. **Toda restricción importante debe existir en RLS.** Lo que esté solo en el frontend es solo comodidad.
2. **El rol se guarda en la base de datos**, no en los metadatos del usuario de Supabase Auth, porque el propio usuario puede modificar esos metadatos. En cada acción, las políticas RLS verifican el rol consultando la base de datos.
3. **El usuario no puede cambiar su propio rol** una vez registrado.
4. **El registro lo resuelve un trigger de la base de datos.** El formulario envía el tipo de cuenta elegido y el trigger crea el registro del usuario con su rol. El trigger solo acepta los valores permitidos (usuario o vendedor) y, ante cualquier otro valor, asigna el rol de usuario.
5. **Las claves del proyecto van en variables de entorno** y nunca se suben al repositorio. En el navegador solo se usa la clave pública (anon); la clave de servicio (service role) nunca va en el frontend.

### Permisos por tipo de dato

| Dato | Leer | Crear, editar y eliminar |
|---|---|---|
| Publicaciones | Todos, incluidos los visitantes | Solo el vendedor dueño |
| Categorías | Todos | Nadie desde la app |
| Datos de contacto del vendedor | Solo usuarios con sesión | Solo el vendedor dueño |
| Datos públicos del perfil | Todos | Solo el propio usuario (sin poder cambiar su rol) |
| Imágenes (Storage) | Todos | Solo el vendedor dueño |

Esta tabla puede ajustarse cuando el análisis de base de datos defina las entidades finales.

## Consecuencias

**A favor**

- Aunque alguien ignore la interfaz, la base de datos niega lo no permitido.
- No hace falta programar un servidor para validar permisos.
- Las reglas viven en un solo lugar (las políticas RLS) y se pueden revisar.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| Las políticas RLS son un poco difíciles de escribir y probar | Probar cada política con cada rol (visitante, usuario y vendedor) antes de la entrega |
| Una política mal escrita puede dejar datos abiertos | Activar RLS en toda tabla nueva y revisar que ninguna quede sin políticas |

**Riesgo aceptado**

No hay verificación de identidad: cualquier persona puede elegir "vendedor" al registrarse. El alcance lo reconoce como limitación y deja la moderación fuera de esta versión.
