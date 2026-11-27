# Día 1 · Persona B · Público y administrador

**Hoy:** crear los **servicios simulados** de todo el contrato y las primeras piezas del catálogo con datos fijos.
**Antes de empezar:** lee [00-para-los-tres.md](00-para-los-tres.md), el [contrato (sección 4 del plan)](../01-plan-de-desarrollo.md#4-contrato-entre-frontend-y-supabase) y el [ADR-002](../../03-arquitectura/decisiones/ADR-002-organizacion-del-frontend.md). Abre el [boceto refinado](../../boceto-refinado.html): de ahí salen los datos de prueba.

> Tu meta del día: que a las **12:00** estén subidos los servicios simulados. Con eso A y S ven qué debe devolver cada función, y mañana tú ya trabajas con datos.

---

## Tareas, en orden

### 1. Servicios simulados (3 h) ← lo más importante del día
Ruta: `Proyecto_web/wissen-web/src/services/`

- [ ] `git checkout -b b-publico`.
- [ ] Un archivo por servicio: `auth.js`, `usuarios.js`, `negocios.js`, `categorias.js`, `publicaciones.js`, `imagenes.js`, `calificaciones.js`, `comentarios.js`, `administracion.js`, `reportes.js`.
- [ ] Cada uno exporta **exactamente** las funciones del contrato. Ejemplo:
  ```js
  // src/services/categorias.js  (versión simulada)
  import { CATEGORIAS } from './datos-simulados'

  export async function listar() {
    return { datos: CATEGORIAS, error: null }
  }
  ```
- [ ] **Todas** devuelven `{ datos, error }`. En caso de fallo, `error` es un **código** (`'ACC-01'`), no un texto.
- [ ] `datos-simulados.js` con los datos del boceto: Dulces Sofi, Brownies de chocolate, etc. Incluye varias categorías (con «Otro»), 6–10 publicaciones con 2–3 imágenes, comentarios, un negocio con redes sociales, usuarios de cada rol.
- [ ] `publicaciones.listar(filtros, pagina)` debe **paginar de 10 en 10** y filtrar por texto, categoría y tipo (decisión 4 del documento de desarrollo).
- [ ] Usa los **nombres de campo que pase S** (`snake_case`, en español). Hasta que los tenga, usa los del manual y ajusta después.
- [ ] Simula una espera corta (`await new Promise(r => setTimeout(r, 300))`) para ver el estado `Cargando`.

> Hoy se escribe el contrato. El martes cada archivo cambia su cuerpo por la llamada real a Supabase **sin que cambie lo que devuelve**. Por eso hay que hacerlo bien ahora.

### 2. Piezas del catálogo (2–3 h)
Cada pieza = `src/components/Nombre.jsx` + `src/styles/piezas/nombre.css`. Usa las variables de A (`00-variables.css`) cuando las suba (~13:00). Mientras tanto, trabaja la estructura HTML y los datos.

| Pieza | Qué hace | Datos de prueba |
|---|---|---|
| `Etiqueta` | Dato corto (categoría, tipo) | texto fijo |
| `TarjetaPublicacion` | Ficha con cinta: imagen, nombre, precio, negocio | una publicación simulada |
| `Galeria` | Imagen principal, contador y miniaturas | 3 imágenes |
| `Buscador` | Cuadro con lupa | controlado con `useState` |

- [ ] Probarlas en una página temporal (`pages/PruebasB.jsx`, **no se sube a `main`**) y comparar con el boceto.
- [ ] Si A aún no subió los estilos base, **haz `git pull` cada hora** hasta que estén.

### 3. Si queda tiempo
- [ ] `Precio`, `EstadoVacio` (con los textos `VAC-01` a `VAC-03` de la [sección 6.4](../00-documento-de-desarrollo.md)).
- [ ] Adelantar `Catalogo.jsx` con lo que ya tienes (mañana lo conectas con `BarraFiltros`).

---

## Pull Request de hoy
Rama `b-publico` → `main`. Debe incluir: `src/services/` completo (simulado) y las 4 piezas.
- [ ] `npm run build` sin errores · [ ] `npm run lint` sin errores.
- [ ] A (o S) lo revisa y se une. Todos hacen `git pull`.

## Listo cuando…
- [ ] Las **10 funciones de servicio** del contrato existen y devuelven `{ datos, error }`.
- [ ] Desde cualquier componente se puede hacer `const { datos } = await publicaciones.listar({}, 1)` y salen 10 o menos publicaciones del boceto.
- [ ] `TarjetaPublicacion` y `Galeria` se ven como el boceto.

## Reglas que cuidas hoy
- **Nada de datos escritos dentro de los componentes.** Los datos viven en `services/` y los componentes los piden.
- Un `.css` por pieza. Sin estilos sueltos en la página.
- Si necesitas una pieza que es de A (`Boton`, `Tarjeta`…), **espera a su `push`** o avísale. No la copies.

## Mañana (sáb 28)
`Catalogo` y `BarraFiltros` con los servicios. Después (dom 29): `DetallePublicacion`, `ListadoNegocios`, `PerfilNegocio`, estrellas y comentarios.
