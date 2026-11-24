# ADR-003 · Seguridad y permisos: control en dos capas

- **Estado:** Aceptado
- **Fecha:** 2026-11-22 (actualizado 2026-11-23: se agrega el rol administrador, con maestro e hijos, baja permanente y bitácora; y los permisos de los reportes y de las categorías)
- **Relacionada con:** [ADR-001](ADR-001-estilo-de-arquitectura.md), [ADR-002](ADR-002-organizacion-del-frontend.md)

> Este ADR describe **reglas y principios**, no el diseño de tablas. La estructura de la base de datos (tablas, columnas y relaciones) la define el análisis de base de datos.

## Contexto

El sistema tiene cuatro roles: visitante, usuario registrado, vendedor y administrador (ver [glosario](../../01-producto/01-glosario.md) y [usuarios](../../01-producto/04-usuarios.md)). Los permisos más importantes son:

- Cualquiera puede ver el catálogo.
- Cualquiera puede ver los datos de contacto de un vendedor en su publicación.
- Solo quien tiene sesión puede calificar y comentar.
- Solo el vendedor puede crear, editar y eliminar sus propias publicaciones.
- El administrador controla el sistema: puede dar de baja (eliminar de forma permanente) publicaciones, comentarios, usuarios, negocios y otros administradores, agregar, editar y eliminar categorías, consultar los reportes de todo el sistema y agregar otros administradores.
- Hay dos clases de administrador: el **administrador maestro** (uno solo, que nunca se elimina) y los **administradores hijos** (los que el maestro u otro administrador agregan). El sistema nunca se queda sin administrador.

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
3. **El usuario no puede cambiar su propio rol** una vez registrado. La única excepción es el administrador, que puede otorgar el rol de administrador a otro usuario registrado; esa política RLS comprueba que quien lo hace sea administrador en la base de datos. Los administradores agregados así son **hijos**.
4. **El registro lo resuelve un trigger de la base de datos.** El formulario **no envía un tipo de cuenta**: el trigger siempre crea el registro con el rol de usuario, sea cual sea lo que envíe el navegador. Se pasa a vendedor al crear un negocio. **El rol administrador nunca se asigna desde el registro**: el equipo da de alta la cuenta del **administrador maestro** directamente en la base de datos (panel de Supabase) y, a partir de ahí, un administrador puede agregar más, que serán hijos (regla 3). Al iniciar sesión con matrícula y contraseña, el sistema lee el rol guardado en la base de datos y decide qué pantallas muestra.
5. **El administrador también se valida en RLS.** Las políticas de baja y de administración de categorías comprueban que el rol guardado en la base de datos sea administrador; ocultar el panel en el frontend no basta.
6. **"Dar de baja" es eliminar de forma permanente y no se puede recuperar.** Eliminar una cuenta de Supabase Auth requiere la clave de servicio, que nunca va en el frontend (regla 8); por eso la baja se hace con una **función de la base de datos** que primero comprueba que quien la llama sea administrador y después elimina el registro y todo lo que depende de él (por ejemplo, los negocios, publicaciones, comentarios e imágenes de un usuario).
7. **Cada baja queda registrada en una bitácora.** Antes de eliminar, la función guarda qué se dio de baja (tipo y datos básicos de identificación, como matrícula o nombre del negocio), qué administrador lo hizo y cuándo. La bitácora **sobrevive a la baja**, solo la lee un administrador y nadie puede editarla ni borrarla desde la app. Se guardan los datos mínimos necesarios para saber qué cuentas fueron eliminadas, no su contenido.
8. **Las claves del proyecto van en variables de entorno** y nunca se suben al repositorio. En el navegador solo se usa la clave pública (anon); la clave de servicio (service role) nunca va en el frontend.
9. **El administrador maestro es intocable y siempre hay uno.** Ninguna política RLS ni función permite dar de baja al maestro ni quitarle el rol (ni él a sí mismo). Cualquier administrador puede dar de baja a los administradores hijos. Así el sistema nunca se queda sin administrador.
10. **Las contraseñas nunca se guardan en texto plano.** Las de las cuentas las gestiona Supabase Auth (cifradas). Si se mantiene una contraseña propia por negocio (como en el prototipo), se guarda cifrada con hash en la base de datos y se verifica allí mismo, nunca en el navegador. Esa contraseña existe por seguridad: si alguien llega a dejar la cuenta de usuario abierta o pública, el dueño aún puede salvar su negocio, porque modificarlo o eliminarlo exige esa contraseña. Para que cumpla su función, la comprobación la hace la base de datos al ejecutar la acción; si solo se comprobara en la pantalla, se podría saltar pidiendo los datos directamente a la API.

### Permisos por tipo de dato

| Dato | Leer | Crear, editar y eliminar |
|---|---|---|
| Publicaciones | Todos, incluidos los visitantes | Vendedor dueño; el administrador puede dar de baja cualquiera |
| Categorías | Todos | Solo el administrador (crear, editar y eliminar; al eliminar, sus negocios pasan a «Otro») |
| Datos de contacto del vendedor | Todos, incluidos los visitantes | Solo el vendedor dueño |
| Datos públicos del perfil | Todos | El propio usuario (sin poder cambiar su rol); el administrador puede dar de baja |
| Negocios | Todos | Vendedor dueño; el administrador puede dar de baja |
| Comentarios | Todos | Usuario autor (crear, editar y eliminar los suyos); el administrador puede eliminar cualquiera |
| Imágenes (Storage) | Todos | Vendedor dueño; el administrador puede borrar |
| Administradores | Solo administradores | El maestro y los hijos pueden agregar hijos y dar de baja hijos; nadie puede dar de baja al maestro |
| Bitácora de bajas | Solo administradores | Solo la función de baja escribe en ella; nadie edita ni borra |
| Contadores de los reportes (vistas y clics en WhatsApp, uno por publicación y día; ver [ADR-006](ADR-006-reportes-con-contadores.md)) | El vendedor dueño ve los de sus publicaciones; el administrador ve todos | Cualquiera puede sumar a un contador, pero solo mediante una función que no guarda quién fue; nadie edita ni borra |
| Calificaciones (con la fecha de cada una) | Todos | Usuario autor (una por publicación, que puede cambiar; al cambiarla se guarda la nueva fecha) |

Esta tabla puede ajustarse cuando el análisis de base de datos defina las entidades finales.

## Consecuencias

**A favor**

- Aunque alguien ignore la interfaz, la base de datos niega lo no permitido.
- No hace falta programar un servidor para validar permisos.
- Las reglas viven en un solo lugar (las políticas RLS) y se pueden revisar.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| Las políticas RLS son un poco difíciles de escribir y probar | Probar cada política con cada rol (visitante, usuario, vendedor y administrador) antes de la entrega |
| Una política mal escrita puede dejar datos abiertos | Activar RLS en toda tabla nueva y revisar que ninguna quede sin políticas |

**Riesgo aceptado**

No hay verificación de identidad en esta versión: cualquier persona con matrícula y contraseña puede registrarse y crear un negocio, y la matrícula no se comprueba (alguien podría usar la de otra persona). El alcance lo reconoce como limitación; la moderación es manual y la hace el administrador dando de baja lo que corresponda. Verificar la identidad se propuso en el [ADR-005](ADR-005-verificacion-de-identidad.md) y el equipo lo rechazó por el tiempo y la complejidad que añadiría.

Una cuenta de administrador comprometida tiene control total, por lo que cada administrador usa su propia cuenta (no se comparten) y el rol solo se asigna a pocas personas del equipo. Como la baja es permanente, el administrador debe confirmar antes de eliminar.
