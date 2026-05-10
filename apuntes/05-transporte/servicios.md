# Servicios de la capa de transporte

La capa de transporte provee **comunicación lógica entre dos procesos** que corren en distintos hosts. Los protocolos se ejecutan **solamente en las puntas** (end-to-end): los routers intermedios no participan, y no hay manera de bypassear la capa de transporte.

## Rol de cada extremo

- **Emisor**: separa los mensajes de la aplicación en **segmentos** y los entrega al nivel de red.
- **Receptor**: reensambla los segmentos en mensajes y los pasa al nivel de aplicación.

El "canal" por el que una aplicación envía y recibe es el **socket**. La capa de transporte usa el **puerto** del segmento para decidir a qué proceso entregar la información que llega.

## Servicios que ofrecen

| Servicio | TCP | UDP | SCTP |
|---|---|---|---|
| Orientado a conexión | ✓ | ✗ | ✓ |
| Confiable (retransmisión) | ✓ | ✗ | ✓ |
| Control de flujo | ✓ | ✗ | ✓ |
| Control de congestión | ✓ | ✗ | ✓ |
| Preserva límites de mensaje | ✗ | ✓ | ✓ |
| Multi-streaming | ✗ | ✗ | ✓ |
| Multi-homing | ✗ | ✗ | ✓ |

UDP solo ofrece **"mejor esfuerzo"** (best effort), igual que IP.

## Lo que **ningún** protocolo de transporte garantiza

- Mínimo de **demora / latencia**.
- Mínimo de **ancho de banda**.

Esto vale para TCP, UDP, SCTP e IP por igual.

## Aparece en parciales

- "¿Cuál/es de los siguientes protocolos me garantiza un mínimo delay en el envío de paquetes?" → **Ninguno** (preguntas-teoricas, ej. 37).
- "Cuando envía por correo una carta simple, asumiendo que fue recibida por el destinatario. ¿Esto es análogo a TCP o UDP?" → **UDP**, porque no se valida la recepción (guía 4 ej. 1; preguntas-teoricas ej. 38).
- "¿Cuál es el protocolo de transporte que intercambia datagramas sin acuses de recibo ni envío garantizado?" → **UDP** (guía 4 ej. 2).
- "Si el protocolo de una capa del modelo OSI es con conexión, entonces la capa superior también ofrecerá un servicio con conexión." → **Falso**: HTTP sobre TCP es el contraejemplo, HTTP funciona sin conexión (preguntas-teoricas ej. 36).

## Fuente

- Apunte oficial, Unidad 5, p. 51.
- Guía 4 (Transporte), ej. 1, 2, 6.
