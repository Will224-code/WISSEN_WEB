# Día 1 · Persona S · Supabase

**Hoy:** crear el proyecto, **las 11 tablas** y pasar las claves al equipo.
**Antes de empezar:** lee [00-para-los-tres.md](00-para-los-tres.md). Tu base es el [manual de diseño de base de datos](../../04-analisis%20de%20base%20de%20datos/incicaciones/00-manual-de-diseno-de-base-de-datos.md) (secciones 5 y 6) y el [ADR-003](../../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md).

> Hoy **no** haces autenticación ni permisos (RLS). Eso es mañana y el domingo. Hoy solo estructura.

---

## Tareas, en orden

### 1. Crear el proyecto (30 min)
- [ ] Crear el proyecto en Supabase (plan gratuito). Anota la región y la contraseña de la base en un lugar seguro.
- [ ] Copiar `Project URL` y `anon public key` (Settings → API). **No uses la `service_role key` en el frontend jamás.**
- [ ] Pasarlas a A y B por mensaje privado.
- [ ] En el repo, `Proyecto_web/wissen-web/.env.example` con `VITE_SUPABASE_URL=` y `VITE_SUPABASE_ANON_KEY=` (sin valores). Confirma que `.env` está en `.gitignore`.
- [ ] La rama `origin/supabase-setup` ya trae `src/lib/supabaseClient.js`. Revísalo y úsalo; si está bien, súbelo a `main` hoy para que A y B lo usen.

### 2. Crear las tablas (2–3 h)

Usa el **SQL Editor** de Supabase y **guarda cada script** en `Proyecto_web/wissen-web/supabase/` (por ejemplo `01-tablas.sql`) para que quede en Git y se pueda repetir si algo se rompe.

| # | Tabla | Notas importantes |
|---|---|---|
| 1 | `usuario` | `id` = el mismo `id` de `auth.users`. Matrícula **única**, rol (`usuario` / `administrador`), `es_maestro` (solo uno), carrera (Enfermería, Educación, Software, Veterinaria, Contaduría), foto opcional |
| 2 | `categoria` | Nombre **único**. Una marca para «Otro» (no se edita ni se elimina) |
| 3 | `negocio` | Dueño (`usuario_id`), `categoria_id`, nombre, teléfono como **texto**, descripción y foto opcionales |
| 4 | `credencial_negocio` | Solo `negocio_id` y contraseña **con hash**. Tabla aparte a propósito |
| 5 | `red_social_negocio` | Negocio, red (Facebook / Instagram / Twitter), enlace. Máximo 3 por red |
| 6 | `publicacion` | `negocio_id`, nombre, precio, tipo (Producto / Servicio), descripción, `creado_en`. **Sin teléfono** (sale del negocio) |
| 7 | `imagen_publicacion` | `publicacion_id`, URL, `orden`. Máximo 10 por publicación |
| 8 | `comentario` | `publicacion_id`, `usuario_id`, texto, `creado_en` |
| 9 | `calificacion` | `publicacion_id`, `usuario_id`, estrellas 1–5, fecha. **Única por pareja** usuario + publicación |
| 10 | `contador_interaccion` | `publicacion_id`, tipo (vista / clic_whatsapp), día, cantidad. **Única por publicación + tipo + día.** No guarda quién |
| 11 | `bitacora_baja` | Tipo de baja, datos como **texto**, quién lo hizo (texto), cuándo. **Sin llaves foráneas** (debe sobrevivir a las bajas) |

Checklist de calidad al crear cada tabla:
- [ ] Llave primaria y llaves foráneas con `on delete` pensado (publicación borrada → se borran sus imágenes y comentarios; negocio borrado → sus publicaciones).
- [ ] `check` para estrellas (1–5), tipo (Producto/Servicio), máximo de redes y de imágenes.
- [ ] `unique` donde toca: matrícula, nombre de categoría, calificación por usuario+publicación, contador por publicación+tipo+día.
- [ ] Nombres en `snake_case`, en español, **sin tildes ni ñ**.

### 3. Dejar a mano el contrato de datos (30 min)
- [ ] Crear `Proyecto_web/wissen-web/supabase/README.md` (o un mensaje en el grupo) con **cada tabla y sus columnas exactas**. B lo usa para que los datos simulados tengan los mismos nombres.
- [ ] Si cambias un nombre después, avisa a A y B antes.

---

## Decisión que te toca hoy (con el equipo)

**Login con matrícula.** Supabase Auth pide correo. Propuesta ya acordada: correo interno `matricula@wissen.local`.
- [ ] Authentication → Providers → Email: **desactiva «Confirm email»** (si no, nadie puede entrar porque `@wissen.local` no recibe correos).
- [ ] Deja anotado en el README que el frontend construirá el correo a partir de la matrícula (`${matricula}@wissen.local`).

---

## Listo cuando…

- [ ] Las 11 tablas existen y se ven en **Table Editor**.
- [ ] Los scripts `.sql` están en el repo.
- [ ] A y B tienen `URL`, `anon key` y la lista de columnas.
- [ ] Desde el frontend se puede hacer un `select` a `categoria` sin error de conexión (aunque devuelva vacío; los permisos llegan el domingo). *Si devuelve vacío o error por RLS, es normal: significa que sí llega.*

## Si te sobra tiempo (adelanto de mañana)

- Activar Authentication y crear el administrador maestro (`00000001`).
- Cargar las 15 categorías + «Otro».
- Cargar datos de prueba iguales a los del boceto (Dulces Sofi, Brownies de chocolate…) para que B los vea cuando cambie de simulado a real.

## Si te atoras

- Algo del diseño no cuadra con el manual → anótalo en el grupo, **decide y avanza**; el plan permite ajustes pequeños.
- Supabase lento o pausado → no bloquea: A y B siguen con servicios simulados. Pero avisa para planear el recorte.
