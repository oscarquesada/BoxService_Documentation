# Tecnologías y arquitectura

**Revisión de código/configuración: 7 de noviembre de 2026.** Las versiones indicadas se tomaron de manifiestos y compilaciones revisados; las dependencias con rangos pueden resolverse a versiones diferentes según su lockfile.

## 1. Componentes

```text
Web Next.js → Proxy de la web → Backend ASP.NET Core → PostgreSQL / Supabase
Mobile React Native/Expo ─────→ Backend compartido ───→ Supabase Storage (fotos)
Portal web del cliente ──────→ Rutas del portal en el backend
```

La web es la parte visual; el backend procesa acciones y reglas; PostgreSQL conserva los registros. Supabase aloja la base y el almacenamiento de fotografías utilizado por el proyecto. Mobile no tiene un backend propio independiente dentro de estos repositorios.

## 2. Frontend web

| Tecnología | Versión/referencia revisada | Uso |
|---|---|---|
| TypeScript | Rama 5.x declarada | Tipado de páginas, componentes y contratos. |
| React / React DOM | 19.2.8 | Interfaz por componentes. |
| Next.js | 16.3.8 en el build revisado | Rutas, renderizado y proxy del frontend. |
| TanStack React Query | 5.x | Consultas, caché, mutaciones y actualización de datos. |
| React Hook Form + Zod | Declarados en `web/package.json` | Formularios y validaciones visibles. |
| Tailwind CSS | 4.x | Estilos de la web. |
| jose | Declarado en `web/package.json` | Lectura de claims del token en el servidor web. |
| @react-oauth/google | Declarado en `web/package.json` | Componente de ingreso Google del portal. |
| ESLint | 9.x | Comprobaciones de calidad del código. |

La aplicación actual está dentro de `web/` en BoxService_FrontEnd. La lectura del rol en la web sirve para navegación e interfaz; el backend verifica la firma y autorización de las solicitudes.

## 3. Backend

| Tecnología | Versión/referencia revisada | Uso |
|---|---|---|
| C# | Proyecto con destino `net10.0` | Lenguaje del servidor. |
| ASP.NET Core / .NET | **10** | API HTTP y autenticación. |
| Npgsql | 10.0.3 declarado | Consultas parametrizadas y transacciones PostgreSQL. |
| JWT Bearer | 10.0.12 declarado | Validación de tokens del backend. |
| Google.Apis.Auth | 1.77.0 declarado | Validación de identidad Google del portal. |
| Supabase SDK | 8.1.0 declarado | Integración declarada con Supabase; fotografías mediante el servicio Storage del proyecto. |
| xUnit | Proyecto de pruebas unitarias | Pruebas locales del backend. |

La entrada del servidor está en `Program.cs`. Se usan endpoints de ASP.NET Core y capas de servicios/repositorios. La persistencia revisada utiliza SQL mediante Npgsql; no se debe presentar Entity Framework como el mecanismo de estas migraciones.

## 4. Base de datos y fotografías

- PostgreSQL en Supabase para talleres, usuarios, clientes, vehículos, presupuestos, services, facturas e inspecciones.
- Supabase Storage para archivos de fotos, en el bucket privado `vehicle-inspections`.
- Las tablas guardan rutas y metadatos de las fotos; no sus bytes.
- El backend entrega URLs firmadas temporales para verlas.
- Migraciones SQL en `Database/migrations/`, con nombres numerados.

Las migraciones recientes agregan `facturas.paid_at` y `detalle_factura`. Activar RLS en una tabla no reemplaza los filtros por taller del backend ni implica que se hayan definido políticas para todas las tablas.

`dotnet run -- migrate` ejecuta todos los archivos de migraciones en orden contra la base configurada; no solo los últimos dos. El detalle de su aplicación y de los datos históricos está en el [informe de cambios](docs/cambios/2026-11-07-seguridad-facturacion-metricas.md).

## 5. Autenticación y autorización

