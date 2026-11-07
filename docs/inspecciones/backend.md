# Checklist y fotografías de ingreso del vehículo

**Revisión: 7 de noviembre de 2026.** Se conservan los contratos del módulo y se actualizan el estado del frontend y los controles de acceso. Los ejemplos de configuración contienen marcadores de posición, no credenciales reales.

## Objetivo

Esta funcionalidad permite registrar el estado en el que ingresa un vehículo al taller para un service determinado.

El registro incluye:

- Nivel de combustible.
- Observaciones generales.
- Persona que realizó la inspección.
- Fecha y hora de inspección.
- Checklist del estado del vehículo.
- Fotografías de daños o del estado general del vehículo.

El objetivo es conservar evidencia del estado inicial del vehículo para cada visita al taller.

## Relación con el sistema

La inspección se vincula directamente con un `service`, porque un mismo vehículo puede ingresar varias veces al taller.

```text
Cliente → Vehículo → Service → Inspección → Ítems y fotografías
```

No se guardan nuevamente el cliente ni el vehículo dentro de la inspección. Esa información se obtiene mediante el service asociado.

Cada service puede tener una sola inspección debido a la restricción `UNIQUE` sobre `id_service`.

## Base de datos

La migración correspondiente es:

```text
Database/migrations/010_crear_service_inspections.sql
```

La migración crea tres tablas.

### `service_inspections`

Representa la inspección general realizada al ingresar el vehículo.

| Campo | Tipo | Descripción |
|---|---|---|
| `id_inspection` | SERIAL | Clave primaria |
| `id_service` | INT | Service inspeccionado |
| `fuel_level` | SMALLINT | Nivel de combustible entre 0 y 100 |
| `general_observations` | TEXT | Observaciones generales |
| `inspected_by` | VARCHAR(100) | Usuario que realizó la inspección |
| `inspected_at` | TIMESTAMPTZ | Fecha y hora de la inspección |

`id_service` es una clave foránea hacia `services(id_service)` y utiliza `ON DELETE CASCADE`. Si se elimina un service, también se elimina su inspección.

### `service_inspection_items`

Guarda el resultado de cada elemento del checklist.

| Campo | Tipo | Descripción |
|---|---|---|
| `id_item` | SERIAL | Clave primaria |
| `id_inspection` | INT | Inspección a la que pertenece |
| `item_code` | VARCHAR(50) | Código interno del elemento |
| `item_name` | VARCHAR(100) | Nombre visible |
| `status` | VARCHAR(20) | Estado del elemento |
| `observation` | VARCHAR(500) | Observación opcional |

Estados permitidos:

- `ok`: el elemento está en buen estado.
- `damaged`: se detectó un daño.
- `not_checked`: el elemento no fue revisado.

La combinación de `id_inspection` e `item_code` es única para evitar elementos duplicados. Si se elimina una inspección, sus ítems se eliminan mediante `ON DELETE CASCADE`.

### `service_inspection_photos`

Guarda los datos de las fotografías asociadas a una inspección.

| Campo | Tipo | Descripción |
|---|---|---|
| `id_photo` | SERIAL | Clave primaria |
| `id_inspection` | INT | Inspección relacionada |
| `storage_path` | TEXT | Ruta de la imagen en Supabase Storage |
| `photo_type` | VARCHAR(30) | Tipo o categoría de fotografía |
| `description` | VARCHAR(500) | Descripción opcional |
| `original_file_name` | VARCHAR(255) | Nombre original del archivo |
| `content_type` | VARCHAR(100) | Tipo MIME |
| `created_at` | TIMESTAMPTZ | Fecha de carga |

La imagen no se guarda dentro de PostgreSQL. La tabla solamente almacena su ruta y sus metadatos. El archivo real se almacena en Supabase Storage.

## Checklist definido

Los elementos actuales se encuentran en `Services/InspectionChecklistCatalog.cs`:

- Paragolpes delantero.
- Paragolpes trasero.
- Puertas.
- Capó.
- Baúl.
- Parabrisas y vidrios.
- Espejos.
- Luces.
- Neumáticos.
- Interior y tapizados.
- Tablero.
- Rueda de auxilio y herramientas.
- Objetos personales.

