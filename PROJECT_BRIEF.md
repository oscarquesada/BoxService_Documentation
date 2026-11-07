# Project Brief — BoxService

**Revisión: 7 de noviembre de 2026.** El estado funcional se mantiene en [MVP.md](MVP.md); este documento presenta el propósito y el alcance.

## 1. Descripción

BoxService es una plataforma para organizar el trabajo de talleres mecánicos. Permite registrar clientes y vehículos, preparar presupuestos, registrar trabajos, documentar el estado de ingreso y gestionar sus facturas e historial.

## 2. Problema que aborda

La información de un taller puede quedar repartida entre papeles, planillas y mensajes. Eso dificulta recuperar trabajos anteriores, conocer qué se presupuestó, distinguir lo cobrado de lo pendiente y conservar evidencia de cómo ingresó un vehículo.

El sistema busca reunir esos datos en un circuito de uso cotidiano, con acceso según el rol y separación de la información de cada taller.

## 3. Objetivo general

Administrar la atención del vehículo desde su registro hasta el trabajo realizado, la facturación, el cobro registrado y la consulta de su historial.

## 4. Objetivos específicos

- Centralizar clientes, vehículos y trabajos.
- Preparar presupuestos y registrar su aprobación o rechazo.
- Permitir trabajos rápidos registrados directamente, sin presupuesto previo.
- Conservar una inspección con checklist y fotografías por visita/service.
- Emitir facturas con conceptos, cantidades y precios.
- Diferenciar facturado, cobrado y pendiente de cobro.
- Administrar talleres, usuarios y permisos.
- Restringir las operaciones al taller correspondiente.
- Facilitar el contacto manual por WhatsApp desde Recordatorios.
- Ofrecer acceso desde la web y ampliar el uso mediante mobile y el portal existente, respetando las limitaciones de cada versión.

## 5. Público objetivo

- Dueños de talleres pequeños y medianos.
- Empleados y personal administrativo del taller.
- Administrador general de la plataforma.
- Clientes que consultan la información habilitada de sus vehículos en el portal.

## 6. Equipo y áreas de participación

| Integrante | Área principal de participación |
|---|---|
| Oscar Quesada | Frontend, experiencia de usuario, vistas operativas e inspecciones con fotos y checklist. |
| Maximiliano Gartner | Integración, revisión de código, migraciones y desarrollo general. |
| Leonardo Busch | Autenticación, autorización, roles y seguridad. |
| Cristhian Carrasco | Backend, API, base de datos y migración a ASP.NET Core con PostgreSQL. |

Las áreas pueden compartirse y ajustarse durante los sprints; no implican propiedad exclusiva de los módulos.

## 7. Usuarios y responsabilidades

| Usuario | Responsabilidad |
|---|---|
| Administrador general (`superadmin`) | Gestionar los talleres y las funciones globales de administración. |
| Dueño (`dueno`) | Administrar su taller, sus empleados y sus datos operativos; consultar Métricas. |
| Empleado (`empleado`) | Ejecutar las operaciones habilitadas de su taller. Puede consultar el catálogo sin modificarlo. |
| Cliente del portal (`customer`) | Consultar la información de sus vehículos que el portal existente expone. |

La consulta del portal tiene implementación. La aprobación/rechazo de presupuestos por el propio cliente y el envío de solicitudes a múltiples talleres no se consideran terminados. La aplicación móvil existente es una interfaz operativa; no debe presentarse toda ella como una aplicación de autogestión del cliente.

## 8. Alcance actual

- Gestión web de talleres, usuarios, clientes, vehículos, presupuestos, services, facturas y catálogo.
- Inspección por service, checklist y fotografías privadas con enlaces temporales.
- Facturación desde presupuesto y facturación directa de un service sin presupuesto.
- Consulta del historial y vistas de impresión.
- Registro del cobro completo de una factura, anulación y métricas del dueño.
- Recordatorios próximos/vencidos y contacto manual por WhatsApp.
- Aplicación móvil con pantallas y capacidades parciales respecto de la web.
- Portal existente para consulta del cliente, sujeto a su configuración e invitación.

## 9. Recorridos de referencia

```text
Con presupuesto: Cliente y vehículo → Presupuesto aprobado → Service → Factura → Cobro
Directo: Cliente y vehículo → Registrar service → Facturar conceptos y precios → Cobro
```

La inspección y el historial se vinculan al service. El historial conserva el trabajo desde su registro, independientemente de cuándo se facture o cobre.

## 10. Criterios de éxito

- El usuario accede a las funciones de su rol.
- Las operaciones rechazan recursos y relaciones de otros talleres.
- El taller registra clientes y vehículos y recupera su historial.
- Un presupuesto aprobado puede continuar como trabajo realizado.
- Un trabajo sin presupuesto puede facturarse con un importe válido.
- El checklist y las fotografías se conservan y pueden consultarse.
- Una factura no se duplica para el mismo service en los casos probados.
- El dueño distingue lo facturado, lo cobrado y lo pendiente.
- La interfaz comunica cargas, resultados y errores sin quedar indefinidamente en “Cargando…”.

Estos criterios orientan la validación: no constituyen una certificación de todas las rutas ni de todos los entornos. Los casos y resultados efectivamente comprobados están en el [informe de cambios](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md).

## 11. Restricciones y ampliaciones

El proyecto es académico y su planificación original comprende ocho semanas. Los servicios externos dependen de configuración y disponibilidad. Stock, agenda/turnos, QR, aprobación del cliente, pagos parciales y mensajería automática deben conservarse como ampliaciones pendientes hasta confirmar su implementación.

Las correcciones recientes están publicadas en ramas de trabajo; su aprobación, integración y despliegue deben verificarse por separado.
