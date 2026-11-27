# Día 1 · Persona A · Base, cuentas y vendedor

**Hoy:** dejar el proyecto ordenado y los **estilos base + piezas básicas** listos. Tu trabajo es lo primero que B necesita para no frenarse.
**Antes de empezar:** lee [00-para-los-tres.md](00-para-los-tres.md), las [reglas de CSS](../../05-vista/04-reglas-css.md), las [bases del diseño](../../05-vista/03-bases-del-diseno.md) y el [ADR-002](../../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md). Abre el [boceto refinado](../../boceto-refinado.html) en el navegador: es tu referencia visual.

> Tu meta del día: que a las **13:00** estén subidos `00-variables.css`, `01-base.css` y la estructura de carpetas, para que B pueda construir encima.

---

## Tareas, en orden

### 1. Preparar el proyecto (30–45 min)
Ruta de trabajo: `Proyecto_web/wissen-web/`

- [ ] `git checkout -b a-base`.
- [ ] `npm install` y `npm install lucide-react` (los íconos, ver sección 8 del documento de desarrollo).
- [ ] Borrar lo que sobra del scaffold de Vite: contenido de `App.jsx`, `App.css`, `index.css`, `assets/react.svg`, `assets/vite.svg`, `assets/hero.png`.
- [ ] Crear la estructura (según sección 8 del [documento de desarrollo](../00-documento-de-desarrollo.md)):
  ```
  src/
    components/
      estructura/      (BarraSuperior, Pestanas, Pagina, Hoja, Marca…)
    context/           (AuthContext)
    lib/               (supabaseClient.js, mensajes.js)
    pages/
    routes/
    services/          (B lo llena hoy)
    styles/
      00-variables.css
      01-base.css
      02-estructura.css
      piezas/          (un .css por pieza)
      paginas/
  ```
- [ ] `src/main.jsx` importa los tres CSS globales en ese orden (00 → 01 → 02).
- [ ] Subir a `a-base` y avisar al grupo.

### 2. Estilos base (1.5–2 h)
Salen del boceto y de las reglas de CSS. No inventes valores: **cópialos del boceto**.

- [ ] `00-variables.css`: colores, tipografías, espacios, radios, sombras (todo como variables `--nombre`).
- [ ] `01-base.css`: reinicio, tipografía, fondo, enlaces, enfoque visible.
- [ ] `02-estructura.css`: lo que usan las estructuras de página (papel cuadriculado, hoja con margen, anchos).
- [ ] Cargar las fuentes del boceto (Google Fonts en `index.html` o en `public/`).

> **Subir y avisar a B en cuanto estos tres archivos estén listos**, aunque las piezas no.

### 3. Piezas básicas (2 h)
Cada pieza = `src/components/Nombre.jsx` + `src/styles/piezas/nombre.css`. **Un `.css` por pieza**, sin estilos sueltos en las páginas.

| Pieza | Variantes que necesita ya | Se usa en |
|---|---|---|
| `Boton` | primario, confirmar, peligro, secundario, texto, chico, ancho | Todas |
| `Campo` | texto, contraseña, número, área, lista; con etiqueta y error | Todos los formularios |
| `Tarjeta` | contenedor blanco | Formularios, perfiles |
| `Alerta` | error en línea | Todos los formularios |
| `Icono` | envoltorio de `lucide-react` (chico, normal, grande) | Todas |

- [ ] Cada pieza probada en una página temporal (por ejemplo `pages/Pruebas.jsx`, **no se sube a `main`**) comparando con el boceto.

### 4. Si aún queda tiempo
- [ ] `lib/mensajes.js`: un objeto con **los códigos y textos exactos** de la [sección 6 del documento de desarrollo](../00-documento-de-desarrollo.md) (ACC-01…, FRM-01…, SIS-01…). Es lo que usan `Alerta` y los servicios.
- [ ] Empezar `Avatar`, `Cargando` y `Marca`.

---

## Pull Request de hoy
Rama `a-base` → `main`. Debe incluir: estructura de carpetas, 3 CSS globales, `Boton`, `Campo`, `Tarjeta`, `Alerta`, `Icono`.
- [ ] `npm run build` sin errores · [ ] `npm run lint` sin errores.
- [ ] B (o S) lo revisa y se une. Todos hacen `git pull`.

## Listo cuando…
- [ ] `npm run dev` arranca en blanco, sin errores en consola.
- [ ] Las 5 piezas se ven **igual que el boceto**.
- [ ] B ya puede importar `Boton`, `Tarjeta` y las variables CSS.

## Reglas que cuidas hoy (para todo el proyecto)
- Un archivo `.css` por pieza; sin estilos propios en las páginas.
- Componentes en `PascalCase`; archivos CSS en `kebab-case`.
- Nada de colores o tamaños escritos a mano: **siempre variables**.

## Mañana (sáb 28)
`BarraSuperior`, `Pestanas`, `Pagina`, `Ventana`, `AuthContext`, `RutaProtegida`, **Inicio de sesión y Registro**. Para entonces S ya tendrá la autenticación.