Cuando un elemento no se envía en la solicitud, queda registrado con el estado `not_checked`.

## Supabase Storage

Se creó un bucket privado llamado `vehicle-inspections` con la siguiente configuración:

- Bucket privado.
- Tamaño máximo por archivo: 8 MB.
- Tipos permitidos: `image/jpeg`, `image/png` e `image/webp`.

Las fotografías utilizan una ruta similar a:

```text
services/{serviceId}/inspections/{inspectionId}/{identificador}.{extension}
```

Ejemplo:

```text
services/39/inspections/1/422245d9f55642619a057497bb7b0707.png
```

El bucket es privado. El backend genera una URL firmada temporal con una duración informada de 900 segundos, y el navegador accede al archivo mediante ese enlace. No recibe la clave privada de Supabase. Quien tenga el enlace puede usarlo mientras siga vigente.

Cuando la URL vence, la fotografía no se elimina. Solamente se debe solicitar una URL nueva.

## Configuración

La configuración local se guarda en `appsettings.json`:

```json
"Supabase": {
  "Url": "URL_DEL_PROYECTO",
  "Key": "SECRET_KEY_DEL_BACKEND",
  "Bucket": "vehicle-inspections"
}
```

También pueden utilizarse estas variables de entorno:

```text
SUPABASE_URL
SUPABASE_KEY
SUPABASE_BUCKET
```

La secret key debe utilizarse solamente en el backend. Nunca debe subirse a GitHub, guardarse en el frontend, enviarse al navegador ni incluirse en documentación pública. `appsettings.json` debe permanecer ignorado por Git.

## Endpoints

Todos estos endpoints requieren autenticación JWT, rol `dueno` o `empleado` y un service perteneciente al taller autenticado. Comparten `TenantServiceFilter`; un service ajeno devuelve 404 antes de ejecutar la operación o acceder a Storage.

### Obtener la inspección

```http
GET /services/{serviceId}/inspection
```

Devuelve la inspección completa, incluyendo checklist y metadatos de las fotografías. Si el service todavía no tiene una inspección, devuelve `404`.

### Crear una inspección

```http
POST /services/{serviceId}/inspection
Content-Type: application/json
Authorization: Bearer {token}
```

Ejemplo:

```json
{
  "fuelLevel": 50,
  "generalObservations": "Rayón visible en la puerta derecha.",
  "items": [
    {
      "itemCode": "front_bumper",
      "status": "ok",
      "observation": null
    },
    {
      "itemCode": "doors",
      "status": "damaged",
      "observation": "Rayón en puerta derecha"
    }
  ]
}
```

El usuario que realiza la inspección se obtiene del JWT y se guarda en `inspected_by`.

### Actualizar una inspección

```http
PATCH /services/{serviceId}/inspection
Content-Type: application/json
Authorization: Bearer {token}
```

Permite actualizar el combustible, las observaciones y los elementos del checklist.

### Subir una fotografía

```http
POST /services/{serviceId}/inspection/photos
Content-Type: multipart/form-data
Authorization: Bearer {token}
```

Campos:

| Campo | Descripción |
|---|---|
| `file` | Archivo de imagen |
| `photoType` | Tipo de fotografía, por ejemplo `damage` |
| `description` | Descripción opcional |

La inspección debe existir antes de agregar fotografías. El backend valida tipo de archivo, tamaño máximo de 8 MB y un máximo de 12 fotografías por inspección.

### Obtener URL de una fotografía

```http
GET /services/{serviceId}/inspection/photos/{photoId}/url
Authorization: Bearer {token}
```

Respuesta:

```json
{
  "success": true,
  "data": {
    "url": "URL_FIRMADA_TEMPORAL",
    "expiresIn": 900
  },
  "error": null
}
```

### Eliminar una fotografía

```http
DELETE /services/{serviceId}/inspection/photos/{photoId}
Authorization: Bearer {token}
```

Elimina el archivo de Supabase Storage y el registro de `service_inspection_photos`.

## Archivos incorporados

