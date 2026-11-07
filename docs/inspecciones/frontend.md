# Inspecciones de ingreso de vehículos — Frontend

**Revisión: 7 de noviembre de 2026.** Este archivo integra el informe anterior y las aclaraciones de seguridad/feedback actuales. El estado de integración en ramas de destino se verifica por separado.

## Descripción

Se incorporó al frontend de BoxService la posibilidad de registrar y consultar el estado en el que ingresa un vehículo al taller.

Cada inspección se encuentra vinculada a un service específico, permitiendo conservar un registro independiente para cada visita del vehículo.

La funcionalidad incluye:

- Nivel de combustible.
- Observaciones generales.
- Checklist del estado del vehículo.
- Fotografías del ingreso.
- Visualización y eliminación de fotografías.

## Ubicación en el sistema

La inspección se encuentra disponible desde:

1. Taller.
2. Selección de un vehículo.
3. Historial de services.
4. Botón **Inspección** del service correspondiente.

## Funcionamiento

### Crear una inspección

Cuando un service todavía no tiene una inspección, se muestra un formulario nuevo.

El usuario puede completar:

- Nivel de combustible entre 0% y 100%.
- Observaciones generales.
- Estado de cada elemento del checklist.
- Observaciones particulares para cada elemento.

Al presionar **Crear inspección**, la información se envía al backend y queda vinculada al service.

### Modificar una inspección

Si el service ya tiene una inspección, el frontend recupera la información guardada.

El usuario puede modificar el combustible, las observaciones o el checklist y guardar los cambios mediante el botón **Guardar cambios**.

### Estados del checklist

Cada elemento admite uno de los siguientes estados:

- `not_checked`: Sin revisar.
- `ok`: Sin daños.
- `damaged`: Con daño.

### Elementos revisados

El checklist incluye:

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

## Fotografías

Una vez creada la inspección, el usuario puede cargar fotografías relacionadas con el ingreso del vehículo.

Cada fotografía contiene:

- Archivo.
- Tipo de fotografía.
- Descripción.
- Nombre original.
- Tipo de contenido.
- Fecha de carga.

### Formatos permitidos

- JPG/JPEG.
- PNG.
- WebP.
- Tamaño máximo: 8 MB.
- Hasta 12 fotografías por inspección, validado por el backend.

### Tipos de fotografía

- Vista general u otra.
- Daño.
- Frente.
- Parte trasera.
- Lateral izquierdo.
- Lateral derecho.
- Tablero.

### Almacenamiento

Los archivos reales se guardan de manera privada en el bucket de Supabase Storage:

```text
vehicle-inspections
```

La base de datos solamente almacena la información descriptiva y la ruta del archivo.

Para visualizar una imagen, el frontend solicita al backend una URL firmada temporal. De esta manera, el bucket puede permanecer privado.

## Relación de datos

```text
Vehículo
└── Service
    └── Inspección
        ├── Elementos del checklist
        └── Fotografías
```

La inspección no se vincula directamente con el vehículo porque un mismo vehículo puede ingresar varias veces al taller. Cada service conserva su propia inspección y fotografías.

## Endpoints utilizados

```http
GET /services/{serviceId}/inspection
POST /services/{serviceId}/inspection
PATCH /services/{serviceId}/inspection
POST /services/{serviceId}/inspection/photos
GET /services/{serviceId}/inspection/photos/{photoId}/url
DELETE /services/{serviceId}/inspection/photos/{photoId}
```

## Archivos modificados

### `web/components/taller/HistoryList.tsx`

- Agrega el botón **Inspección** a cada service del historial.
- Controla la apertura y el cierre del formulario.

### `web/components/taller/ServiceInspectionModal.tsx`

Nuevo componente encargado de:

- Mostrar el formulario de inspección.
- Crear y modificar el registro.
- Administrar el checklist.
- Mostrar el progreso de elementos revisados.
- Subir fotografías.
- Abrir fotografías mediante URL firmada.
- Eliminar fotografías.

### `web/types/entities.ts`

Agrega los tipos TypeScript correspondientes a:

- Inspección.
- Elementos del checklist.
- Estados del checklist.
- Fotografías.
- Solicitudes de creación y modificación.
- Respuesta de URL firmada.

### `web/lib/api/services.ts`

Agrega las funciones para consumir los endpoints de inspecciones y fotografías.

### `web/hooks/useServices.ts`

Agrega hooks de TanStack Query para:

- Consultar una inspección.
- Crear o modificar una inspección.
- Subir fotografías.
- Eliminar fotografías.
- Actualizar automáticamente la información mostrada.

### `web/lib/api/client.ts`

Agrega soporte para enviar formularios `multipart/form-data`, necesario para transferir archivos.

### `web/app/api/proxy/[...path]/route.ts`

Modifica el proxy de Next.js para reenviar correctamente:

- El cuerpo binario de la fotografía.
- El encabezado `Content-Type`.
- El límite multipart.
- El token JWT de autenticación.

## Seguridad

- El navegador no recibe la clave privada de Supabase. Abre las imágenes mediante enlaces firmados solicitados al backend.
- La secret key de Supabase permanece únicamente en el backend.
- Las peticiones pasan por el proxy autenticado de Next.js.
- Las fotografías se visualizan mediante URLs firmadas temporales.
- El bucket permanece privado.

## Pruebas realizadas

Se verificaron correctamente:

- Creación de una inspección.
- Registro de los 13 elementos del checklist.
- Modificación de combustible y observaciones.
- Persistencia al cerrar y volver a abrir la inspección.
- Carga de fotografías PNG.
- Almacenamiento de archivos en Supabase Storage.
- Registro de fotografías en la base de datos.
- Visualización mediante URL firmada.
- Compilación de producción mediante `npm.cmd run build`.

La compilación finalizó correctamente, sin errores de TypeScript.

## Dependencia con el backend

Esta funcionalidad requiere que los endpoints de inspecciones y la integración con Supabase Storage del backend estén incorporados en la rama `develop`.

También requiere las tablas:

```text
service_inspections
service_inspection_items
service_inspection_photos
```

y el bucket privado:

```text
vehicle-inspections
```


## Seguridad y feedback incorporados en noviembre de 2026

El backend comprueba que el service pertenezca al taller autenticado en las seis rutas de inspecciones/fotos. La interfaz muestra “Inspección no encontrada o sin acceso” cuando no puede acceder al service.

Un 404 de inspección no se interpreta automáticamente como permiso para crearla: el modal consulta si el service es accesible. Solo en ese caso ofrece la creación de una inspección nueva. La decisión de permitir una operación sigue estando en el backend.

Las consultas y los botones de guardado, apertura y carga de fotos incorporan indicadores animados y estado ocupado. Se agregaron mensajes comunes y reintentos apropiados. Los estados accesibles usan `role="status"`, `role="alert"` y `aria-busy` según corresponda.

El enlace de una foto tiene una vigencia informada de 900 segundos. Quien ya posee un enlace firmado puede abrirlo mientras esté vigente; el control entre talleres impide obtener uno nuevo desde la API con una cuenta ajena.

En las últimas pruebas se comprobó lectura propia y rechazo de consultas/escrituras ajenas, con los límites indicados en el [informe de cambios](../cambios/2026-11-07-seguridad-facturacion-metricas.md). No se eliminó una foto real en esa ronda.

Ver también [backend, tablas y contratos](backend.md).
