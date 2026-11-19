# Reglas del modulo de pruebas de usuario (apps/pruebas)

## Proposito
El estudiante escribe casos de prueba de usuario, los asocia a los
requerimientos, los ejecuta a mano contra el sistema y ve cuales
pasan. El sistema NO trae casos de prueba: los escriben los
estudiantes. Nunca generes, cargues ni sugieras casos de ejemplo.

## Requisitos
- Origen: docs/requerimientos.csv con columnas codigo, descripcion,
  tipo y modulo.
- Comando: python manage.py cargar_requisitos. Crea o actualiza cada
  Requisito por su codigo, se puede repetir sin duplicar e imprime
  cuantos creo y cuantos actualizo.
- En el sistema los requisitos son solo de lectura: lista con filtro
  por tipo y modulo, y cuantos casos tiene cada uno.

## Caso de prueba
- Campos: codigo, titulo, rol, precondiciones, pasos (uno por linea),
  resultado_esperado y requisitos.
- Todo caso debe tener al menos un requisito asociado; si no, el
  formulario muestra un error.
- El formulario muestra los requisitos como casillas
  (CheckboxSelectMultiple), cada una con codigo y descripcion.
- El detalle muestra el caso, sus requisitos y el historial de
  ejecuciones.

## Ejecucion
- Se registra desde el detalle del caso, en /casos/<id>/ejecutar/.
- Campos: resultado (paso, fallo, bloqueado) y observaciones. Si el
  resultado es fallo, las observaciones son obligatorias (clean()).
- ejecutado_por es el usuario actual y la fecha es automatica.
- Las ejecuciones no se editan ni se borran: son el historial.
- Un caso se puede ejecutar varias veces; vale la ultima ejecucion.

## Estado de un requisito
Funcion estado_requisito(requisito) en apps/pruebas/servicios.py. Mira
el ultimo resultado de cada caso asociado:
- Sin casos: ningun caso lo referencia.
- Sin ejecutar: tiene casos, pero ninguno se ejecuto.
- Fallo: algun caso tiene como ultimo resultado fallo.
- Bloqueado: ninguno fallo y algun caso quedo bloqueado.
- Paso: todos sus casos tienen ultimo resultado paso.
- En curso: ninguno fallo ni esta bloqueado, algunos pasaron y otros
  no se han ejecutado.
Se evalua en ese orden.

## Trazabilidad
- /trazabilidad/ (TemplateView). Una fila por requisito: codigo,
  descripcion, tipo, casos asociados (enlaces) y estado (insignia de
  Bootstrap: Paso verde, Fallo rojo, Bloqueado amarillo, En curso
  azul, Sin ejecutar y Sin casos gris).
- Arriba, un resumen: total de requisitos, con casos (cantidad y %),
  que pasan (cantidad y %), con fallo y sin casos.
- Filtro por estado con ?estado=<estado> y por modulo con
  ?modulo=<modulo>.

## URLs (rutas cortas)
/requisitos/, /casos/, /casos/nuevo/, /casos/<id>/,
/casos/<id>/editar/, /casos/<id>/ejecutar/, /trazabilidad/
Nombres: requisito_list, caso_list, caso_create, caso_detail,
caso_update, ejecucion_create, trazabilidad.

## Permisos
- Requisitos: view_requisito. Casos: view_casoprueba,
  add_casoprueba, change_casoprueba, delete_casoprueba.
- Ejecuciones: view_ejecucionprueba y add_ejecucionprueba.
- /trazabilidad/ exige view_casoprueba.
- Sigue las demas reglas de docs/: vistas con clases genericas,
  formularios ModelForm con Bootstrap, templates con base.html.
