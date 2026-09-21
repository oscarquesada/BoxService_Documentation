# Agenda de trabajo — 8 semanas

## 1. Metodología

El trabajo se organiza en cuatro sprints de dos semanas. Cada sprint agrupa funcionalidades relacionadas y finaliza con una revisión del avance, pruebas y actualización de la documentación.

Los estados utilizados son:

- **Terminado:** funcionalidad implementada.
- **En desarrollo:** trabajo iniciado y todavía no finalizado.
- **Planificado:** trabajo pendiente para los próximos sprints.

## 2. Situación inicial

### Trabajo terminado

- Migración del frontend a React, TypeScript y Next.js.
- Migración del backend a ASP.NET Core y PostgreSQL.
- Autenticación con JWT/OIDC y roles de dueño, superadministrador y empleado.
- Rediseño de la pantalla de inicio de sesión.
- Mejoras en la vista operativa del taller, combos de service y validaciones.
- Revisión de código e integración de clientes con el backend real.

### Trabajo en desarrollo

- Migraciones de base de datos.
- Módulo de fotografías y checklist del vehículo.

## 3. Planificación semanal

| Semana | Sprint | Actividades principales | Resultado esperado | Estado inicial |
|---|---:|---|---|---|
| 1 | 1 | Revisión de requerimientos, definición del MVP, organización del Trello y repositorios | Alcance y documentación inicial definidos | Terminado |
| 2 | 1 | Infraestructura base, ambientes, CI/CD, manejo de secretos y despliegues de desarrollo en Vercel y Render | Entornos de desarrollo preparados | Planificado |
| 3 | 2 | Consolidación del login, roles y permisos; conexión del frontend con el backend real | Acceso seguro e integración funcional | Parcialmente terminado |
| 4 | 2 | Alta de talleres y empleados, middlewares de seguridad, auditoría, recuperación de contraseña y pruebas unitarias | Administración básica y controles de seguridad | Planificado |
| 5 | 3 | Migraciones pendientes, fotografías, checklist e inspección del vehículo | Registro documentado del estado del vehículo | En desarrollo |
| 6 | 3 | Arquitectura multi-tenant, dashboard del dueño, pagos, stock de repuestos y notificaciones | Datos separados por taller y herramientas de gestión | Planificado |
| 7 | 4 | Aplicación móvil y portal del cliente para consultar estados y aprobar presupuestos | Primer canal de autogestión del cliente | Planificado |
| 8 | 4 | Código QR del vehículo, agenda de turnos, pruebas integrales, correcciones, documentación y presentación final | Versión integrada y preparada para la entrega | Planificado |

## 4. Detalle por sprint

### Sprint 1 — Base técnica e infraestructura

**Semanas 1 y 2**

- Definir alcance y MVP.
- Preparar repositorios y documentación.
- Configurar ambientes de desarrollo.
- Implementar CI/CD.
- Configurar el manejo de secretos.
- Desplegar el frontend de desarrollo en Vercel.
- Desplegar el backend de desarrollo en Render.

### Sprint 2 — Seguridad y administración

**Semanas 3 y 4**

- Conectar el frontend con el login y los permisos por rol.
- Implementar el alta de talleres.
- Implementar la gestión de empleados.
- Crear middlewares de seguridad y verificaciones.
- Realizar una auditoría de seguridad.
- Incorporar recuperación de contraseña.
- Crear pruebas unitarias.

### Sprint 3 — Gestión y operación del taller

**Semanas 5 y 6**

- Completar migraciones.
- Finalizar fotografías y checklist del vehículo.
- Implementar pagos y stock de repuestos.
- Incorporar notificaciones por WhatsApp o SMS.
- Crear recordatorios automáticos de service.
- Crear el dashboard para el dueño del taller.
- Implementar la arquitectura multi-tenant.

### Sprint 4 — Experiencia del cliente y cierre

**Semanas 7 y 8**

- Desarrollar la aplicación móvil con React Native y TypeScript.
- Crear el portal del cliente.
- Permitir la consulta del estado del vehículo.
- Permitir la aprobación de presupuestos desde el celular.
- Incorporar un código QR para identificar el vehículo.
- Incorporar agenda de turnos.
- Ejecutar pruebas integrales.
- Corregir errores y actualizar documentación.
- Preparar la demostración y presentación final.

## 5. Seguimiento

Al finalizar cada semana, el equipo debe:

1. Revisar las tarjetas del Trello.
2. Actualizar el estado de las tareas.
3. Registrar bloqueos o dificultades.
4. Verificar los cambios integrados en GitHub.
5. Actualizar esta agenda si cambia el alcance.

## 6. Criterio de priorización

El equipo priorizará primero las funciones necesarias para completar el flujo del MVP. Las funciones complementarias podrán reprogramarse si afectan la estabilidad o la entrega del circuito principal.