- Personal: usuario/contraseña, verificación del hash y emisión de JWT propio del backend.
- Roles backend: `superadmin`, `dueno`, `empleado`; la web normaliza los dos últimos como `owner` y `employee`.
- Portal: invitación/vínculo con Google y token de aplicación para el cliente (`customer`).
- El proveedor Google no reemplaza el ingreso de personal ni convierte toda la autenticación en un único flujo OIDC.
- La web conserva el token de personal en cookies HTTP-only y lo envía al backend mediante su proxy.
- Las correcciones recientes validan usuario/taller activos y versión de credencial en nuevas peticiones.
- Los secretos se configuran fuera de los documentos públicos; no se incluyen valores de claves, contraseñas ni cadenas de conexión aquí.

El restablecimiento administrativo de contraseñas existe. La opción autónoma “¿Olvidaste tu contraseña?” no completa todavía un flujo real de recuperación.

## 6. Aplicación móvil

| Tecnología | Versión declarada revisada | Uso |
|---|---|---|
| React Native | 0.86.3 | Interfaz móvil. |
| React | 19.2.3 | Componentes. |
| Expo | ~57.0.25 | Ejecución y herramientas de la app. |
| Expo Router | ~57.0.23 | Navegación. |
| TypeScript | ~6.0.3 | Tipado. |
| TanStack React Query | 5.x | Comunicación y caché de datos. |
| NativeWind | 4.x | Estilos. |
| Expo Secure Store | Declarado | Almacenamiento seguro utilizado por la app. |
| Expo Print / Sharing | Declarados | Generación/compartición de documentos. |

Hay código y pantallas existentes; no es únicamente una propuesta futura. `app.json` y `eas.json` contienen configuración Expo/EAS. Esa configuración no confirma por sí sola una publicación en tiendas. Los límites funcionales están en [MVP.md](MVP.md).

## 7. Servicios externos

- **Google:** identidad del cliente para el portal existente.
- **Supabase:** PostgreSQL y fotografías privadas.
- **ArgAutos:** catálogo de marcas, modelos y versiones consultado por el backend.
- **WhatsApp:** enlaces con mensajes preparados; el envío actual es manual.

## 8. Separación por taller

La arquitectura multi-tenant ya tiene implementación: cada operación utiliza el taller del usuario autenticado. Las mejoras recientes refuerzan comprobaciones de recursos y relaciones que faltaban.

Ejemplo: crear un vehículo exige que su cliente sea del mismo taller. Abrir o modificar una inspección exige que su service pertenezca al taller autenticado. El administrador global tiene rutas y facultades diferentes de las operativas del dueño/empleado.

## 9. Herramientas y despliegue configurado

| Herramienta/configuración | Qué se confirmó |
|---|---|
| Git / GitHub | Repositorios separados y trabajo con ramas/PR. |
| Trello | Herramienta indicada en la planificación del equipo; no se auditó su tablero. |
| GitHub Actions frontend | Instalación, lint, typecheck y build; Node 22 en el workflow revisado. |
| GitHub Actions backend | Restore, build y compilación de la imagen Docker con .NET 10. No se observó un paso `dotnet test` en ese workflow. |
| Docker | Dockerfile del backend con imágenes SDK/runtime .NET 10. |
| Render | `render.yaml` define servicios Docker asociados a `develop` y `main`, con secretos configurables externamente. |
| Vercel | Referido por el equipo y por la configuración/comentarios del frontend; no se verificó su dashboard ni un despliegue activo en esta revisión. |
| Expo/EAS | Configuración presente para mobile; distribución efectiva no verificada. |

Un workflow de compilación es integración continua. No demuestra que todas las pruebas se ejecuten allí ni que una versión haya sido desplegada. La configuración y el estado real de cada entorno deben verificarse antes de una demostración pública.

## 10. Repositorios

- [Frontend](https://github.com/maxi-gartner/BoxService_FrontEnd).
- [Backend](https://github.com/maxi-gartner/BoxService_BackEnd).
- [Mobile](https://github.com/maxi-gartner/BoxService_Mobile).

Este documento no modifica configuraciones ni recomienda publicar secretos. Los ejemplos completos de contratos y los archivos de cada cambio se encuentran en la documentación técnica enlazada desde el README.
