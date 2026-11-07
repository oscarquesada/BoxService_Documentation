# MVP y estado funcional de BoxService

**Revisión: 7 de noviembre de 2026.**

## 1. Qué cubre el MVP

El circuito mínimo permite registrar cliente y vehículo, registrar un trabajo con o sin presupuesto, conservar su inspección e historial y emitir una factura con conceptos e importes.

El presupuesto es parte del recorrido habitual, pero no es obligatorio para un trabajo directo. Registrar un cobro no implica procesar un pago bancario o una pasarela: el usuario indica que la factura fue cobrada.

## 2. Cómo se interpretan los estados

| Estado | Significado |
|---|---|
| Confirmado | Función revisada en código y comprobada en las pruebas indicadas. |
| Código presente | Se identificó una implementación; no se verificó todo su recorrido en esta ronda. |
| Parcial | Algunas acciones están implementadas y otras siguen pendientes. |
| Pendiente | No se presenta una implementación completa confirmada. |

Los estados describen el código y las pruebas revisadas, **no confirman que esté integrado ni desplegado**. Las correcciones nuevas corresponden a la rama `dev-oscar-seguridad-facturacion-metricas`, commits backend `6595801` y frontend `9af5ecd`.

## 3. Estado por funcionalidad

| Área | Estado de la web/backend revisados | Alcance y límites |
|---|---|---|
| Ingreso de personal | Confirmado | Usuario/contraseña y JWT del backend. |
| Roles y separación por taller | Confirmado en los casos probados | Dueño, empleado y administrador; se reforzaron rutas y relaciones. No equivale a una auditoría exhaustiva de todos los endpoints. |
| Gestión de talleres y pagos mensuales de talleres | Código presente | Administración global; pagos de suscripción del taller, no cobros de reparaciones ni stock. |
| Alta y activación/desactivación de empleados | Confirmado | El dueño gestiona a sus empleados; se probó pérdida de acceso de una cuenta desactivada. |
| Restablecer contraseña por dueño/administrador | Confirmado | Se probó invalidación de la sesión anterior. La recuperación autónoma desde el login sigue pendiente. |
| Clientes | Confirmado para alta y consulta | No se presenta edición completa de todos los datos como una función confirmada. Recordatorios permite actualizar el teléfono por su ruta específica. |
| Vehículos e historial | Confirmado para alta, asociación y consulta | El registro asocia un cliente; no realiza una verificación documental de propiedad. No se confirma edición completa del vehículo. |
| Catálogo | Código presente y permisos visibles probados | Ítems de mano de obra/repuestos y precios; no implica existencias de inventario. |
| Presupuestos | Confirmado | Creación, estados, edición permitida y continuidad al trabajo. La decisión se registra desde el taller. |
| Service desde presupuesto | Confirmado | Generación desde un presupuesto aprobado y vínculo con el vehículo. |
| Service sin presupuesto | Confirmado | Registro directo e historial; se puede facturar cargando conceptos y precios. |
| Estado general de avance del service | Pendiente de definir como flujo independiente | El modelo actual no tiene un campo general de estado; no se describe un botón inexistente de finalización del service. |
| Inspección, checklist y fotos web | Confirmado | Formulario, 13 elementos, fotografías, consulta y controles de acceso probados. No se ejecutó un borrado real en la última ronda. |
| Facturas web | Confirmado | Emisión con/sin presupuesto, detalle propio, consulta, impresión/PDF, cobro completo y anulación. |
| Prevención de facturas duplicadas | Confirmado en los casos probados | Rechazo de segunda emisión y de una de dos solicitudes simultáneas. |
| Métricas del dueño | Confirmado para los recorridos probados | Facturado, cobrado, pendiente y aviso de cobros antiguos sin fecha. No hay selector de períodos ni pagos parciales. |
| Recordatorios web | Implementación y pruebas previas registradas | Próximos en siete días y vencidos, WhatsApp manual y confirmación manual de contacto. Integración del PR previo por verificar. |
| Portal del cliente | Parcial | Invitación/Google y consulta con código presente. Configuración e ingreso real no probados en esta ronda; aprobación del cliente pendiente. |
| Aplicación móvil | Parcial | Tiene implementación operativa y pantallas. No es únicamente una planificación. La última ronda no actualizó ni probó mobile en ejecución. |
| Mensajes y cargas web | Confirmado en comprobaciones locales y revisión visual | Estados comunes de error, animaciones, reintentos apropiados y botones ocupados. |

## 4. Diferencias de mobile

El proyecto mobile contiene rutas para login, Taller, clientes, vehículos, facturas y administración, entre otras. Utiliza React Native con Expo y consume el backend compartido.

En el código revisado se pueden consultar e imprimir facturas. **Emitir y cobrar facturas desde mobile sigue mostrando “próximamente”.** La impresión actual de mobile obtiene conceptos del presupuesto; no se adaptó en esta entrega para utilizar el detalle propio de una factura directa. Sus etiquetas de “Ingresos” tampoco incorporaron la separación nueva de la web entre facturado y cobrado.

Las mejoras de Recordatorios de mobile se desarrollaron localmente en trabajo anterior, pero no se confirmó su publicación porque el push había sido rechazado por permisos. No deben presentarse como integradas en el repositorio remoto sin verificarlo con su responsable.

## 5. Portal del cliente

El portal existente permite consultar los vehículos y datos habilitados del cliente. No se incluye como terminado:

- Aprobación/rechazo del presupuesto por el propio cliente.
- Crear una solicitud y enviarla a varios talleres.
- Comparar propuestas y elegir un taller.

Esos recorridos deben analizarse e implementarse por separado. No forman parte de las dos migraciones recientes de facturación.

## 6. Ampliaciones pendientes

- Pagos parciales de una factura y fechas de cobro ingresadas por el usuario.
- Stock e inventario de repuestos.
- Envío automático de WhatsApp o SMS; los recordatorios actuales requieren acción manual.
- Agenda y reserva de turnos.
- QR del vehículo.
- Recuperación autónoma de contraseña.
- Auditoría integral.
- Paridad de funcionalidades entre web y mobile.
- Recorrido de aprobación/autogestión del cliente.

La facturación fiscal y las pasarelas de pago reales no se consideran resueltas por generar un comprobante imprimible o marcar “Cobrar”.

## 7. Validación e integración

Se registraron compilaciones correctas, 19 pruebas unitarias, 56 comprobaciones de reglas locales, 27 comprobaciones de integración y 20 de feedback web. Son grupos distintos de pruebas y no una suite homogénea única. El [informe detallado](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md) explica qué cubre cada uno, qué no se ensayó y los datos QA conservados.

Las migraciones 016 (`paid_at`) y 017 (`detalle_factura`) ya se aplicaron manualmente en la base utilizada para probar. Su presencia debe verificarse en cualquier otro entorno antes de utilizar el backend nuevo.
