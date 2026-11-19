# Proyecto Django con Docker

## Entorno
- Stack: Django 5 + PostgreSQL, todo dentro de Docker
  (docker-compose.yml). Servicios: web y db.
- TODO comando de Django se ejecuta dentro del contenedor:
  docker compose exec web python manage.py <comando>
  Si web no esta corriendo:
  docker compose run --rm web python manage.py <comando>
- Nunca ejecutes python, pip ni manage.py directo en el computador.
- Si cambia requirements.txt hay que reconstruir:
  docker compose up --build -d
- Nunca ejecutes docker compose down -v sin permiso expreso del
  estudiante: borra la base de datos.
- Antes de ejecutar un comando de Docker, di cual es y para que.

## Paquete del proyecto
- En estas reglas <proyecto> es el nombre de la carpeta de
  configuracion de Django: la que contiene settings/ y urls.py (por
  ejemplo taller_proyecto). Se ve en DJANGO_SETTINGS_MODULE de
  docker-compose.yml (<proyecto>.settings.local). Usa siempre el
  nombre real, nunca escribas "<proyecto>" literal.

## Estructura
- Settings en <proyecto>/settings/ (base.py, local.py, production.py,
  maintenance.py). Las apps van en apps/<nombre>.
- Las apps salen de docs/modelo_datos.md: cada "## App <nombre>" es
  una carpeta apps/<nombre>. Ademas existen siempre apps/inicio,
  apps/cuentas y apps/pruebas.

## Fuentes de verdad
- Del caso: docs/modelo_datos.md, docs/requerimientos.csv,
  docs/identidad.md, docs/roles.md y docs/reglas_flujo.md.
- Generales: docs/reglas_base_datos.md, reglas_formularios.md,
  reglas_vistas_templates.md, reglas_frontend.md, reglas_usuarios.md
  y reglas_pruebas_usuario.md.
- El codigo se ajusta a los docs, nunca al reves. No cambies un doc
  si no te lo piden. No inventes modelos, campos ni requerimientos.

## Forma de trabajar
- Despues de cualquier cambio, muestra el diff y espera confirmacion.
- Si ya existe algo que coincide con los docs, no lo toques.
- Hay un agente por tema (@modelos, @frontend, @formularios, @flujo,
  @vistas, @usuarios, @pruebas) y un agente @maestro que los
  coordina. Cada uno toca solo sus archivos.
- Nunca escribas casos de prueba de usuario: los escriben los
  estudiantes.
