# ADR-006 · Reportes: contadores de interacciones, sin identificar a nadie

- **Estado:** Propuesta
- **Fecha:** 2026-11-23
- **Relacionada con:** [ADR-002](ADR-002-organizacion-del-frontend.md), [ADR-003](ADR-003-seguridad-y-permisos.md)

> Este ADR describe **reglas y principios**, no el diseño de tablas. La estructura de la base de datos la define el análisis de base de datos.

## Contexto

El [alcance](../../01-producto/03-alcance.md) incluye reportes por mes para el dueño de cada negocio y para el administrador. Muestran:

- **Vistas** del detalle de cada publicación.
- **Clics en el teléfono** (WhatsApp).
- **Calificación promedio** y **comentarios nuevos** (estos salen de datos que ya existen, con su fecha).

Hay una vista general y una vista por publicación, con detalle día por día. No se miden ventas ni pedidos, porque la compra se coordina por WhatsApp fuera de la aplicación.

El equipo pidió **no complicar más el negocio**: la idea es un **contador** que vaya contando cada interacción para usarlo después en el reporte. Solo se muestran **cantidades**, no quién vio o quién hizo clic.

## Opciones consideradas

| Opción | Descripción | Resultado |
|---|---|---|
| A. Un solo contador total por publicación | Una cifra que sube con cada interacción | Descartada: no permite reportes por mes ni día por día |
| **B. Un contador por publicación, tipo de interacción y día** | Cada interacción suma 1 al contador de ese día | **Elegida** |
| C. Un registro por cada interacción | Guardar cada vista o clic como una fila con su fecha | Descartada: crece sin parar y guarda más de lo necesario |
| D. Registrar también quién | Guardar la cuenta de quien interactuó | Descartada: complica el negocio y no hace falta para mostrar cantidades |
| E. Herramienta externa de analítica | Un servicio de terceros | Descartada: añade una cuenta y una pieza más (ADR-001) |

## Decisión

Cada interacción medida **suma 1 a un contador**. Hay un contador por **publicación**, por **tipo de interacción** (vista del detalle o clic en WhatsApp) y por **día**. El día sirve para armar después el reporte por mes y el detalle día por día.

Los reportes **solo muestran cantidades**: el navegador suma los contadores del periodo consultado y calcula los totales y los promedios.

### Reglas

1. **Sin identidad:** el contador no guarda quién interactuó, ni su matrícula, ni ningún dato que lo identifique.
2. **La base de datos suma:** el navegador avisa que ocurrió una interacción y una función de la base de datos suma 1 al contador del día (si ese día todavía no tiene contador, empieza en 1). La suma la hace la base de datos y no el navegador, para que no se pierdan cuentas si dos personas interactúan al mismo tiempo.
3. **Las vistas del propio dueño no cuentan:** el navegador no avisa cuando quien mira es el dueño de la publicación.
4. **El navegador hace el trabajo del reporte:** sumar por mes, armar el detalle por día y calcular promedios ocurre en el frontend, dentro de `services/` o en la página del reporte.
5. **Quién ve qué (ADR-003):** el vendedor ve solo los contadores de sus publicaciones y el administrador ve todos. Cualquiera puede sumar a un contador, pero solo mediante la función; nadie puede editarlos ni borrarlos desde la aplicación.
6. **Calificaciones y comentarios:** no necesitan contador; el reporte los calcula con la fecha que ya tienen.

## Consecuencias

**A favor**

- Muy poco que programar: sumar a un contador y leerlos.
- Crece poco: una fila por publicación, tipo y día, sin importar cuánta gente entre.
- No se guardan datos personales de quien navega.
- Permite los reportes por mes y por día que pide el alcance.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| No se puede saber a qué hora ocurrió, ni quién | Es lo que se quiere: el reporte solo necesita cantidades por día |
| Una vez sumado, un conteo no se puede deshacer | Aceptable: las cantidades son informativas y no afectan permisos ni ventas |
| El navegador decide si cuenta o no la vista del dueño, así que alguien podría inflar los contadores llamando a la función directamente | Aceptable en un proyecto escolar: los números no afectan permisos ni ventas |
