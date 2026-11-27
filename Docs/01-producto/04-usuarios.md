# 04 · Usuarios

Este documento describe los tipos de usuario del sistema, qué puede hacer cada uno y las reglas que se aplican a sus permisos. Tomó como base el [prototipo](../05-vista/boceto-refinado/boceto-refinado.html), que se está actualizando; si hay diferencias, **este documento prevalece**.

## 1. Tipos de usuario

El sistema maneja **4 tipos de usuario**. Cada uno tiene permisos y accesos distintos.

| Tipo de usuario | Nombre en el glosario | ¿Tiene cuenta? |
|---|---|---|
| Usuario no registrado | Visitante | No |
| Usuario registrado | Usuario registrado (Cliente) | Sí |
| Usuario registrado como negocio | Usuario Vendedor | Sí |
| Usuario registrado como administrador | Usuario Administrador | Sí |

## 2. Jerarquía y reglas generales

- Los permisos son **acumulativos** en este orden: Visitante → Usuario registrado → Vendedor.
- El **Administrador** puede hacer todo lo anterior y además controla el sistema.
- El registro **no pide elegir un tipo de cuenta**: todos se registran como Usuario registrado. Se pasa a Vendedor al crear un negocio (ver 5).
- La cuenta de **Administrador no se crea desde el registro**: el equipo da de alta la del **administrador maestro** directamente en la base de datos desde el inicio del proyecto. Después, **un administrador puede dar el rol a otras cuentas ya creadas**, que serán **administradores hijos** (ver 6).
- **El tipo de cuenta lo determina el sistema, no el usuario.** Todos inician sesión con su **matrícula y contraseña**; el sistema busca esas credenciales en la base de datos y, según la cuenta encontrada, decide si es Usuario, Vendedor o Administrador y qué pantallas le muestra.
- La **matrícula** es el identificador de inicio de sesión y no se repite entre cuentas.
- Los permisos se validan en la interfaz y también en la base de datos (ver [ADR-003](../03-arquitectura/decisiones/ADR-003-seguridad-y-permisos.md)).

## 3. Usuario no registrado (Visitante)

Entra al sistema como invitado, desde el botón **Invitado** de la pantalla de inicio de sesión.

**Puede**
- Ver el catálogo y el detalle de las publicaciones y los negocios.
- Ver el contacto del vendedor en el detalle de la publicación.

**No puede**
- Calificar ni comentar. Al intentarlo, el sistema le invita a registrarse.
- Entrar a un perfil de usuario.
- Crear negocios.

## 4. Usuario registrado

Se registra con sus datos personales (nombre, apellidos, matrícula, edad, carrera, semestre y contraseña) e inicia sesión con su matrícula.

**Puede**
- Todo lo que hace el Visitante.
- Ver su perfil y editar su información y su foto.
- Calificar publicaciones; puede cambiar su calificación.
- Crear, editar y eliminar **sus propios** comentarios.
- Crear un negocio desde su perfil (**Añadir un negocio**) y con ello pasar a Vendedor.

## 5. Usuario registrado como negocio (Vendedor)

Es un Usuario registrado que ya creó al menos un negocio. No hay un cambio de cuenta aparte: la sección **Negocios** de su perfil lista sus negocios y permite añadir más.

**Cómo funciona**
- Al crear un negocio se indican nombre, teléfono, categoría y una **contraseña propia del negocio**.
- Un usuario puede tener **varios negocios**.
- Para trabajar en un negocio se **ingresa** con su contraseña. Al **salir del negocio** se vuelve a la cuenta de usuario.
- Eliminar un negocio pide la contraseña del negocio y borra también sus publicaciones. No se puede deshacer.

**Puede**
- Todo lo que hace el Usuario registrado.
- Modificar los datos de sus negocios (nombre, teléfono, descripción y foto).
- Crear, editar y eliminar **las publicaciones de sus negocios**, con su contacto de vendedor.
- Consultar los **reportes de sus negocios** por mes (pestaña «Reportes» del panel del negocio): vista general y por publicación, con vistas, clics en WhatsApp, calificación promedio y comentarios nuevos. Solo ve los de sus propios negocios.

