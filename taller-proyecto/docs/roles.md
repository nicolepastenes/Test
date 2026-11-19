# Roles y permisos - Taller mecanico

Permiso = <app>.<accion>_<modelo>. Apps del sistema: clientes, taller,
ordenes, facturacion y pruebas.

## Roles (grupos)
- Recepcionista: clientes.view/add/change_cliente,
  clientes.view/add/change_vehiculo, ordenes.view_ordentrabajo,
  ordenes.add_ordentrabajo, ordenes.notificar_admin,
  ordenes.view_notificacion, taller.view/add/change_proveedor,
  taller.view_repuesto.
  Menu: Inicio, Clientes, Ordenes, Proveedores, Repuestos.
- Mecanico: ordenes.view_ordentrabajo, ordenes.change_ordentrabajo,
  ordenes.avanzar_orden, ordenes.view/add/change_detallerepuesto,
  ordenes.view/add/change_manoobra, ordenes.view_notificacion,
  taller.view_repuesto.
  Menu: Inicio, Ordenes (solo las suyas), Repuestos.
- Cliente: ordenes.view_ordentrabajo, ordenes.aprobar_orden,
  ordenes.view_notificacion, clientes.view_vehiculo.
  Menu: Inicio, Ordenes (solo las de sus vehiculos).
- Administrador: TODOS los permisos de clientes, taller, ordenes,
  facturacion y pruebas, y marcado como "Es staff".
- Tester (el estudiante que ejecuta pruebas):
  pruebas.view_requisito, pruebas.view/add/change/delete_casoprueba y
  pruebas.view/add_ejecucionprueba.

## Permiso por accion de una orden
- Asignar o reasignar mecanico: ordenes.asignar_mecanico
- Diagnostico, repuestos, mano de obra y enviar al cliente:
  ordenes.change_ordentrabajo
- Aprobar o rechazar: ordenes.aprobar_orden
- Cambiar estado y usar repuesto: ordenes.avanzar_orden
- Notificar al administrador: ordenes.notificar_admin
- Borrar orden: ordenes.delete_ordentrabajo
- Descuento en boleta: facturacion.aplicar_descuento

## Usuarios de demostracion (clave "clave1234")
- admin1: Administrador
- recepcion1: Recepcionista
- mecanico1 y mecanico2: Mecanico, cada uno con su perfil Mecanico
- cliente1: Cliente, con su perfil Cliente
- tester1: Tester

## Datos de demostracion
- Un proveedor y tres repuestos de ejemplo (uno con stock bajo el
  minimo).
- No se crean vehiculos ni ordenes: se registran durante las pruebas.
