# Sockets Bloqueantes

Una operación bloqueante es aquella en la que el proceso que la ejecuta queda detenido hasta que la operación se completa. Por ejemplo, una lectura de red con un socket bloqueante deja al programa esperando hasta que llegan los datos.

## Cliente vs servidor

- **Cliente:** se puede dar el lujo de bloquearse esperando un recurso (ej. respuesta del servidor).
- **Servidor:** no debería bloquearse, porque atiende a varios clientes en simultáneo. Si un servidor se bloquea esperando datos de un cliente, deja de atender al resto.

## Comunicación entre procesos

- En el **mismo host**: la define el sistema operativo (ej. pipes, IPC).
- En **distintos hosts**: por intercambio de mensajes a través de la red.

Si tenemos un protocolo de aplicación, le pedimos servicios a transporte. No importa si el otro proceso corre en otra computadora o en la misma, pero sí necesitamos algún concepto de red porque podemos saturarla o tener pérdidas según las elecciones que hagamos.

## Identificación de procesos

- **Proceso cliente:** el que inicia la conexión.
- **Proceso servidor:** el que espera conexiones.
- Cada host se identifica por su **IP**.
- Cada proceso dentro del host se identifica por el **número de puerto**.
- El puerto del **servidor debe ser conocido** (públicamente acordado).
- El puerto del **cliente puede no especificarse**: el SO le asigna uno libre (puerto efímero).

## Qué define el protocolo de aplicación

- Tipos de mensaje (binario o de texto).
- Sintaxis de los mensajes.
- Semántica de los mensajes.
- Reacción frente a errores: lo más simple y no ambigua posible.

## Operaciones básicas que sugiere TCP/IP para una API

- Reservar recursos locales (buffers de entrada/salida).
- Especificar endpoints origen y destino (IP + puerto).
- Iniciar una conexión (cliente).
- Enviar un datagrama (cliente).
- Esperar una conexión (servidor).
- Enviar y recibir datos.
- Determinar cuándo arriba información.
- Generar y manejar datos urgentes.

## APIs aceptadas para TCP/IP

- **Berkeley sockets** (UNIX) — casi el estándar de facto.
- **Windows Sockets** (Microsoft).
- **Transport Layer Interface (TLI)** — AT&T, para System V UNIX.

Berkeley se diseñó en los 80 reusando system calls existentes y soporta múltiples familias de protocolos, no solo TCP/IP. El usuario indica la familia (ej. TCP/IP) y el tipo de servicio (con o sin conexión).

## Fuente

Apunte oficial Unidad 11, p. 132-133.
