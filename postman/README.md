# NovaCommerce Order Management API

**Asignatura:** Integración de Sistemas  
**Taller:** Diseño de una API REST como contrato de integración  
**Versión:** 1.0.0  
**Especificación:** OpenAPI 3.0.3

## 1. Problema de integración

NovaCommerce es una plataforma ficticia de comercio electrónico que necesita comunicarse con un Sistema Empresarial de Gestión de Pedidos.

La integración permite registrar nuevos pedidos, consultar pedidos existentes, recuperar pedidos por cliente y actualizar sus estados sin que la plataforma comercial deba conocer los detalles internos del sistema proveedor.

Para resolver esta necesidad se diseñó un contrato de integración mediante una API REST, utilizando el enfoque *Contract First*.

## 2. Sistemas involucrados

**Sistema consumidor:** Plataforma de Comercio Electrónico NovaCommerce. Se encarga de solicitar operaciones relacionadas con los pedidos de sus clientes.

**Sistema proveedor:** Sistema Empresarial de Gestión de Pedidos. Expone las operaciones REST necesarias para gestionar los pedidos y sus estados.

**Mecanismo de integración:** Comunicación HTTP mediante API REST, utilizando JSON como formato de intercambio.

## 3. Recursos principales

- **Order:** representa un pedido y su información.
- **OrderItem:** representa los productos y cantidades solicitadas.
- **Customer:** representa al cliente que realiza el pedido, identificado mediante `customerId`.
- **Product:** representa un producto referenciado mediante `productId`.

Los recursos Customer y Product se utilizan como referencias dentro del contrato; no se exponen como endpoints independientes en esta versión.

## 4. Endpoints

| Método | Endpoint | Descripción |
|---|---|---|
| POST | `/orders` | Crear un pedido |
| GET | `/orders/{orderId}` | Consultar un pedido específico |
| GET | `/orders?customerId=C-1024` | Consultar pedidos por cliente |
| PATCH | `/orders/{orderId}` | Actualizar parcialmente el estado de un pedido |

## 5. Contrato de datos y errores

La API utiliza estructuras JSON para intercambiar información. Los modelos se definen en `components/schemas`, permitiendo reutilizar los mismos tipos de datos entre diferentes operaciones.

Se documentan respuestas exitosas como `200 OK` y `201 Created`, así como errores `400 Bad Request`, `404 Not Found`, `409 Conflict` y `500 Internal Server Error`.

Los errores siguen un modelo común con los atributos `code`, `message` y el identificador opcional `correlationId`.

## 6. Decisiones de diseño justificadas

**Decisión 1. Utilización de REST y recursos de negocio.**

Se utilizan endpoints orientados a recursos, como `/orders`, en lugar de rutas procedimentales como `/createOrder`. Esto permite expresar las operaciones mediante métodos HTTP estandarizados y mantener una interfaz coherente para el consumidor.

**Decisión 2. Reutilización de schemas mediante OpenAPI.**

Los modelos se centralizaron en `components/schemas` para evitar duplicación de estructuras, mejorar la consistencia del contrato y facilitar futuras modificaciones. Esta decisión favorece la modularidad, la reutilización y la mantenibilidad.

**Decisión 3. Manejo consistente de respuestas y errores HTTP.**

Se seleccionaron códigos HTTP según el resultado de cada operación. Por ejemplo, `201 Created` representa la creación de un pedido y `409 Conflict` permite comunicar conflictos de negocio como inventario insuficiente. El modelo común de errores ayuda al consumidor a interpretar los fallos de forma predecible.

**Decisión 4. Separación entre contrato e implementación.**

Se adoptó el enfoque *Contract First* para especificar las operaciones antes de implementar el servicio. Esto reduce las dependencias entre equipos y permite revisar anticipadamente los requisitos de integración.

## 7. Validación y uso en Postman

La especificación OpenAPI se visualizó en Swagger Editor, donde se identificaron cuatro operaciones REST y cinco schemas reutilizables.

Posteriormente, la especificación se importó en Postman y se prepararon las solicitudes POST, GET y PATCH correspondientes.

Se verificaron las URLs, parámetros, cuerpos JSON, ejemplos de respuestas y la variable `baseUrl`.

El backend no fue implementado, ya que la consigna establece que su desarrollo no es obligatorio. Por tanto, no se reportan pruebas de ejecución real del servicio.

## 8. Archivos entregados

- `openapi.yaml`: especificación formal del contrato API REST.
- `postman/API.postman_collection.json`: colección de solicitudes y ejemplos importados en Postman.
- `README.md`: documentación del escenario de integración y de las decisiones de diseño.

## 9. Reflexión final

**¿Qué ventaja obtiene el sistema consumidor al disponer de un contrato OpenAPI antes de que el proveedor haya terminado de implementar la API?**

Disponer anticipadamente de un contrato OpenAPI permite que el sistema consumidor conozca las operaciones disponibles, los parámetros requeridos, las estructuras de los mensajes y las posibles respuestas sin depender de que el proveedor haya finalizado su implementación.

Esto facilita el desarrollo paralelo de ambos sistemas, reduce malentendidos entre equipos y permite detectar inconsistencias de integración desde las primeras etapas del proyecto.

En el caso de NovaCommerce, la plataforma comercial puede preparar sus solicitudes y adaptar su lógica de consumo utilizando el contrato documentado, sin conocer la base de datos ni los detalles internos del Sistema Empresarial de Gestión de Pedidos.
