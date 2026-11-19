# Modelo de datos - Taller mecanico

Diseno de referencia del docente. Si el equipo disena su propio
modelo, reemplaza este archivo antes de usar el agente @modelos.

## App clientes

### Cliente
- nombre: CharField(150)
- rut: CharField(12), unico
- telefono: CharField(20)
- correo: EmailField, opcional
- usuario: OneToOneField(User, related_name="cliente", null, blank,
  on_delete=SET_NULL). Es la cuenta con la que el cliente entra.

### Vehiculo
- cliente: ForeignKey(Cliente, related_name="vehiculos",
  on_delete=CASCADE)
- patente: CharField(10), unico
- marca: CharField(50)
- modelo: CharField(50)
- anio: PositiveIntegerField

## App taller

### Proveedor
- nombre: CharField(150)
- telefono: CharField(20)
- correo: EmailField, opcional

### Repuesto
- nombre: CharField(150)
- stock: PositiveIntegerField
- stock_minimo: PositiveIntegerField
- precio: DecimalField(10, 2)
- proveedor: ForeignKey(Proveedor, related_name="repuestos",
  on_delete=PROTECT)

### Mecanico
- usuario: OneToOneField(User, related_name="mecanico",
  on_delete=CASCADE)
- especialidad: CharField(80), opcional
- Propiedad libre (no es un campo): True si el mecanico no tiene
  ordenes en estado en_diagnostico ni en_reparacion.

## App ordenes

### OrdenTrabajo
- vehiculo: ForeignKey(clientes.Vehiculo, related_name="ordenes",
  on_delete=PROTECT)
- problema: TextField
- estado: CharField(25) con opciones: recibido, en_diagnostico,
  esperando_aprobacion, aprobada, rechazada, en_reparacion,
  listo_retiro. Por defecto: recibido.
- fecha_recepcion: DateField, por defecto la fecha de hoy
- mecanico: ForeignKey(taller.Mecanico, related_name="ordenes",
  null, blank, on_delete=SET_NULL)
- diagnostico: TextField, opcional
- Permisos propios (Meta.permissions): asignar_mecanico,
  aprobar_orden, avanzar_orden, notificar_admin

### DetalleRepuesto
- orden: ForeignKey(OrdenTrabajo, related_name="repuestos",
  on_delete=CASCADE)
- repuesto: ForeignKey(taller.Repuesto, related_name="usos",
  on_delete=PROTECT)
- cantidad: PositiveIntegerField
- usado: BooleanField, por defecto False (False = necesario,
  True = ya usado y descontado del stock)

### ManoObra
- orden: ForeignKey(OrdenTrabajo, related_name="mano_obra",
  on_delete=CASCADE)
- descripcion: CharField(200)
- monto: DecimalField(10, 2)

### Notificacion
- destinatario: ForeignKey(User, related_name="notificaciones",
  on_delete=CASCADE)
- mensaje: CharField(250)
- orden: ForeignKey(OrdenTrabajo, related_name="notificaciones",
  null, blank, on_delete=CASCADE)
- leida: BooleanField, por defecto False
- fecha: DateTimeField, automatica al crear

## App facturacion

### Boleta
- orden: OneToOneField(ordenes.OrdenTrabajo, related_name="boleta",
  on_delete=CASCADE)
- tipo: CharField(10) con opciones: boleta, factura. Por defecto:
  boleta.
- fecha: DateField, por defecto la fecha de hoy
- subtotal: DecimalField(10, 2)
- descuento_porcentaje: PositiveIntegerField, por defecto 0
  (entre 0 y 100)
- total: DecimalField(10, 2)
- Permiso propio (Meta.permissions): aplicar_descuento

## App pruebas

### Requisito
- codigo: CharField(10), unico (ejemplo: RF-01)
- descripcion: TextField
- tipo: CharField(20) con opciones: Funcional, No funcional
- modulo: CharField(30)

### CasoPrueba
- codigo: CharField(10), unico (ejemplo: CP-01)
- titulo: CharField(150)
- rol: CharField(20) con opciones: recepcionista, mecanico,
  administrador, cliente
- precondiciones: TextField, opcional
- pasos: TextField (un paso por linea)
- resultado_esperado: TextField
- requisitos: ManyToManyField(Requisito, related_name="casos")
- creado_por: ForeignKey(User, null, on_delete=SET_NULL)

### EjecucionPrueba
- caso: ForeignKey(CasoPrueba, related_name="ejecuciones",
  on_delete=CASCADE)
- fecha: DateTimeField, automatica al crear
- ejecutado_por: ForeignKey(User, null, on_delete=SET_NULL)
- resultado: CharField(10) con opciones: paso, fallo, bloqueado
- observaciones: TextField, opcional
