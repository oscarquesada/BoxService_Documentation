# BoxService — Documentación del proyecto

BoxService es un sistema de gestión para talleres mecánicos. Reúne clientes, vehículos, presupuestos, services, inspecciones y facturas, y permite conservar el historial de cada vehículo.

Cada taller administra sus propios datos. La plataforma distingue al administrador general, al dueño y a los empleados; también tiene un portal del cliente y una aplicación móvil con alcances diferentes de la web.

**Última revisión documental: 7 de noviembre de 2026.**

## Por dónde empezar

| Documento | Para qué sirve |
|---|---|
| [Project Brief](PROJECT_BRIEF.md) | Problema, objetivos, usuarios y alcance del proyecto. |
| [MVP y estado funcional](MVP.md) | Qué existe, qué está parcialmente implementado y qué sigue pendiente. |
| [Funcionamiento del sistema](FUNCIONAMIENTO.md) | Recorrido del vehículo, facturación con/sin presupuesto, cobros y recordatorios. |
| [Tecnologías y arquitectura](TECNOLOGIAS.md) | Componentes reales, versiones, autenticación, datos y despliegue configurado. |
| [Agenda y seguimiento](AGENDA_TRABAJO.md) | Plan original de ocho semanas y seguimiento actualizado, sin confundir planificación con entrega. |

## Documentación técnica y cambios

- [Inspecciones: backend, tablas, checklist y fotografías](docs/inspecciones/backend.md).
- [Inspecciones: interfaz web y uso del checklist y las fotos](docs/inspecciones/frontend.md).
- [Correcciones del 7 de noviembre: seguridad, facturación, Métricas, errores y carga](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md).
- [Antecedente histórico: rediseño de Login y Taller](docs/historial/login-y-taller.md).

## Flujo principal

El taller registra o selecciona al cliente y su vehículo. A partir de ahí hay dos recorridos:

```text
Con presupuesto:
Cliente y vehículo → Presupuesto → Aprobación → Realizar trabajo → Service → Factura

Sin presupuesto previo:
Cliente y vehículo → Registrar service → Cargar conceptos y precios al facturar → Factura

Después de emitir:
Factura emitida → Cobrar → Factura cobrada y actualización de Métricas
```

El service aparece en el historial desde su registro; no necesita esperar a la factura. La inspección de ingreso y sus fotos se vinculan al service correspondiente a esa visita.

## Estado de la documentación

La web ya cuenta con inspecciones, checklist y fotografías. Existe una aplicación móvil con pantallas operativas, y el portal existente contempla la consulta del cliente. Esto no significa que todas las versiones tengan las mismas acciones: por ejemplo, mobile permite consultar/imprimir facturas, pero emitirlas y cobrarlas sigue indicado como “próximamente” en el código revisado.

Las correcciones recientes están publicadas en la rama `dev-oscar-seguridad-facturacion-metricas`:

- Backend: [`6595801`](https://github.com/maxi-gartner/BoxService_BackEnd/commit/6595801701de96e1a874db82535fb64a365d8c00).
- Frontend: [`9af5ecd`](https://github.com/maxi-gartner/BoxService_FrontEnd/commit/9af5ecd5148b37f8d8caccefa40ff61ca0eedcc3).

Se revisaron código local y resultados de pruebas. **No se da por confirmado el merge a `develop`/`main` ni un despliegue por el solo hecho de publicar esos commits.** El alcance exacto y los límites están en [MVP.md](MVP.md).

Las migraciones 016 y 017 se aplicaron manualmente en la base compartida utilizada para las pruebas. El [informe de cambios](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md) explica qué agregan y qué considerar en otras bases.

## Repositorios relacionados

- [BoxService_FrontEnd](https://github.com/maxi-gartner/BoxService_FrontEnd).
- [BoxService_BackEnd](https://github.com/maxi-gartner/BoxService_BackEnd).
- [BoxService_Mobile](https://github.com/maxi-gartner/BoxService_Mobile).

## Integrantes

- Oscar Quesada.
- Maximiliano Gartner.
- Leonardo Busch.
- Cristhian Carrasco.

Las responsabilidades pueden compartirse y ajustarse durante el proyecto. Este repositorio concentra documentación; el código de la aplicación se mantiene en sus repositorios respectivos.