| Archivo | Responsabilidad |
|---|---|
| `Models/ServiceInspection.cs` | Modelos de inspección, ítems y fotografías |
| `DTOs/ServiceInspectionDto.cs` | Contratos de entrada y salida de la API |
| `Repositories/ServiceInspectionRepository.cs` | Acceso a PostgreSQL |
| `Services/InspectionChecklistCatalog.cs` | Catálogo de elementos del checklist |
| `Services/ServiceInspectionService.cs` | Validaciones y lógica de negocio |
| `Storage/SupabaseStorageService.cs` | Comunicación con Supabase Storage |
| `Api/ServiceInspectionEndpoints.cs` | Grupo de endpoints y autorización |
| `Api/TenantServiceFilter.cs` | Comprobación compartida de pertenencia del service |
| `Database/migrations/010_crear_service_inspections.sql` | Creación de las tablas |

También se modificaron `Program.cs`, para registrar los servicios y mapear los endpoints, y `BoxService_BackEnd.csproj`, para agregar el paquete de Supabase.

## Registro de dependencias

En `Program.cs` se registran:

```csharp
builder.Services.AddScoped<ServiceInspectionRepository>();
builder.Services.AddScoped<ServiceInspectionService>();
builder.Services.AddSingleton<SupabaseStorageService>();
builder.Services.AddScoped<ITenantServiceAccess, TenantServiceAccess>();
```

También se agregan los endpoints:

```csharp
app.MapServiceInspectionEndpoints();
```

## Ejecución del backend

El proyecto utiliza .NET 10.

```powershell
dotnet restore
dotnet build -p:UseAppHost=false
dotnet .\bin\Debug\net10.0\BoxService_BackEnd.dll
```

El backend queda disponible en `http://localhost:5001`.

Para comprobar la conexión:

```powershell
Invoke-RestMethod "http://localhost:5001/health" |
ConvertTo-Json -Depth 5
```

## Pruebas realizadas

La funcionalidad se probó con el service `39`.

- Se creó correctamente la inspección `1`.
- Se registraron 13 elementos del checklist.
- Se registró el nivel de combustible.
- Se guardaron observaciones generales.
- Se marcó un daño en una puerta.
- Se subió una imagen PNG al bucket privado.
- Se guardaron sus metadatos en PostgreSQL.
- Se generó una URL firmada.
- La imagen pudo visualizarse desde el navegador.
- La inspección devolvió correctamente sus ítems y su fotografía.
- El proyecto compiló y funcionó con .NET 10.

## Seguridad

- Las rutas requieren un JWT válido.
- La secret key de Supabase permanece únicamente en el backend.
- El bucket no es público.
- Las imágenes se visualizan mediante URLs temporales.
- La base de datos almacena solamente la ruta y los metadatos.
- Los archivos admitidos están limitados a JPEG, PNG y WEBP.
- El tamaño máximo configurado es de 8 MB.

## Estado actual

El backend y la interfaz web de inspecciones están implementados. La web permite consultar y editar el checklist, registrar combustible y observaciones, subir fotografías y abrir sus enlaces firmados. El código incluye eliminación de fotos; la última ronda no ejecutó un borrado efectivo de una imagen.

El modal distingue ausencia de inspección de falta de acceso al service y muestra estados de error y carga. Ver [documentación frontend](frontend.md).

Los controles recientes de pertenencia se publicaron con las correcciones de seguridad. El merge y el despliegue deben verificarse; una prueba local no confirma publicación en todos los entornos.

## Pruebas recientes y límites

- Con el dueño del taller se consultó la inspección original y se abrió su foto.
- Otro taller recibió 404 al consultar la misma inspección y al solicitar un enlace nuevo para su foto.
- Se probaron consultas y escrituras ajenas con datos QA, sin modificar los registros originales.
- La ruta de borrado se probó con un identificador de foto inexistente: devolvió el error de pertenencia del service antes de buscar la imagen. No se borró una foto real.
- No se ejecutó una auditoría exhaustiva de todos los roles ni se reconstruyeron datos históricos.

La evidencia, las limitaciones de URLs firmadas y los registros QA conservados están en el [informe de cambios](../cambios/2026-11-07-seguridad-facturacion-metricas.md).
