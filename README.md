# Express Repartidor

Aplicación independiente de **reparto de pedidos comerciales** de Express.

## Función exclusiva

Recibir, aceptar, recoger y entregar pedidos de restaurantes, supermercados, farmacias y otras tiendas generados por clientes del módulo **Delivery** de la aplicación principal Express.

**NO recibe viajes Taxi ni solicitudes de Envíos/Paquetería entre personas.** Ambos servicios siguen en el modo conductor de `jorge2610g/Expressdelivery`.

## Arquitectura confirmada (14 de octubre de 2026)

- **App Express principal:** cliente Taxi; cliente Envíos; cliente compras Delivery/restaurantes; conductor Taxi; conductor Envíos.
- **App Express Repartidor (este repositorio):** exclusivamente pedidos comerciales (`market`, registros con `marketplace_order_id`).
- **App Express Restaurante:** gestión de negocios en `jorge2610g/Express_Restaurante`.
- **Express Admin:** panel independiente `jorge2610g/Adminexpress`.
- Backend: proyecto Supabase compartido de Express, sin duplicar base de datos. Mantener aislamiento de identidad, zona, rol y entornos Preview/Producción.

## Seguridad y despliegue

La app tendrá su propio package ID, autenticación/roles y registro de notificaciones por **aplicación**. El backend debe validar autorización comercial y entorno antes de ofrecer o asignar un pedido. No reutilizar indiscriminadamente los tokens push de Taxi o Envíos. No almacenar secretos privados ni credenciales de firma en el repositorio.

## Estado

**Repositorio inicializado; app Flutter de Repartidor aún no migrada ni compilada.**

En `jorge2610g/Expressdelivery`, la rama `refactor/primary-driver-taxi-parcel-only-20261014` y [PR #235](https://github.com/jorge2610g/Expressdelivery/pull/235) preparan la exclusión de nuevas ofertas comerciales del modo conductor principal. **PR en borrador, sin publicación**.

La siguiente fase tras validar esa separación es implementar aquí la app Flutter, reutilizando de forma segura ofertas, mapas, aceptación, recogida, confirmación y ganancias de Marketplace con los RPCs existentes, y validar notificaciones, asignación y permisos E2E antes de publicar.
