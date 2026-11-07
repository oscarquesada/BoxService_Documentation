# Antecedente histórico: cambios en Login y Taller

> Este documento se conserva como registro de una etapa anterior. Las referencias a login por correo, credenciales mock, autenticación pendiente y fotos/checklist por implementar **no describen el estado actual**. Hoy el personal ingresa con usuario/contraseña y JWT del backend; inspecciones y fotos tienen interfaz web. Para el estado vigente ver [MVP](../../MVP.md), [Funcionamiento](../../FUNCIONAMIENTO.md) y [cambios recientes](../cambios/2026-11-07-seguridad-facturacion-metricas.md).

La fecha original de este informe no se reconstruyó. Su contenido histórico se conserva a continuación.

---

# BoxServices - Cambios en Login y módulo Taller

## 1. Objetivo

Este documento registra las mejoras realizadas en la pantalla de inicio de sesión y en el módulo Taller del frontend de BoxServices. Los cambios fueron desarrollados en Next.js con React y TypeScript y se incorporaron mediante un Pull Request desde la rama `dev-oscar` hacia `develop`.

## 2. Alcance

La modificación comprendió los siguientes archivos:

- `web/app/(auth)/login/page.tsx`
- `web/app/(dashboard)/taller/page.tsx`
- `web/components/taller/VehicleOverview.tsx`
- `web/components/taller/VehicleWorkspaceHeader.tsx`
- `web/components/ui/Input.tsx`

No se modificó la lógica del backend dentro de este cambio.

## 3. Cambios en la pantalla de inicio de sesión

### 3.1. Rediseño visual

Se rediseñó la pantalla para mejorar la jerarquía visual y aprovechar mejor el espacio disponible. La nueva estructura separa la identidad de BoxServices del formulario de acceso y mantiene un diseño adaptable para diferentes tamaños de pantalla.

Principales mejoras:

- Panel de identidad con el nombre de BoxService.
- Formulario de acceso más amplio y legible.
- Distribución en columnas para pantallas medianas y grandes.
- Conservación del diseño adaptable en pantallas pequeñas.
- Mejor separación entre títulos, campos, acciones y mensajes.

### 3.2. Validación del formulario

El formulario utiliza `react-hook-form` y `zod` para validar los datos antes de intentar iniciar sesión.

Validaciones implementadas:

- El correo electrónico es obligatorio.
- El correo debe tener un formato válido.
- La contraseña es obligatoria.
- Los errores se muestran junto al campo correspondiente.
- El botón indica el estado `Ingresando...` mientras se procesa la solicitud.

### 3.3. Visualización de la contraseña

Se incorporó un botón para alternar entre mostrar y ocultar la contraseña. Esta acción solo modifica la visualización del campo y no altera su valor.

### 3.4. Recuperación de contraseña

Se agregó la opción visual `¿Olvidaste tu contraseña?`. La recuperación todavía no está conectada a un flujo real; al utilizarla se informa que la funcionalidad aún no se encuentra disponible.

Esta opción deberá vincularse posteriormente con la pantalla o endpoint de recuperación de contraseña.

### 3.5. Accesibilidad y cursores

Se mejoró la interacción con los controles:

- Los textos de correo y contraseña se vincularon con sus campos mediante `htmlFor` e `id`.
- Al seleccionar un label, el navegador enfoca el campo correspondiente.
- Los elementos interactivos utilizan cursor de puntero.
- Los campos y labels de escritura utilizan cursor de texto.
- El botón deshabilitado utiliza un cursor que comunica que la acción no está disponible.

Para soportar estas propiedades, el componente reutilizable `Label` fue actualizado para aceptar los atributos normales de una etiqueta HTML `label`, incluyendo `htmlFor` y `className`.

### 3.6. Manejo de errores

Los errores devueltos por el cliente de la API se muestran dentro de un componente de alerta. Si ocurre un error no identificado, se presenta un mensaje general para evitar mostrar detalles técnicos al usuario.

### 3.7. Estado actual del login

En el momento de realizar estos cambios, el login utilizaba credenciales simuladas para desarrollo. La interfaz quedó preparada para conectarse posteriormente con la autenticación real del backend.

Queda pendiente:

- Reemplazar el mock por el endpoint real de autenticación.
- Recibir y validar el JWT.
- Aplicar permisos según el rol del usuario.
- Implementar la recuperación real de contraseña.

