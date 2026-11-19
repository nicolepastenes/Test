# Identidad del sistema - Taller mecanico

## Nombre y pie de pagina
- Nombre del sistema: Taller Mecanico
- Pie de pagina, texto de la izquierda: Taller Mecanico

## Colores
- --color-primario: #1F3A5F   (azul acero: menu, botones principales)
- --color-secundario: #3A3F47 (gris carbon: pie de pagina)
- --color-acento: #F28C28     (naranja: detalles y alertas)
- --color-fondo: #F4F5F7      (fondo de la pagina)
- --color-texto: #222222      (texto)

## Logo
- static/img/logo.svg (un engranaje). Lo reemplaza cada equipo.

## Opciones del menu (izquierda)
Inicio | Clientes | Ordenes | Mecanicos | Repuestos | Proveedores |
Boletas | Reportes (Facturacion, Repuestos) | Pruebas (Requisitos,
Casos, Trazabilidad) | Administracion
- Cada una se muestra con el permiso de ver su modelo (ver roles.md).
  Reportes - Facturacion usa facturacion.view_boleta y Reportes -
  Repuestos usa taller.view_repuesto.
- Notificaciones va a la derecha, con insignia (RF-05, RF-13, RF-20).

## Pantalla del mecanico (RNF-01, RNF-05)
- La lista de ordenes del mecanico muestra tarjetas con botones
  grandes (btn btn-lg w-100).
- El cambio de estado se hace con una barra fija al pie
  (templates/parciales/barra_estado.html, clase fixed-bottom) que
  aparece en el detalle de la orden solo si el usuario tiene
  ordenes.avanzar_orden. Siempre esta en el mismo lugar, tiene una
  seleccion simple (opciones con radio) con los estados permitidos y
  un boton grande "Cambiar estado".

## Formato de numeros
- Los montos se muestran con simbolo y separador de miles:
  ${{ valor|floatformat:"0g" }}
