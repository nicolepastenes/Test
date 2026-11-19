# Reglas de flujo (logica del taller)

## Donde vive la logica
- En apps/ordenes/servicios.py, como funciones normales (no son
  vistas). Las vistas las llaman desde form_valid y no repiten la
  logica.
- Cada funcion que toca varias tablas usa transaction.atomic().
- Si una regla no se cumple, la funcion lanza ValidationError con un
  mensaje en espanol; la vista lo muestra en el formulario.

## Estados de la orden
recibido -> en_diagnostico -> esperando_aprobacion -> aprobada
         -> en_reparacion -> listo_retiro
esperando_aprobacion -> rechazada
- Las transiciones permitidas estan en un diccionario TRANSICIONES de
  servicios.py. Cualquier otra transicion es un error.

## Funciones y requerimientos
- crear_orden(vehiculo, problema): estado recibido, fecha de hoy.
  (RF-03)
- asignar_mecanico(orden, mecanico): exige mecanico.libre. Si la orden
  esta recibida pasa a en_diagnostico; si esta aprobada solo cambia
  de mecanico. Notifica al mecanico. (RF-04, RF-05, RF-16)
- enviar_a_cliente(orden): exige estado en_diagnostico y diagnostico
  no vacio. Pasa a esperando_aprobacion y notifica al usuario del
  cliente, si lo tiene. (RF-09, RF-10)
- decidir_orden(orden, aprobar): exige esperando_aprobacion. Pasa a
  aprobada o rechazada y notifica a todos los usuarios del grupo
  Recepcionista. (RF-12, RF-13)
- notificar_administrador(orden): exige estado aprobada. Notifica a
  los usuarios del grupo Administrador. (RF-14)
- avanzar_estado(orden, nuevo_estado, usuario): solo el mecanico
  asignado; solo aprobada -> en_reparacion y en_reparacion ->
  listo_retiro. Al llegar a listo_retiro llama a generar_boleta.
  (RF-17, RF-21)
- usar_repuesto(detalle): exige orden en_reparacion y stock suficiente.
  Descuenta el stock, marca usado=True y, si el stock queda menor o
  igual a stock_minimo, notifica a los administradores. (RF-18,
  RF-19, RF-20)
- generar_boleta(orden): subtotal = suma de cantidad x precio de los
  repuestos usados + suma de la mano de obra; descuento 0; tipo
  boleta. (RF-21)
- aplicar_descuento(boleta, porcentaje): entre 0 y 100;
  total = subtotal x (100 - porcentaje) / 100, con 2 decimales.
  (RF-23)
- notificar(usuarios, mensaje, orden=None): crea una Notificacion por
  usuario. Todas las demas funciones la usan.

## Quien ve que orden (filtro del queryset en las vistas)
- Administrador y Recepcionista: todas.
- Mecanico: solo las asignadas a el.
- Cliente: solo las de sus vehiculos.

## Notificaciones
- Son internas del sistema (no hay correo). Se listan en
  /notificaciones/; al abrir la lista las no leidas se muestran
  destacadas y luego quedan leidas.
- apps/ordenes/context_processors.py expone notificaciones_sin_leer
  (cantidad) a todos los templates. Se registra en
  TEMPLATES -> context_processors de settings.

## Decisiones del docente sobre requerimientos ambiguos
- RF-09: el envio al cliente es un boton explicito del mecanico
  ("Enviar al cliente"), no es automatico al guardar.
- RF-14: es un boton de la recepcionista ("Notificar al
  administrador") sobre ordenes aprobadas.
- RF-19: el stock se descuenta al registrar el USO (RF-18), no al
  registrar el repuesto como necesario (RF-07).
- RF-20: aunque la lista lo marca como no funcional, se implementa
  como alerta interna.
- RNF-03 (15 a 20 ordenes, esperas de 2 s) no se implementa ni se
  prueba con casos de usuario.

## Acciones y URLs
Todas son FormView sobre un objeto (<id>), con template
accion_form.html. Las funciones del taller viven en
apps/ordenes/servicios.py. Formato: URL, nombre, formulario, permiso,
funcion.
- /ordenes/<id>/asignar/  orden_asignar
  AsignarMecanicoForm (mecanico: Select solo con mecanicos libres)
  ordenes.asignar_mecanico  asignar_mecanico
- /ordenes/<id>/diagnostico/  orden_diagnostico
  DiagnosticoForm (ModelForm de OrdenTrabajo, solo diagnostico)
  ordenes.change_ordentrabajo  solo si esta en_diagnostico (RF-06)
- /ordenes/<id>/repuestos/  orden_repuesto_add
  DetalleRepuestoForm (repuesto y cantidad)
  ordenes.change_ordentrabajo  solo si esta en_diagnostico (RF-07)
- /ordenes/<id>/mano-obra/  orden_mano_obra_add
  ManoObraForm (descripcion y monto)
  ordenes.change_ordentrabajo  solo si esta en_diagnostico (RF-08)
- /ordenes/<id>/enviar/  orden_enviar
  EnviarOrdenForm (sin campos, solo confirma)
  ordenes.change_ordentrabajo  enviar_a_cliente
- /ordenes/<id>/decision/  orden_decision
  DecisionForm (decision: RadioSelect, aprobar o rechazar)
  ordenes.aprobar_orden  decidir_orden
- /ordenes/<id>/estado/  orden_estado
  EstadoForm (estado: RadioSelect con los estados permitidos; recibe
  la orden en __init__)  ordenes.avanzar_orden  avanzar_estado
- /ordenes/<id>/usar-repuesto/  orden_usar_repuesto
  UsarRepuestoForm (detalle: Select solo con repuestos necesarios aun
  no usados)  ordenes.avanzar_orden  usar_repuesto
- /ordenes/<id>/notificar-admin/  orden_notificar_admin
  NotificarAdminForm (sin campos)  ordenes.notificar_admin
  notificar_administrador
- /boletas/<id>/descuento/  boleta_descuento
  DescuentoForm (porcentaje, entre 0 y 100)
  facturacion.aplicar_descuento  aplicar_descuento

## Listados y reportes
- /clientes/ y /vehiculos/ (RF-01, RF-02, con enlace desde el
  detalle del cliente).
- /ordenes/ (filtrada por rol, ver "Quien ve que orden") con detalle,
  y /ordenes/<id>/borrar/ como DeleteView (RF-24, RF-30).
- /mecanicos/ y su detalle con las ordenes de cada uno (RF-15,
  RF-25). /repuestos/ y su detalle (RF-26). /proveedores/ (RF-29).
- /boletas/ (RF-22).
- /reportes/facturacion/: boletas del mes y total facturado por mes
  (RF-27). Permiso facturacion.view_boleta.
- /reportes/repuestos/: repuestos mas usados y existencia actual
  (RF-28). Permiso taller.view_repuesto.
- /notificaciones/ (permiso ordenes.view_notificacion).
