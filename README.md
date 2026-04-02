# Trabajo Práctico - Middlewares Orientados a Mensajes

Esta entrega consiste en la implementación de work queue y exchange, componentes de RabbitMQ, utilizando la biblioteca Pika en Python. Las funciones implementadas para interactuar con el middleware son:

`start_consuming(on_message_callback)`

`stop_consuming()`

`send(message)`

`close()`

En la implementación se optó por usar `channel.basic_qos(prefetch_count=1)` para lograr una distribución equitativa de mensajes, al evitar que un worker reciba un mensaje hasta que haya hecho el ACK del anterior, y se envía al siguiente que no está ocupado.

Para la work queue, se usa el exchange por default `exchange=''` para enviar el mensaje directamente a la cola.

Se usó un exchange de tipo direct para enviar el mensaje a las colas cuyas binding keys sean iguales a las routing keys del mensaje.

La implementación considera el manejo de errores, levantando la excepción correspondiente ante problemas de conexión o internos.

## Ejecución

`make up` : Inicia contenedores de RabbitMQ  y de pruebas de integración. Comienza a seguir los logs de las pruebas.

`make down`:   Detiene los contenedores de pruebas y destruye los recursos asociados.

`make logs`: Sigue los logs de todos los contenedores en un solo flujo de salida.

`make local`: Ejecuta las pruebas de integración desde el Host, facilitando el desarrollo. Se explica con mayor detalle dentro de su sección.
