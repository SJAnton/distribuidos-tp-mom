# Trabajo Práctico - Middlewares Orientados a Mensajes

## Descripción

Esta entrega consiste en la implementación de una capa de abstracción para el envío y recepción de mensajes mediante una work queue y un exchange en RabbitMQ, utilizando la biblioteca Pika en Python.

Se utiliza un exchange de tipo `direct`, donde para este caso se asignó las múltiples routing keys a una única cola.

La implementación considera el manejo de errores, levantando la excepción correspondiente ante problemas de conexión `pika.exceptions.AMQPConnectionError` o internos, que son tratados como un `Exception` genérico.

Las funciones implementadas para interactuar con el middleware son:

### start_consuming(on_message_callback)

En la implementación se optó por usar `channel.basic_qos(prefetch_count=1)` para lograr una distribución equitativa de mensajes, al evitar que un worker reciba un mensaje hasta que haya hecho el ACK del anterior, y se envía al siguiente que no está ocupado.

Dentro de la función se encuentra un wrapper `callback` con la firma requerida para poder ejecutar la función `on_message_callback` con los parámetros que requiere. En esta implementación, el mensaje es enviado de nuevo  a la queue si el consumidor envía un NACK al broker, mediante `requeue=True`.

### stop_consuming()

En ambos casos se busca hacer un graceful shutdown, mediante un `basic_cancel()` para que el consumidor no reciba más mensajes, y `stop_consuming()`, para salir del loop generado por la función bloqueante `start_consuming()`.

### send(message)

Para la work queue, se usa el exchange por default `exchange=''` para enviar el mensaje directamente a la cola.

En el exchange de esta entrega se envía el mensaje para cada routing key.

### close()

Para tanto la queue como el exchange, se cierra primero el canal y después la conexión. Se observó un error `shutdown_error` de tipo `noproc` en los logs, que sucede cuando un supervisor intenta cerrar un canal que ya fue cerrado. Se asume que este error no tiene ningún efecto. ([Fuente](https://github.com/rabbitmq/rabbitmq-server/issues/2124#issuecomment-536414087))

## Ejecución

`make up` : Inicia contenedores de RabbitMQ  y de pruebas de integración. Comienza a seguir los logs de las pruebas.

`make down`:   Detiene los contenedores de pruebas y destruye los recursos asociados.

`make logs`: Sigue los logs de todos los contenedores en un solo flujo de salida.

`make local`: Ejecuta las pruebas de integración desde el Host, facilitando el desarrollo. Se explica con mayor detalle dentro de su sección.
