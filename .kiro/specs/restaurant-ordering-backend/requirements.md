# Requirements Document

## Introduction

Este documento define los requerimientos para el backend del sistema de pedidos para restaurantes. El sistema proporcionará una API REST que permitirá a los clientes realizar pedidos desde sus mesas mediante códigos QR, con validación de IP Wi-Fi del restaurante. Aunque inicialmente se desarrollará solo para español, la estructura de base de datos estará preparada para soportar múltiples idiomas en el futuro. El backend incluirá autenticación JWT, gestión de roles, y actualizaciones en tiempo real mediante WebSockets. El frontend será desarrollado por separado en otro repositorio.

## Requirements

### Requirement 1

**User Story:** Como cliente del restaurante, quiero ver el menú del restaurante a través de la API, para que pueda revisar los platos disponibles.

#### Acceptance Criteria

1. WHEN un cliente solicita el menú THEN el sistema SHALL devolver todas las categorías y elementos del menú en español
2. WHEN se solicita GET /restaurants/:id/menu THEN el sistema SHALL devolver la estructura completa del menú con categorías y platos
3. WHEN la base de datos tenga estructura multi-idioma THEN el sistema SHALL estar preparado para devolver traducciones en el futuro
4. WHEN se solicita el menú THEN el sistema SHALL incluir información de precios, descripciones y disponibilidad

### Requirement 2

**User Story:** Como cliente del restaurante, quiero poder realizar pedidos solo cuando esté conectado al Wi-Fi del restaurante, para que el sistema garantice que estoy físicamente presente en el establecimiento.

#### Acceptance Criteria

1. WHEN un cliente intenta realizar un pedido THEN el sistema SHALL validar que la IP del cliente esté en la lista de IPs autorizadas del restaurante
2. IF la IP del cliente no está autorizada THEN el sistema SHALL rechazar el pedido con un mensaje de error apropiado
3. WHEN se recibe un pedido THEN el sistema SHALL verificar la IP tanto en el frontend como en el backend para evitar manipulaciones
4. WHEN se configura un restaurante THEN el sistema SHALL permitir gestionar la lista de IPs públicas autorizadas

### Requirement 3

**User Story:** Como encargado del restaurante, quiero gestionar las mesas y generar códigos QR únicos, para que los clientes puedan acceder al menú desde cada mesa específica.

#### Acceptance Criteria

1. WHEN el encargado crea una nueva mesa THEN el sistema SHALL generar un token único para esa mesa
2. WHEN se solicita el código QR de una mesa THEN el sistema SHALL generar un QR que contenga la URL con el token de la mesa
3. WHEN un cliente escanea un QR THEN el sistema SHALL asociar los pedidos posteriores con esa mesa específica
4. WHEN el encargado consulta las mesas THEN el sistema SHALL mostrar el estado actual de cada mesa (libre, ocupada, esperando pedido, etc.)

### Requirement 4

**User Story:** Como empleado del restaurante (cocina/encargado), quiero recibir notificaciones en tiempo real de nuevos pedidos y cambios de estado, para que pueda responder rápidamente a las necesidades de los clientes.

#### Acceptance Criteria

1. WHEN se crea un nuevo pedido THEN el sistema SHALL emitir una notificación WebSocket a todos los empleados conectados
2. WHEN cambia el estado de un pedido THEN el sistema SHALL notificar el cambio a través de WebSocket
3. WHEN un empleado se conecta THEN el sistema SHALL enviar el estado actual de todos los pedidos pendientes
4. WHEN se actualiza el estado de una mesa THEN el sistema SHALL emitir la actualización a todos los clientes conectados

### Requirement 5

**User Story:** Como administrador del sistema, quiero gestionar usuarios con diferentes roles y permisos, para que cada empleado tenga acceso solo a las funcionalidades que necesita.

#### Acceptance Criteria

1. WHEN un usuario intenta acceder al sistema THEN el sistema SHALL autenticar usando JWT
2. WHEN se autentica un usuario THEN el sistema SHALL incluir los roles y permisos en el token JWT
3. IF un usuario intenta acceder a una funcionalidad sin permisos THEN el sistema SHALL denegar el acceso con código 403
4. WHEN se crea un usuario THEN el sistema SHALL asignar roles específicos (encargado, cocina, administrador)

### Requirement 6

**User Story:** Como desarrollador, quiero que el sistema tenga una base de datos bien estructurada para soporte multi-idioma, para que sea fácil agregar nuevos idiomas y mantener las traducciones.

#### Acceptance Criteria

1. WHEN se agrega un nuevo elemento al menú THEN el sistema SHALL permitir crear traducciones para múltiples idiomas
2. WHEN se consulta un elemento del menú THEN el sistema SHALL devolver la traducción correspondiente al idioma solicitado
3. WHEN no existe traducción para un idioma THEN el sistema SHALL usar la traducción en español como fallback
4. WHEN se agregan nuevos idiomas THEN el sistema SHALL mantener la estructura de traducciones existente

### Requirement 7

**User Story:** Como cliente del restaurante, quiero realizar pedidos de manera intuitiva seleccionando elementos del menú, para que pueda completar mi orden fácilmente.

#### Acceptance Criteria

1. WHEN un cliente selecciona elementos del menú THEN el sistema SHALL permitir especificar cantidades y observaciones
2. WHEN se envía un pedido THEN el sistema SHALL crear el registro del pedido con todos los elementos seleccionados
3. WHEN se crea un pedido THEN el sistema SHALL asignar un estado inicial "pendiente" y asociarlo con la mesa correspondiente
4. WHEN se confirma un pedido THEN el sistema SHALL generar un número de orden único para seguimiento

### Requirement 8

**User Story:** Como empleado de cocina, quiero ver todos los pedidos pendientes organizados por prioridad y tiempo, para que pueda gestionar eficientemente la preparación de los platos.

#### Acceptance Criteria

1. WHEN la cocina consulta pedidos THEN el sistema SHALL devolver todos los pedidos con estado "pendiente" o "en preparación"
2. WHEN se actualiza el estado de un pedido THEN el sistema SHALL permitir cambiar entre estados (pendiente, en preparación, listo, entregado)
3. WHEN se consultan los detalles de un pedido THEN el sistema SHALL mostrar todos los elementos, cantidades y observaciones especiales
4. WHEN se marcan elementos como listos THEN el sistema SHALL actualizar el estado parcial del pedido