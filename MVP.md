# Producto Mínimo Viable (MVP)

## 1. Definición

El MVP de BoxService es la versión mínima del sistema que permite completar el circuito principal de trabajo de un taller mecánico de principio a fin.

No es necesario que incluya todas las mejoras futuras, pero sí debe permitir registrar el ingreso de un vehículo, presupuestar el trabajo, realizar el service, facturarlo y conservar su historial.

## 2. Funcionalidades indispensables

### Acceso y seguridad

- Inicio de sesión.
- Autenticación mediante JWT/OIDC.
- Roles de superadministrador, dueño y empleado.
- Validación de permisos según el rol.
- Manejo seguro de credenciales y secretos.

### Talleres y empleados

- Alta y administración de talleres.
- Alta y gestión de empleados.
- Asociación de usuarios con el taller correspondiente.
- Separación de datos entre talleres.

### Clientes y vehículos

- Alta, consulta y modificación de clientes.
- Alta, consulta y modificación de vehículos.
- Asociación de uno o más vehículos con un cliente.
- Consulta del historial de cada vehículo.

### Presupuestos

- Creación de presupuestos.
- Registro de servicios, repuestos e importes.
- Consulta del estado del presupuesto.
- Aprobación o rechazo del presupuesto.

### Services

- Creación de un service a partir de un presupuesto aprobado.
- Registro del estado del trabajo.
- Vista operativa para el taller.
- Registro de observaciones, tareas y validaciones.
- Carga de fotografías del vehículo.
- Inspección mediante checklist.

### Facturación e historial

- Generación de una factura vinculada al service.
- Registro del cierre del trabajo.
- Almacenamiento del service en el historial del vehículo.

## 3. Flujo cubierto por el MVP

1. Un usuario autorizado inicia sesión.
2. Registra o selecciona un cliente.
3. Registra o selecciona el vehículo del cliente.
4. Crea un presupuesto con servicios y repuestos.
5. El presupuesto se aprueba o rechaza.
6. Si se aprueba, se genera el service.
7. El taller realiza el trabajo y registra su avance.
8. Se agregan fotografías, controles y observaciones.
9. El service se finaliza y se genera la factura.
10. La información queda guardada en el historial del vehículo.

## 4. Funcionalidades posteriores al MVP

Las siguientes funciones aportan valor, pero pueden incorporarse después de completar y validar el circuito principal:

- Aplicación móvil desarrollada con React Native y TypeScript.
- Portal del cliente para consultar estados y aprobar presupuestos.
- Notificaciones mediante WhatsApp o SMS.
- Recordatorios automáticos de próximos services.
- Pagos y control de stock de repuestos.
- Dashboard avanzado para el dueño del taller.
- Código QR asociado al vehículo.
- Agenda y reserva de turnos.
- Auditoría avanzada y recuperación de contraseña.

## 5. Fuera del alcance inicial

- Integraciones contables o fiscales completas.
- Cobros reales mediante múltiples pasarelas de pago.
- Gestión logística avanzada de proveedores.
- Automatizaciones con inteligencia artificial.
- Soporte para operaciones de grandes concesionarias.

Estas funciones pueden evaluarse como futuras ampliaciones sin comprometer la entrega del MVP.

