# Funcionamiento del sistema

**Revisión: 7 de noviembre de 2026.** Esta guía describe la web y el backend revisados. El alcance de mobile y del portal se distingue al final.

## 1. Ingreso y roles

El personal ingresa con usuario y contraseña. El backend autentica mediante JWT y determina el rol y el taller correspondiente.

- El administrador general gestiona la plataforma y los talleres.
- El dueño administra su taller, empleados y Métricas.
- El empleado opera sobre las funciones habilitadas de su taller; puede consultar el catálogo sin modificarlo.

Desactivar una cuenta impide sus nuevas peticiones mientras esté inactiva. Cambiar su contraseña hace que las sesiones anteriores dejen de superar la validación. La pérdida de acceso se detecta al realizar una petición; no se envía un cierre instantáneo en tiempo real a cada ventana.

## 2. Cliente y vehículo

El usuario busca o registra al cliente y después registra o selecciona un vehículo asociado a ese cliente. El alta verifica que el cliente pertenezca al mismo taller.

La asociación registra a quién se vincula el vehículo en el sistema; no comprueba su propiedad mediante documentación o un registro externo. Los nombres de usuario de acceso son únicos en la plataforma, aunque las personas trabajen en talleres diferentes.

## 3. Espacio “Taller”

Se selecciona un vehículo y se abre su ficha, con estas secciones:

- “Resumen”.
- “Presupuestos”.
- “Historial”.
- “Facturación”.
- “+ Nuevo presupuesto”.
- “+ Registrar service”.

“Cambiar vehículo” vuelve a la búsqueda. Un enlace `/taller?id=...` permite abrir la ficha directamente, sujeto a los controles de acceso.

## 4. Recorrido con presupuesto

1. El taller crea un presupuesto con conceptos de mano de obra/repuestos, cantidades y precios.
2. Registra su envío y la aprobación o rechazo recibido del cliente.
3. Sobre un presupuesto aprobado, “Realizar trabajo” permite revisar los conceptos y generar el service con su detalle.
4. Se vincula el presupuesto al service. El presupuesto pasa al estado de trabajo completado utilizado por ese circuito.
5. Al facturar, el backend usa los conceptos del presupuesto, valida la relación con el service/vehículo/taller y calcula el total.

La aprobación se registra desde la operación del taller. No se presenta como una respuesta que el cliente ya pueda dar por sí mismo desde el portal.

## 5. Recorrido sin presupuesto previo

Para un trabajo directo, se utiliza “+ Registrar service” sin crear presupuesto. Se cargan fecha, kilometraje, tipo de service, observaciones y detalle del trabajo.

Después, en “Facturación”, se pulsa “Facturar”. Aparece “Facturar service sin presupuesto”, donde se agregan conceptos manuales o del catálogo, cantidades y precios. El servidor calcula el importe y exige un total mayor a cero.

```text
Ejemplo:
Lámpara:                1 × $50.000 = $50.000
Colocación de lámpara:  1 × $30.000 = $30.000
Total de la factura:                 $80.000
```

No se necesita crear un presupuesto artificial. El service puede existir sin presupuesto y sin factura; son registros con responsabilidades distintas.

## 6. Inspección, checklist y fotografías

La inspección se abre desde “Historial”, mediante “Inspección” en el service correspondiente. Primero debe existir ese service, porque la inspección queda asociada a la visita/trabajo, no directamente al vehículo.

Permite registrar combustible, observaciones generales y el estado de 13 elementos del checklist: “Sin revisar”, “Sin daños” o “Con daño”, con observaciones por elemento.

Una vez creada la inspección pueden cargarse fotos, clasificarlas, describirlas y abrirlas con “Ver foto”. El backend admite JPEG, PNG y WebP de hasta 8 MB y hasta 12 fotos por inspección. Los archivos están en un bucket privado; se abren mediante enlaces firmados de duración limitada.

Cada consulta o modificación comprueba la pertenencia del service al taller. Una inspección ausente permite crearla solo si el service es accesible. Un recurso ajeno muestra el estado de error correspondiente.

Ver [guía frontend](docs/inspecciones/frontend.md) y [guía backend](docs/inspecciones/backend.md).

## 7. Qué significa “finalizar”

