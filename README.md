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

## Contratos de código y plan de ejecución — auditoría 2026-10-14

**No existe todavía aplicación Flutter compilable en este repositorio.** Su README inicial no significa que el flujo de entrega comercial se haya migrado.

### Fuente que se puede aprovechar, sin copiar toda la app

- `Expressdelivery/lib/video_style_home.dart`: UI mapa/oferta del conductor; extraer únicamente interacción de pedidos comerciales.
- `Expressdelivery/lib/services/express_service.dart`: servicios/rutas RPC; desacoplar del estado Taxi/Envíos.
- `Expressdelivery/lib/push_notifications_native.dart` y `supabase/functions/express-push-dispatch/index.ts`: rediseñar tokens y destino **por aplicación**; no usar registro FCM de Taxi como si fuera Repartidor.
- `Expressdelivery/lib/core/driver_work_scope.dart`: el conductor Principal ya filtra `ride/parcel` (PR #235 fusionada); Repartidor debe admitir **solo** pedidos de comercio `marketplace_order_id != null`, nunca parcelas de `delivery_requests` sin pedido comercial.
- `Expressdelivery/lib/marketplace_checkout_page.dart` y `lib/express_delivery_v2_page.dart`: pertenecen a **cliente** y permanecen en Principal.
- `Adminexpress/lib/admin_delivery_unified.dart` y `admin_marketplace.dart`: panel global de configuración/monitoreo; no trasladar permisos administrativos a esta app.

### Recorrido y pantallas objetivo

1. Acceso/registro del repartidor con identidad, vehículo, zona, disponibilidad y permisos exigidos realmente por backend.
2. Inicio con **mapa** y estado online/offline. Listado de ofertas comerciales con temporizador real, comercio, recogida/destino, ruta, distancia, pago/ganancia con moneda autoritativa.
3. Oferta entrante → aceptar/rechazar (sin doble asignación) → mapa al comercio.
4. Pedido listo y recogida validada → ruta hacia cliente → contacto seguro → confirmación/código de entrega → finalizado/cancelado.
5. Historial, ganancias, ajustes, perfil, ayuda y notificaciones de ofertas (foreground/background).
6. Adaptar estilo azul Express, panel compacto y mapa visible, conforme al mockup de Delivery; no copiar botones de Taxi/PIN de abordaje.

### Barreras obligatorias antes de reemplazar legacy

- Registrar token FCM por app/instalación/sesión/rol y canal; suscribir/borrar al cambiar cuenta y no despertar el conductor Principal con una orden de restaurante.
- Despacho comercial autoritativo en Supabase con RLS/RPC: `marketplace_order_id` real, repartidor autorizado, zona/vehículo compatibles, pedido abierto, expiración, exclusión de autoaceptación no solicitada.
- Backend mantiene lectura/seguimiento de pedidos activos y compatibilidad con APK anteriores. **No apagar la ruta antigua antes de tener receptores nuevos validados y plan de reversión.**
- Dos actores/roles + Admin, pruebas de push con Android en segundo plano, ofertas caducadas, usuario desconectado, cancelación, ubicación/GPS, offline, tarifas CLP/BOB, Preview/Producción sin mezcla.
- Una PR verde de Flutter NO constituye QA E2E ni autorización de Google Play.

**Decisión definitiva:** Express Principal conserva cinco recorridos cliente/conductor Taxi, Envíos y Delivery compras; solamente las nuevas entregas comerciales se mueven. Referencia maestra en `Expressdelivery/docs/EXPRESS_CODE_AUDIT_AND_FOUR_APP_PLAN_20261014.md`.
