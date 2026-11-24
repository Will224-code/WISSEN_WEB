# ADR-005 · Verificación de identidad de los usuarios

- **Estado:** Rechazada
- **Fecha:** 2026-11-23
- **Relacionada con:** [ADR-003](ADR-003-seguridad-y-permisos.md)

> El equipo **rechazó** este ADR: no se verifica la identidad en esta versión. Se deja el documento como registro de las opciones que se consideraron y del motivo del rechazo (ver [alcance](../../01-producto/03-alcance.md)).

## Contexto

El registro pide matrícula, datos personales y contraseña, y el inicio de sesión usa la matrícula. Nada comprueba que quien se registra sea realmente el estudiante dueño de esa matrícula ni que sea de la UMT. Consecuencias:

- Una persona puede registrarse con la matrícula de otra (suplantación).
- Una persona ajena a la UMT puede registrarse y crear un negocio.
- Quien se registre primero con una matrícula impide que su verdadero dueño la use.

Hoy el único control es la moderación manual: el administrador da de baja lo que corresponda ([ADR-003](ADR-003-seguridad-y-permisos.md)).

## Opciones consideradas

| Opción | Descripción | Esfuerzo | Qué cubre |
|---|---|---|---|
| A. Sin verificación (situación actual) | Se confía en lo que escribe el usuario; el administrador corrige a mano | Ninguno | Nada preventivo |
| B. Correo institucional | Al registrarse se envía un correo al correo de la UMT y la cuenta se activa al confirmarlo (Supabase Auth ya incluye la confirmación por correo) | Bajo | Que la persona controle un correo de la UMT |
| C. Aprobación por el administrador | La cuenta queda pendiente hasta que un administrador la revisa y aprueba | Medio | Revisión humana, pero añade trabajo al administrador y retrasa el acceso |
| D. Lista de matrículas válidas | El equipo carga las matrículas permitidas y solo esas pueden registrarse | Medio a alto (requiere obtener y mantener la lista) | Que la matrícula exista |

Las opciones B y D se pueden combinar.

## Recomendación que se hizo

Si el equipo decidía verificar, la opción **B (correo institucional)** era la de mejor relación esfuerzo-beneficio y encajaba con el principio de facilitar el desarrollo sin servidor propio. Si no alcanzaba el tiempo antes de la entrega, se mantenía la **A** y el riesgo seguía aceptado en el ADR-003.

## Decisión

**Rechazada** (2026-11-23). No se agrega verificación de identidad en esta versión.

**Motivo:** verificar alargaría el proyecto y añadiría complejidad que el equipo no puede asumir en el tiempo disponible. Con una semana de desarrollo, el principio rector es facilitar el desarrollo y evitar la complejidad.

Se mantiene la **opción A**. Si más adelante se quiere reabrir el tema, las opciones de la tabla siguen siendo el punto de partida (la B, correo institucional, es la más barata) y habría que escribir un ADR nuevo que reemplace a este.

## Consecuencias

- No cambia nada en el registro ni en los permisos: el flujo del alcance y de [usuarios](../../01-producto/04-usuarios.md) sigue igual.
- El riesgo sigue aceptado y descrito en el ADR-003: alguien podría usar la matrícula de otra persona o registrarse sin ser de la UMT. La moderación sigue siendo manual, a cargo del administrador.
- Si se reabre el tema, habría que añadir un paso al registro y probar la política RLS para que una cuenta sin verificar no pueda crear negocios ni comentar.
