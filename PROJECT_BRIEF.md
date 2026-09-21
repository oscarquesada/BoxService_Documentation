# Project Brief — BoxService

## 1. Nombre del proyecto

**BoxService**

## 2. Descripción

BoxService es una plataforma orientada a la gestión integral de talleres mecánicos. Permite registrar clientes y vehículos, preparar presupuestos, controlar la aprobación de los trabajos, administrar services, documentar el estado del vehículo y generar la facturación correspondiente.

La solución busca reemplazar registros dispersos, tareas manuales y procesos poco organizados por un circuito digital centralizado y fácil de utilizar.

## 3. Problema identificado

Muchos talleres administran la información mediante papel, planillas, mensajes y herramientas separadas. Esto puede generar pérdida de información, dificultad para conocer el estado de un trabajo, errores en presupuestos y facturas, y poco control sobre el historial de cada vehículo.

Además, el cliente suele depender de llamadas o mensajes para conocer el estado de su vehículo o aprobar un presupuesto.

## 4. Objetivo general

Desarrollar un sistema web que permita administrar el circuito completo de atención de un taller mecánico, desde el ingreso del cliente y su vehículo hasta la facturación y el almacenamiento del historial del service.

## 5. Objetivos específicos

- Centralizar los datos de clientes, vehículos y trabajos realizados.
- Digitalizar la creación y aprobación de presupuestos.
- Facilitar el seguimiento de los services.
- Registrar fotografías y una inspección mediante checklist.
- Generar facturas asociadas a los trabajos realizados.
- Conservar el historial de mantenimiento de cada vehículo.
- Administrar usuarios con diferentes roles y permisos.
- Separar la información de cada taller mediante una arquitectura multi-tenant.
- Mejorar la comunicación entre el taller y sus clientes.

## 6. Público objetivo

El sistema está dirigido principalmente a:

- Dueños de talleres mecánicos.
- Empleados y operarios del taller.
- Personal administrativo.
- Clientes que necesitan consultar presupuestos y el estado de sus vehículos.

En una primera etapa, el proyecto apunta a talleres pequeños y medianos que necesitan organizar y digitalizar su operación.

## 7. Integrantes y áreas de trabajo

| Integrante | Área principal de participación |
|---|---|
| Oscar Quesada | Frontend, experiencia de usuario, vistas operativas y módulo de fotografías y checklist |
| Maximiliano Gartner | Integración, revisión de código, migraciones y desarrollo general |
| Leonardo Busch | Autenticación, autorización, roles y seguridad |
| Cristhian Carrasco | Backend, API, base de datos y migración a ASP.NET Core con PostgreSQL |

Estas áreas representan la distribución actual del trabajo y pueden modificarse o compartirse durante los diferentes sprints.

## 8. Roles del sistema

### Superadministrador

Administra la plataforma de manera general y puede gestionar los talleres registrados.

### Dueño del taller

Administra los datos del taller, los empleados, los clientes, los vehículos y el seguimiento general de la operación.

### Empleado u operario

Gestiona las tareas habilitadas para su rol, como consultar vehículos, trabajar sobre un service, completar controles y registrar avances.

### Cliente

En etapas posteriores podrá consultar el estado del vehículo y aprobar o rechazar presupuestos desde un portal o aplicación móvil.

## 9. Alcance

El proyecto contempla:

- Aplicación web para la gestión del taller.
- Autenticación y autorización según roles.
- Gestión de talleres y empleados.
- Gestión de clientes y vehículos.
- Presupuestos y aprobación.
- Gestión y seguimiento de services.
- Inspección mediante fotografías y checklist.
- Facturación e historial del vehículo.
- Separación de datos por taller.
- Preparación de una aplicación móvil y un portal para clientes.

## 10. Entregable principal

Una versión funcional de BoxService que permita ejecutar el flujo principal de un taller de manera completa:

**Cliente y vehículo → Presupuesto → Aprobación → Service → Facturación → Historial**

## 11. Criterios de éxito

- El usuario puede iniciar sesión y acceder únicamente a las funciones correspondientes a su rol.
- Un taller puede registrar clientes y vehículos.
- Se puede crear un presupuesto y registrar su aprobación.
- El presupuesto aprobado puede continuar como service.
- El taller puede documentar el estado del vehículo.
- El service puede finalizarse y facturarse.
- El trabajo realizado queda disponible en el historial del vehículo.
- Los datos de un taller no pueden ser consultados por otro taller.

## 12. Restricciones

- El desarrollo debe completarse dentro del período académico establecido.
- El equipo cuenta con ocho semanas de planificación.
- Algunas integraciones externas pueden depender de servicios de terceros.
- Las funcionalidades planificadas para etapas posteriores estarán sujetas al tiempo disponible.

