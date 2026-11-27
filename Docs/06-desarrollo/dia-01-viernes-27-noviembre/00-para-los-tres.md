# Día 1 · Viernes 27 de noviembre · Arranque

**Quedan 5 días más:** sáb 28 · dom 29 · lun 30 · mar 1 (integración) · mié 2 (entrega, solo correcciones).
**Plan completo:** [01-plan-de-desarrollo.md](../01-plan-de-desarrollo.md)

> Este archivo es lo que **leen los tres** antes de empezar. Después, cada quien abre el suyo:
> [S · Supabase](01-persona-S-supabase.md) · [A · Base, cuentas y vendedor](02-persona-A-base-cuentas-vendedor.md) · [B · Público y administrador](03-persona-B-publico-y-administrador.md)

---

## 1. Reunión de arranque (30 min, los tres, antes de tocar código)

Se acuerda esto y se anota en el grupo:

1. **Contrato de servicios** ([sección 4 del plan](../01-plan-de-desarrollo.md#4-contrato-entre-frontend-y-supabase)). Todas las funciones devuelven `{ datos, error }`. El `error` es un **código** de `mensajes.js` (`ACC-01`…), no un texto.
2. **Nombres de campo.** Los fija S a partir del [manual de base de datos](../../04-analisis%20de%20base%20de%20datos/incicaciones/00-manual-de-diseno-de-base-de-datos.md). Todo en `snake_case` y en español (`nombre`, `precio`, `negocio_id`, `creado_en`…). Si S cambia un nombre, avisa antes.
3. **Las cuatro decisiones de la [sección 5 del plan](../01-plan-de-desarrollo.md#5-decisiones-que-hay-que-tomar-el-viernes):**
   - Login con matrícula → la matrícula es el correo interno: `matricula@wissen.local`. Sin verificación de correo.
   - Contraseña del negocio → con hash, se verifica con una función de la base de datos (nunca desde el cliente).
   - Administrador maestro → matrícula `00000001`, creado a mano.
   - Las vistas del dueño de un negocio no cuentan.
4. **Quién hace qué hoy** (resumen abajo) y **a qué hora se hace el primer `push`**.

## 2. Resumen del día

| Persona | Meta de hoy | Debe estar subido a Git a las… |
|---|---|---|
| **S · Supabase** | Proyecto creado, **11 tablas**, claves compartidas | 14:00 (tablas) |
| **A · Base y cuentas** | `src/` ordenado, **estilos base y piezas básicas** (`Boton`, `Campo`, `Tarjeta`, `Alerta`) | 13:00 (variables + base + estructura de carpetas) |
| **B · Público** | **Servicios simulados** de todo el contrato + `TarjetaPublicacion`, `Galeria`, `Etiqueta`, `Buscador` con datos fijos | 12:00 (servicios simulados) |

> Las horas son sugeridas; ajústenlas, pero **fijen una** y cúmplanla. Lo que bloquea a los demás se sube primero: los servicios simulados de B y los estilos de A.

## 3. Quién bloquea a quién hoy

```
B (servicios simulados) ──▶ A y S los leen: ven qué devuelve cada función
A (variables + base CSS) ─▶ B los usa para sus piezas
S (nombres de campo)    ──▶ B los usa en los datos simulados (mismos nombres que tendrá la base)
```

- **B no espera a A:** empieza por los servicios (no usan CSS) y después sigue con las piezas.
- **A no espera a nadie:** el CSS base sale del [boceto](../../boceto-refinado.html) y las [reglas de CSS](../../05-vista/04-reglas-css.md).
- **S no espera a nadie:** el diseño de tablas ya está en el manual.

## 4. Reglas de Git (desde hoy)

- Una rama por persona: `supabase`, `a-base`, `b-publico`. Hay ya en el remoto `supabase-setup` y `kevin-desarrollor`; S puede seguir usando la primera.
- `main` siempre compila (`npm run build`). Antes de unir: correr `npm run build` y `npm run lint`.
- Commits cortos y en español: `Agrega variables CSS y estilos base`.
- **No se sube** `.env` (ADR-007). Se pasa por mensaje privado. Sí se sube `.env.example` con los nombres vacíos:
  ```
  VITE_SUPABASE_URL=
  VITE_SUPABASE_ANON_KEY=
  ```
- Dos personas **no tocan el mismo archivo**. Si algo de otro hace falta, se avisa en el grupo.
- Al final del día: Pull Request a `main`, revisado por otra persona, y todos hacen `git pull`.

## 5. Cierre del día (últimos 15 min, los tres)

Revisar juntos, en este orden:

1. ¿`main` compila y arranca con `npm run dev`?
2. ¿S pasó `URL` y `anon key`, y la lista de tablas con sus columnas?
3. ¿B subió los servicios simulados y A los estilos base?
4. ¿Hay algo bloqueado para mañana? Se anota en el grupo **hoy**, no mañana.

Mañana (sáb 28): S → autenticación y datos de prueba · A → barra, `AuthContext`, `RutaProtegida`, Inicio de sesión y Registro · B → `Catalogo` y `BarraFiltros`.
