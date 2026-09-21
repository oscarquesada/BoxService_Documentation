# Tecnologías y arquitectura

## 1. Frontend web

- React
- Next.js
- TypeScript

El frontend contiene las interfaces utilizadas por los dueños, empleados y operarios del taller.

## 2. Backend

- ASP.NET Core sobre .NET 8
- API para la comunicación con el frontend y los demás clientes

El backend concentra las reglas de negocio, la autenticación, los permisos, las validaciones y el acceso a los datos.

## 3. Base de datos

- PostgreSQL
- Migraciones para controlar los cambios de estructura

La base de datos almacena la información de talleres, usuarios, clientes, vehículos, presupuestos, services y facturas.

## 4. Aplicación móvil

- React Native
- TypeScript

La aplicación móvil se encuentra planificada y permitirá extender algunas funciones al cliente y al personal del taller.

## 5. Seguridad

- Autenticación mediante JWT/OIDC.
- Autorización basada en roles.
- Manejo seguro de secretos.
- Middlewares de validación y seguridad.
- Auditoría y recuperación de contraseña planificadas.

## 6. Arquitectura multi-tenant

La arquitectura multi-tenant permitirá que varios talleres utilicen la misma plataforma manteniendo sus datos separados. Cada operación debe identificar el taller correspondiente y limitar el acceso según el usuario autenticado.

## 7. Herramientas de trabajo

- Git y GitHub para control de versiones.
- Trello para planificación y seguimiento.
- Vercel para el despliegue del frontend de desarrollo.
- Render para el despliegue del backend de desarrollo.
- CI/CD para automatizar validaciones y despliegues.

Algunas herramientas corresponden a tareas planificadas y pueden modificarse durante el desarrollo.

## 8. Repositorios relacionados

- [BoxService Frontend](https://github.com/maxi-gartner/BoxService_FrontEnd)
- [BoxService Backend](https://github.com/maxi-gartner/BoxService_BackEnd)
- [BoxService Mobile](https://github.com/maxi-gartner/BoxService_Mobile)
