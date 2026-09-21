# Funcionamiento del sistema

## 1. Acceso

El usuario inicia sesión en BoxService. El sistema valida su identidad y determina las opciones disponibles según su rol: superadministrador, dueño del taller o empleado.

## 2. Registro del cliente

Cuando una persona lleva un vehículo al taller, el usuario busca si ya existe como cliente. Si no existe, registra sus datos personales y de contacto.

## 3. Registro del vehículo

El vehículo se registra y se asocia con su propietario. Se almacenan los datos necesarios para identificarlo y consultar posteriormente sus trabajos realizados.

## 4. Presupuesto

El taller crea un presupuesto con los servicios, tareas y repuestos necesarios. También se registran los importes y las observaciones correspondientes.

El presupuesto queda pendiente hasta que el cliente lo aprueba o rechaza. En una primera versión, el taller puede registrar esta decisión. Posteriormente, el cliente podrá responder desde el portal o la aplicación móvil.

## 5. Creación del service

Cuando el presupuesto es aprobado, el sistema permite generar el service. De esta manera se evita volver a cargar la información y se mantiene la relación entre el presupuesto y el trabajo realizado.

## 6. Inspección y seguimiento

Durante el ingreso o la reparación, el operario puede:

- Completar un checklist del estado del vehículo.
- Cargar fotografías.
- Registrar observaciones.
- Consultar las tareas incluidas en el service.
- Actualizar el estado del trabajo.
- Realizar las validaciones necesarias.

Esta información permite documentar cómo ingresó el vehículo y qué tareas se realizaron.

## 7. Finalización y facturación

Una vez completados los trabajos, el service se marca como finalizado. A partir de la información registrada se genera la factura asociada.

## 8. Historial del vehículo

El trabajo finalizado queda guardado en el historial del vehículo. Esto permite consultar services anteriores, reparaciones, observaciones, fotografías y otros datos relevantes.

## 9. Separación por taller

BoxService utiliza una arquitectura multi-tenant. Cada taller trabaja con sus propios clientes, vehículos, empleados, presupuestos y services. La información de un taller no debe ser visible para los demás.

## 10. Flujo resumido

```text
Inicio de sesión
      ↓
Cliente y vehículo
      ↓
Presupuesto
      ↓
Aprobación o rechazo
      ↓
Service e inspección
      ↓
Finalización y factura
      ↓
Historial del vehículo
```

