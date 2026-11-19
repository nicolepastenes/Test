# Reglas de vistas, urls y home

## Vistas
- Usa SOLO vistas basadas en clases genericas (django.views.generic):
  ListView, DetailView, CreateView, UpdateView, DeleteView,
  TemplateView y FormView. NUNCA escribas vistas como funciones.
- Nombre: <Modelo><Accion>View (ejemplo: ClienteCreateView).
- CreateView y UpdateView: form_class = <Modelo>Form (no uses
  fields), SuccessMessageMixin y success_message.
- CreateView, UpdateView, DeleteView y FormView definen success_url
  (reverse_lazy) o get_success_url.
- Permisos: toda vista lleva PermissionRequiredMixin como primer padre
  con el permiso de docs/roles.md (ListView y DetailView:
  view_<modelo>; Create: add_; Update: change_; Delete: delete_;
  acciones: el permiso que indique docs/reglas_flujo.md).
- Acciones del flujo (las de la seccion "Acciones y URLs" de
  docs/reglas_flujo.md): son FormView. Buscan el objeto por pk con
  get_object_or_404, llaman a la funcion de servicios.py desde
  form_valid y no repiten la logica. Si el servicio lanza
  ValidationError, el error se muestra en el formulario
  (form.add_error(None, mensaje)).
- Los listados que docs/reglas_flujo.md filtra por rol ("Quien ve
  que") se filtran en get_queryset.
- Los reportes son TemplateView que arman su contexto en
  get_context_data con consultas del ORM.
- Las vistas van en apps/<app>/views.py. No toques models.py,
  forms.py, servicios.py ni base.html.

## Home
- App apps/inicio: HomeView(TemplateView), template
  inicio/home.html. Muestra una tarjeta por cada modulo al que el
  usuario tiene acceso (con perms, igual que el menu).

## URLs
- Rutas cortas y explicitas: /<recurso_plural>/, /<id>/ y /<accion>/.
  Ejemplo para un recurso "cliente":
  /clientes/  /clientes/nuevo/  /clientes/3/  /clientes/3/editar/
  /clientes/3/borrar/
  Se usa "nuevo" o "nueva" segun el genero del recurso. Las URLs de
  las acciones del flujo estan en docs/reglas_flujo.md.
- Cada app tiene apps/<app>/urls.py con app_name y path() con
  convertidores (<int:pk>). Nombres: <modelo>_list, _detail, _create,
  _update, _delete; las acciones, el nombre que indique
  docs/reglas_flujo.md.
- <proyecto>/urls.py usa re_path() para TODO:
  re_path(r"^admin/", admin.site.urls)
  re_path(r"^$", HomeView.as_view(), name="inicio")
  re_path(r"^", include("apps.<app>.urls"))
  El include lleva r"^" porque las rutas de cada app ya empiezan con
  el nombre del recurso.
- Nunca escribas rutas a mano en templates: {% url 'app:nombre' %}.

## Templates
- Nombres por defecto de Django en apps/<app>/templates/<app>/:
  <modelo>_list.html, <modelo>_detail.html, <modelo>_form.html,
  <modelo>_confirm_delete.html. Las acciones usan accion_form.html
  (template_name explicito).
- Extienden base.html y escriben solo {% block content %}.
- Formularios: <form method="post">, {% csrf_token %} y
  {% include "parciales/form_campos.html" %}.
- Botones y enlaces de accion solo con perms (ver
  docs/reglas_frontend.md).
- Listas: object_list dentro de <div class="table-responsive">.
- El formato de numeros y precios es el de docs/identidad.md.
