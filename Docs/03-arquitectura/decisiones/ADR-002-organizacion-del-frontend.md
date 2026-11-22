# ADR-002 · Organización interna del frontend: por capas

- **Estado:** Propuesta (pendiente de revisión del equipo)
- **Fecha:** 2026-11-22
- **Relacionada con:** [ADR-001](ADR-001-estilo-de-arquitectura.md)

## Contexto

El frontend es una SPA con React (ADR-001). Tiene 7 vistas y un modelo de datos pequeño (el detalle lo define el análisis de base de datos). El equipo es estudiantil y el principio rector es facilitar el desarrollo y evitar la complejidad.

Hay que decidir cómo ordenar el código dentro de `src/`.

## Opciones consideradas

| Opción | Descripción | Resultado |
|---|---|---|
| **A. Por capas (por tipo de archivo)** | `pages/`, `components/`, `services/`, `context/`, `routes/`, `lib/` | **Elegida** |
| B. Por funcionalidad | `features/catalogo/`, `features/vendedor/`, `features/auth/`, cada una con todo lo suyo | Descartada: excesiva para 7 vistas |
| C. Mixta | Capas para lo compartido y carpetas por funcionalidad para lo propio | Descartada: añade reglas sin necesidad |

## Decisión

El código se organiza **por capas**, es decir, por el tipo de cosa que hace cada archivo:

```
src/
├── pages/        una por vista (Inicio, Detalle, Login, ...)
├── components/   piezas reutilizables (Navbar, TarjetaPublicacion, Filtro...)
├── context/      AuthContext: usuario y rol actuales
├── services/     funciones que hablan con Supabase
├── routes/       definición de rutas y RutaProtegida
├── lib/          configuración del cliente de Supabase
├── styles/       CSS por componente
└── main.jsx
```

### Reglas

1. **Las páginas no llaman a Supabase directamente.** Usan `services/`, así el acceso a datos queda en un solo lugar.
2. **La sesión y el rol viven en `AuthContext`.** Cualquier componente los consulta desde ahí.
3. **El control de acceso por rol está en `RutaProtegida`.** Se aplica además de RLS en la base de datos, no en lugar de ella.

## Por qué

- Es fácil ubicar dónde va cada componente: la carpeta depende del tipo de archivo.
- Es fácil encontrar un error: si falla un dato, se revisa `services/`; si falla el aspecto, `components/` y `styles/`.
- Es fácil agregar un elemento: una vista nueva va en `pages/` y una función de datos nueva va en `services/`.

## Consecuencias

**A favor**

- Estructura sencilla de aprender y de explicar.
- El acceso a datos centralizado en `services/` facilita decidir después qué se guarda en caché si se agrega la PWA.

**En contra y cómo se atiende**

| Desventaja | Cómo se atiende |
|---|---|
| Si el proyecto crece mucho, las carpetas se llenan de archivos | Subdividir dentro de cada carpeta (por ejemplo `components/catalogo/`) o migrar a funcionalidades después |
| Para tocar una funcionalidad hay que abrir varias carpetas | Aceptable con el tamaño actual del proyecto |
