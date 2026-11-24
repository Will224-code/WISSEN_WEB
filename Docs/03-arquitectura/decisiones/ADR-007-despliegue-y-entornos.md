# ADR-007 · Despliegue y entornos: Vercel, Supabase y variables de entorno

- **Estado:** Propuesta
- **Fecha:** 2026-11-23
- **Relacionada con:** [ADR-001](ADR-001-estilo-de-arquitectura.md), [ADR-003](ADR-003-seguridad-y-permisos.md)

## Contexto

La aplicación es una SPA con React y Vite (ADR-001) que habla directamente con Supabase. No hay servidor propio, así que hay que decidir dónde se publica el frontend y cómo se manejan las claves sin filtrarlas. La entrega es el 2 de diciembre de 2026.

## Opciones consideradas

| Opción | Descripción | Resultado |
|---|---|---|
| **A. Vercel** | Hospedaje gratuito para el frontend, con HTTPS y publicación desde GitHub | **Elegida** |
| B. Netlify | Equivalente a Vercel | Descartada: el equipo ya eligió Vercel |
| C. GitHub Pages | Hospedaje estático de GitHub | Descartada: las rutas de una SPA exigen más configuración |
| D. Servidor propio | Hospedar la aplicación nosotros | Descartada: contradice el ADR-001 |

## Decisión

- **Frontend:** se publica en **Vercel**, conectado al repositorio de GitHub.
- **Backend:** el proyecto de **Supabase** en la nube (plan gratuito).
- **Entornos:** dos, **local** (servidor de desarrollo de Vite) y **producción** (Vercel).

### Variables de entorno

1. **En local** las claves van en un archivo **`.env`**, que se agrega al **`.gitignore`** para que nunca se suba al repositorio.
2. **En producción** se configuran en el panel de Vercel (variables de entorno del proyecto), no en un archivo.
3. **Solo se usan la URL del proyecto y la clave pública (anon)** de Supabase. En Vite, las variables que empiezan con `VITE_` quedan **visibles en el navegador**, así que nada secreto debe llevar ese prefijo. La clave de servicio (service role) **nunca** se usa en el frontend (ADR-003).
4. **Se sube un `.env.example`** con los nombres de las variables y sin valores, para que cualquier compañero sepa cuáles crear.

### Publicación

- Cada cambio que llega a la rama principal publica una nueva versión.
- La aplicación está dentro de la carpeta `Proyecto_web/wissen-web`, así que en Vercel esa debe configurarse como carpeta raíz del proyecto.
- Como es una SPA, hay que indicar a Vercel que cualquier ruta (por ejemplo `/publicacion/:id`) cargue `index.html`. Sin esto, recargar la página en una ruta interna da error 404. Se hace con un archivo `vercel.json`.

## Consecuencias

**A favor**

- Sin servidor que mantener y sin costo.
- La clave pública no es secreta por diseño: la seguridad real está en RLS (ADR-003).
- Publicar es automático al subir cambios.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| Si `.env` no está en `.gitignore`, las claves se filtran | **Pendiente:** el `.gitignore` actual del proyecto solo ignora `*.local`, no `.env`. Hay que agregarlo antes de crear el archivo |
| Una clave sensible con prefijo `VITE_` quedaría pública | Revisar que solo la URL y la clave pública usen ese prefijo |
| Supabase pausa el proyecto gratuito tras 7 días sin actividad | Abrir la aplicación antes de la entrega para comprobar que esté activa |
| Dependencia del plan gratuito de Vercel | Suficiente para el uso esperado de la comunidad UMT |