El modelo actual de `Service` no tiene un campo general de estado como “pendiente/en reparación/finalizado”. No se debe describir un botón de finalización general que no existe.

Hay estados de presupuesto y factura y un detalle de tareas del service. “Realizar trabajo” forma parte del circuito del presupuesto. El historial registra el service desde su creación; no espera al cobro.

## 8. Factura y detalle guardado

Las nuevas facturas conservan sus propios renglones: descripción, tipo, cantidad, precio y subtotal. Se guardan junto con la factura en una transacción.

“Imprimir / Guardar PDF” permite obtener la vista imprimible. Las facturas nuevas usan su detalle guardado; las históricas sin copia propia pueden usar el presupuesto asociado como respaldo. Cambiar el precio del catálogo no cambia los conceptos ya guardados con una factura nueva.

El service ya facturado deja de ofrecer “Facturar” en la pantalla. El servidor también rechaza emisiones duplicadas, incluso en el caso probado de dos solicitudes simultáneas.

## 9. Cobrar y anular

Una factura emitida queda pendiente. Al pulsar “Cobrar” se marca completa como cobrada y se registra automáticamente `paid_at` con el momento de esa acción.

No se admiten pagos parciales ni se elige una fecha pasada. Si el pago ocurrió antes y se registra hoy, el sistema utiliza hoy como fecha de registro del cobro.

“Anular” conserva la factura y su detalle, pero la excluye de las sumas de facturación/cobros. El circuito actual no admite emitir otra factura del mismo service aunque la anterior esté anulada.

## 10. Métricas del dueño

- “Total facturado este mes/año”: facturas no anuladas según su emisión.
- “Cobrado este mes/año”: facturas cobradas según el momento registrado en `paid_at`.
- “Pendiente de cobro (total)”: facturas emitidas sin cobrar, sin limitarlo al mes actual.

Si se factura en octubre y se registra el cobro en noviembre, el importe queda facturado en octubre y cobrado en noviembre. La fecha impresa de la factura sigue siendo la de emisión.

Las facturas antiguas cobradas sin fecha se informan aparte; no se inventa un mes para asignarlas. El gráfico “Facturación por mes” sigue siendo de facturado, y no se agregó un gráfico de cobros ni un selector histórico. Los rankings de trabajos toman presupuestos completados, no los conceptos de facturas directas.

## 11. Recordatorios y WhatsApp

“Recordatorios” muestra próximos services en siete días y los vencidos, según el último service del vehículo. El taller abre WhatsApp con un mensaje preparado y lo envía manualmente.

Abrir WhatsApp no confirma que se haya enviado el mensaje. Después de contactar al cliente, el usuario pulsa “Marcar como contactado”. Puede corregir el teléfono desde esa pantalla. No se implementó un envío automático ni SMS en este circuito.

## 12. Permisos y separación entre talleres

La interfaz muestra las opciones del rol, pero la autorización efectiva está en el backend. No alcanza con ocultar botones.

Un dueño/empleado no puede obtener inspecciones, enlaces nuevos de fotos o facturas de otro taller mediante un identificador ajeno en los casos comprobados. También se rechazan las relaciones cruzadas al crear registros. Los enlaces de fotos ya firmados pueden usarse por quien los tenga hasta su vencimiento.

## 13. Errores y operaciones en curso

La web utiliza tarjetas de error, navegación para volver y reintentos apropiados. Distingue falta de acceso, datos inexistentes y problemas de conexión.

Mientras consulta o guarda, muestra indicadores animados y textos de actividad. Los botones ocupados se bloquean para evitar envíos repetidos. Estas protecciones de interfaz complementan los controles del backend.

## 14. Alcance del portal y de mobile

El portal existente tiene consulta del cliente mediante invitación y Google, sujeto a configuración. La aprobación de presupuestos por el cliente y las solicitudes a varios talleres siguen pendientes.

Mobile tiene pantallas operativas y consulta/impresión de facturas, pero emitirlas y cobrarlas continúa indicado como “próximamente”. La última entrega de seguridad/facturación/feedback se modificó en web y backend; no se trasladó íntegramente a mobile.

El estado de pruebas, integración y limitaciones está en [MVP.md](MVP.md) y en el [informe de cambios](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md).