## 4. Cambios en el módulo Taller

### 4.1. Espacio de trabajo por vehículo

El módulo Taller fue reorganizado para trabajar sobre un vehículo seleccionado. El flujo principal es:

1. Buscar y seleccionar un vehículo.
2. Mostrar su información principal.
3. Consultar el resumen, los presupuestos, el historial y la facturación.
4. Crear un presupuesto o registrar un service sin perder el vehículo seleccionado.

### 4.2. Encabezado del vehículo

Se creó el componente `VehicleWorkspaceHeader`, que muestra:

- Marca y modelo del vehículo.
- Patente.
- Cliente relacionado.
- Kilometraje actual.
- Acción `Nuevo presupuesto`.
- Acción `Registrar service`.

Este encabezado mantiene visible el contexto del vehículo mientras el operario navega entre las diferentes secciones.

### 4.3. Vista Resumen

Se creó el componente `VehicleOverview` para presentar un resumen operativo del vehículo.

Incluye:

- Cantidad de presupuestos pendientes.
- Cantidad de services realizados.
- Cantidad de facturas pendientes de cobro.
- Actividad reciente del vehículo.
- Información del próximo mantenimiento.
- Accesos directos al historial, presupuestos y registro de service.

Cuando el vehículo todavía no posee actividad, la pantalla muestra acciones para registrar su primer presupuesto o service.

### 4.4. Navegación por pestañas

Se organizaron las siguientes secciones:

- Resumen.
- Presupuestos.
- Historial.
- Facturación.

Las pestañas se acomodan en más de una fila cuando no existe suficiente espacio, evitando una barra de desplazamiento horizontal innecesaria.

### 4.5. Creación de presupuestos y services

Desde el encabezado se puede acceder directamente a:

- `Nuevo presupuesto`.
- `Registrar service`.

El vehículo seleccionado se conserva al entrar en estas vistas, por lo que el usuario no necesita buscarlo nuevamente. Al finalizar o regresar, puede continuar trabajando sobre el mismo vehículo.

### 4.6. Cambio de vehículo

Se agregó la opción `Cambiar vehículo`, que permite volver a la búsqueda cuando el operario necesita comenzar la atención de otra unidad.

### 4.7. Acceso directo mediante URL

El módulo admite un enlace con el identificador del vehículo:

```text
/taller?id=123
```

Si el vehículo existe, se selecciona automáticamente y se abre su resumen. Esto permite acceder a la ficha desde otras partes del sistema.

### 4.8. Actualización de información

Después de crear o modificar información, se invalidan las consultas almacenadas para solicitar nuevamente los datos y reflejar los cambios en pantalla.

## 5. Decisiones de diseño

- Se mantuvo la lógica de comunicación con la API fuera de las páginas, utilizando funciones y hooks reutilizables.
- Se dividió la interfaz del Taller en componentes específicos para facilitar su mantenimiento.
- Se conservó el vehículo seleccionado durante el flujo operativo.
- Se priorizó una navegación rápida para el operario del taller.
- Las validaciones visibles en el frontend mejoran la experiencia, pero las reglas definitivas deberán validarse también en el backend.

## 6. Validaciones realizadas

- Revisión visual del login y del módulo Taller en ejecución local.
- Prueba de selección y cambio de vehículo.
- Prueba de navegación entre las pestañas.
- Prueba de acceso a las vistas de nuevo presupuesto y nuevo service.
- Revisión de tipos de TypeScript mediante `tsc --noEmit`.
- Corrección del componente `Label` para admitir atributos HTML válidos sin errores de TypeScript.

## 7. Resultado

Los cambios mejoran la usabilidad del inicio de sesión y convierten la pantalla Taller en un espacio de trabajo centralizado por vehículo. La interfaz queda preparada para conectarse con la autenticación JWT, los roles de usuario y los endpoints definitivos del backend.

## 8. Próximos pasos

- Conectar el login con la autenticación JWT del backend.
- Aplicar la autorización según los roles `SuperAdmin`, `Owner` y `Employee`.
- Implementar el flujo real de recuperación de contraseña.
- Integrar fotos y checklist del estado de ingreso del vehículo.
- Confirmar que todos los endpoints respeten la separación de información entre talleres.
