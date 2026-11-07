# Agenda de trabajo y seguimiento — BoxService

**Actualización documental: 7 de noviembre de 2026.**

## 1. Separar plan y avance

El plan original se organizó en cuatro sprints de dos semanas. Se conserva como referencia de objetivos, no como un calendario que pruebe cuándo se terminó o desplegó cada función.

El estado funcional actual se mantiene en [MVP.md](MVP.md). La fecha de esta actualización no asigna retrospectivamente fechas de ejecución a los sprints.

## 2. Planificación original de ocho semanas

| Semana | Sprint | Objetivo original |
|---|---:|---|
| 1 | 1 | Requerimientos, MVP, organización del Trello y repositorios. |
| 2 | 1 | Ambientes, manejo de secretos, CI/CD y despliegues de desarrollo. |
| 3 | 2 | Consolidación del login, roles e integración frontend/backend. |
| 4 | 2 | Talleres, empleados, seguridad, contraseñas y pruebas unitarias. |
| 5 | 3 | Migraciones, fotografías, checklist e inspección del vehículo. |
| 6 | 3 | Separación por taller, métricas, pagos, stock y notificaciones. |
| 7 | 4 | Mobile y portal, con consulta y futura autogestión del cliente. |
| 8 | 4 | QR, agenda, pruebas integrales, documentación y exposición final. |

Agrupar tareas en una semana no significa que todas tengan el mismo avance. Por ejemplo, catálogo no equivale a stock, y restablecimiento administrativo no equivale a recuperación autónoma de contraseña.

## 3. Seguimiento actualizado

| Área del plan | Situación revisada | Próximo paso |
|---|---|---|
| Base web y backend | Next.js/React/TypeScript y ASP.NET Core/.NET 10/PostgreSQL presentes y compilados. | Mantener configuración y documentación alineadas. |
| Integración continua | Workflows presentes; frontend con lint/typecheck/build y backend con build/Docker. | Revisar ejecución de tests en CI; no asumir despliegue por un build correcto. |
| Autenticación y roles | Implementación existente y pruebas de ingreso. | Integrar/revisar los refuerzos recientes de vigencia de sesión. |
| Talleres, empleados y pagos mensuales | Código de administración presente; gestión de empleado probada. | Validar en el entorno de destino las funciones no recorridas en esta ronda. |
| Inspecciones, checklist y fotos web | Implementados, documentados y probados. | Mantener pruebas de acceso entre talleres y revisar los cambios en sus PR. |
| Clientes, vehículos e historial | Alta/consulta y circuito operativo probados. | No anunciar edición completa de todos los registros sin verificarla. |
| Seguridad entre talleres | Correcciones y pruebas directas publicadas en la rama nueva. | Revisión/merge y comprobación posterior en el entorno integrado. |
| Facturación y Métricas | Dos recorridos de facturación, detalle y cobros implementados/probados en la rama nueva. | Integración coordinada de web/backend y esquema de base. |
| Recordatorios web | Implementación y corrección de repetición de la migración 015 publicadas en trabajo previo. | Confirmar aprobación/merge del PR correspondiente. |
| WhatsApp/SMS automático | WhatsApp manual existe; envío automático y SMS no están completados. | Definir alcance separado si se decide automatizar. |
| Mobile | Aplicación operativa parcial existente. | Resolver permisos de publicación y adaptar facturación/métricas con su responsable. |
| Portal | Código de consulta e ingreso Google presente; no probado en vivo en esta ronda. | Validar configuración y separar consulta de aprobación/autogestión todavía pendiente. |
| Stock, QR y turnos | No se presentan como terminados. | Priorizar con el equipo según tiempo disponible. |
| Documentación | Se actualizan estos seis documentos y se incorporan informes detallados. | Revisar el PR documental y mantener el README como índice. |

## 4. Entrega reciente

Rama de aplicación: `dev-oscar-seguridad-facturacion-metricas`.

- Backend: `6595801`.
- Frontend: `9af5ecd`.
- Cambios de base: 016 (`facturas.paid_at`) y 017 (`detalle_factura`).
- Las dos migraciones ya fueron aplicadas manualmente en la base utilizada para probar.
- Compilaciones y pruebas realizadas: ver [informe de cambios](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md).

Las ramas nuevas conservan commits de Recordatorios porque parten del trabajo anterior. Antes de integrar, corresponde revisar la comparación con `develop` y las dependencias entre PR. Esta agenda no certifica que esos PR estén aprobados o fusionados.

## 5. Criterio de cierre de una tarea

Conviene registrar por separado:

1. **Implementación:** código presente en una rama identificada.
2. **Validación:** qué pruebas se hicieron, resultado y limitaciones.
3. **Publicación de la rama:** commit/push y PR correspondiente.
4. **Integración:** aprobación y merge a la rama de destino.
5. **Entorno de uso:** migraciones/configuración necesarias y versión desplegada, si corresponde.

Evitar un único “Terminado” que haga parecer equivalentes escribir código, probarlo localmente e integrarlo en el entorno compartido.

## 6. Prioridades siguientes

- Revisar e integrar los PR de aplicación y sus dependencias.
- Verificar el recorrido corto de demostración en la versión integrada.
- Confirmar alcance y estado publicado de mobile con el compañero responsable.
- Mantener diferenciados los pendientes reales: aprobación del cliente, recuperación autónoma, stock, automatizaciones, QR y agenda.
- Preparar la exposición con funciones comprobadas, sin presentar un roadmap como producto terminado.

No se modifican aquí fechas de examen, asignaciones del Trello ni compromisos del equipo que no hayan sido confirmados.
