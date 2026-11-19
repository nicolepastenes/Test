# Reglas de usuarios y acceso

## Usuarios
- Usa django.contrib.auth con el modelo User por defecto. No crees un
  modelo de usuario propio. Nunca guardes contrasenas en texto plano.
- Sin sesion iniciada solo se ve el login. Toda otra pagina exige
  usuario y contrasena.

## App cuentas (apps/cuentas)
- Vistas y urls con clases: LoginView (authentication_form=LoginForm,
  template cuentas/login.html) y LogoutView. Rutas /login/ y
  /logout/. app_name = "cuentas".
- En Django 5, LogoutView solo acepta POST: "Salir" es un formulario
  con csrf_token, nunca un enlace.
- No hay registro publico: las cuentas las crea el Administrador desde
  el admin de Django. Para dar permisos de administrador a otro
  usuario se le asigna el grupo Administrador y se marca "Es staff".

## Settings (<proyecto>/settings/base.py)
- LOGIN_URL = "cuentas:login"
- LOGIN_REDIRECT_URL = "inicio"
- LOGOUT_REDIRECT_URL = "cuentas:login"

## Comando crear_roles
apps/cuentas/management/commands/crear_roles.py. Se puede repetir sin
duplicar (idempotente) e imprime lo que creo. Todo lo que crea sale de
docs/roles.md:
- Siempre: los grupos con sus permisos. El grupo Administrador recibe
  todos los permisos de las apps indicadas en roles.md.
- Con --demo: ademas los usuarios de prueba (con su perfil, si el rol
  lo tiene) y los datos de demostracion que indique roles.md.
- Si se borra el volumen de la base, se vuelve a ejecutar con
  docker compose exec web python manage.py crear_roles --demo
- Las claves de demostracion son solo para el curso.

## Permisos
- Formato: <app>.<accion>_<modelo> (view, add, change, delete) o un
  permiso propio declarado en Meta.permissions.
- Sin sesion, PermissionRequiredMixin redirige al login; con sesion
  pero sin permiso responde 403.