**No puede**
- Modificar ni eliminar publicaciones, negocios o comentarios de otros usuarios.

## 6. Usuario registrado como administrador

Es el responsable de mantener el control del sistema. Inicia sesión con su matrícula en la misma pantalla que todos y ve el **catálogo normal**, igual que cualquier usuario; no tiene un catálogo propio. Junto a «Mi perfil» aparece el botón **«Ver panel de administrador»**, que abre su **panel de administración** en pantalla completa, con siete secciones: Publicaciones, Negocios, Usuarios, Categorías, Administradores, Bitácora y Reportes (pantallas 29 a 44 del [prototipo](../05-vista/boceto-refinado/boceto-refinado.html)). Con **«Salir del panel»** regresa al catálogo. Las publicaciones y los negocios se dan de baja desde su detalle o perfil (con una «zona de baja»), no desde la lista, para evitar borrados por descuido; los comentarios, desde el detalle de la publicación.

**Puede**
- Todo lo que hacen los demás usuarios.
- Consultar los **reportes** de todo el sistema (vistas, clics en WhatsApp, calificación y comentarios por publicación), filtrados por negocio y por mes.
- Ver **todas** las publicaciones y negocios, y buscar en cada lista del panel (por nombre, matrícula o texto; con filtros por categoría y tipo en publicaciones y negocios).
- Dar de baja (eliminar) publicaciones, comentarios, usuarios, negocios y administradores hijos.
- Agregar, editar y eliminar categorías de negocio. Al editar, el nombre cambia también en los negocios que la usan; al eliminar, esos negocios pasan a «Otro». «Otro» no se puede editar ni eliminar.
- Agregar más cuentas de administrador, otorgando el rol a un usuario ya registrado.

**Administrador maestro y administradores hijos**
- Hay **un solo administrador maestro**, dado de alta en la base de datos por el equipo. Nunca se elimina ni se le quita el rol, y con él el sistema nunca se queda sin administrador.
- Los **administradores hijos** son las cuentas a las que un administrador les otorga el rol. Cualquier administrador puede dar de baja a un hijo; nadie puede dar de baja al maestro.

**Reglas**
- Solo un administrador puede otorgar el rol de administrador. Ningún otro usuario puede hacerlo ni cambiar su propio rol.
- **Dar de baja es permanente y no se recupera.** Elimina la cuenta, negocio, publicación o comentario, junto con lo que depende de ello.
- **Cada baja queda registrada** en una bitácora (qué se eliminó, qué administrador lo hizo y cuándo), para saber qué cuentas fueron eliminadas. La bitácora no se edita ni se borra.
- Como la baja no tiene vuelta atrás, el sistema pide confirmación antes de eliminar.
- Cada administrador usa su propia cuenta; no se comparten. Solo unas pocas personas del equipo tienen este rol, porque una cuenta de administrador comprometida da control total del sistema.

## 7. Resumen de permisos

| Acción | Visitante | Usuario | Vendedor | Admin |
|---|:-:|:-:|:-:|:-:|
| Ver catálogo, publicaciones y contacto del vendedor | ✔ | ✔ | ✔ | ✔ |
| Entrar a su perfil y editar sus datos | ✘ | ✔ | ✔ | ✔ |
| Calificar y comentar | ✘ | ✔ | ✔ | ✔ |
| Crear negocios | ✘ | ✔ (pasa a Vendedor) | ✔ | ✔ |
| Crear, editar y eliminar publicaciones de su negocio | ✘ | ✘ | ✔ | ✔ |
| Eliminar contenido, negocios o usuarios de otros | ✘ | ✘ | ✘ | ✔ |
| Dar de baja a un administrador hijo | ✘ | ✘ | ✘ | ✔ |
| Dar de baja al administrador maestro | ✘ | ✘ | ✘ | ✘ |
| Ver reportes de sus negocios | ✘ | ✘ | ✔ | ✔ (de todos) |
| Agregar, editar y eliminar categorías de negocio | ✘ | ✘ | ✘ | ✔ |
| Agregar cuentas de administrador | ✘ | ✘ | ✘ | ✔ |
