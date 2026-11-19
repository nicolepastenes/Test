# Reglas de la parte grafica (frontend)

## De donde sale cada cosa
- Nombre del sistema, colores, logo, pie de pagina, opciones del menu
  y pantallas especiales: docs/identidad.md.
- Quien ve cada opcion y cada boton: permisos de docs/roles.md.

## Estructura de la pagina
- Toda pagina tiene 3 zonas: encabezado (menu), cuerpo y pie.
- El encabezado y el pie viven en templates/parciales/navbar.html y
  templates/parciales/footer.html.
- templates/base.html los incluye con {% include %} y solo define
  {% block title %} y {% block content %}.
- Cada pagina SOLO escribe {% block content %}. Nunca copies el menu
  ni el pie dentro de una pagina.

## Colores
- Variables CSS en static/css/estilos.css: --color-primario,
  --color-secundario, --color-acento, --color-fondo y --color-texto,
  con los valores de docs/identidad.md.
- Usa siempre var(--color-...); no escribas colores sueltos. Bootstrap
  5 va por CDN; el CSS propio solo ajusta colores.

## Logo
- static/img/logo.svg ya existe. Va a la izquierda del menu, dentro del
  navbar-brand, antes del nombre del sistema. Alto 40px, alt="Logo".
- Favicon: static/img/favicon.ico, enlazado en el head de base.html.

## Menu del encabezado (navbar.html)
- Izquierda: Inicio y las opciones de docs/identidad.md. Cada opcion
  se muestra solo si el usuario tiene el permiso de ver ese modelo:
  {% if perms.<app>.view_<modelo> %}. Las opciones con permiso propio
  usan ese permiso. "Administracion" (admin:index) solo con
  user.is_staff.
- Derecha: si existe la variable notificaciones_sin_leer, un enlace a
  Notificaciones con una insignia con la cantidad (solo si es mayor
  que 0). Despues, el area de sesion: sin sesion "Iniciar sesion"; con
  sesion el nombre de usuario y "Salir" (formulario POST con
  csrf_token).
- Los botones de accion dentro de las paginas (Nuevo, Editar, Borrar y
  las acciones del flujo) usan el permiso que corresponde, con perms,
  igual que el menu.

## Pantallas especiales
- Las que describe docs/identidad.md (por ejemplo una barra fija al
  pie) se hacen como partial en templates/parciales/ y se incluyen en
  la pagina que corresponda. El cuerpo deja espacio abajo
  (padding-bottom) para que una barra fija no tape el contenido.

## Adaptable a cualquier pantalla
- base.html lleva <meta name="viewport"> y usa la grilla de Bootstrap
  (container, row, col-md-*).
- Toda tabla va dentro de <div class="table-responsive">.
- El menu se colapsa en pantallas pequenas (navbar-expand-lg).

## Pie de pagina (footer.html)
- Texto de docs/identidad.md a la izquierda y "(c) {% now "Y" %}
  Equipo <N> - Desarrollo de SIA" a la derecha. Fondo
  var(--color-secundario), texto blanco.
- Siempre al final: body con d-flex flex-column min-vh-100 y main con
  flex-grow-1.

## Mensajes y errores
- base.html muestra los mensajes de django.contrib.messages como
  alertas de Bootstrap antes del bloque content.
- templates/403.html extiende base.html con el texto "No tienes
  permiso para ver esta pagina".

## Static y settings (<proyecto>/settings/base.py)
- Carpeta static/ en la raiz: static/css/estilos.css,
  static/img/logo.svg, static/img/favicon.ico.
- STATIC_URL = "/static/"
- STATICFILES_DIRS = [BASE_DIR / "static"]
- STATIC_ROOT = BASE_DIR / "staticfiles" (solo produccion; en
  .gitignore)
- TEMPLATES, "DIRS": [BASE_DIR / "templates"]
- MESSAGE_TAGS = {messages.ERROR: "danger"}, con
  from django.contrib.messages import constants as messages
- LANGUAGE_CODE = "es" y TIME_ZONE = "America/Santiago"
- Todo template que use estaticos empieza con {% load static %}.
