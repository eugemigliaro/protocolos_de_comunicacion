# Protocolos

Un **protocolo** es un sistema formal de reglas de comunicación. Define qué se manda, en qué formato, en qué orden, y qué hacer ante errores. Los protocolos se clasifican según el servicio que ofrecen a la capa superior.

## Clasificación

### Orientado a conexión vs. no orientado a conexión

- **Orientado a conexión**: antes de mandar información hay que **establecer una conexión** con el otro host. Esto agrega un tiempo de espera previo (handshake).
- **No orientado a conexión**: se envía la información directamente, sin handshake. No hay tiempo de setup.

### Confiable vs. no confiable

- **Confiable**: se **verifica** que la información haya llegado a destino. Importante: no asegura que llegue, sino que avisa si llegó o no (ACK / NACK / timeout).
- **No confiable**: no se verifica nada.

### Tabla con ejemplos del mundo real

| Conexión | Confiabilidad | Ejemplo |
|----------|---------------|---------|
| No orientado a conexión | No confiable | Paloma mensajera |
| No orientado a conexión | Confiable | Carta documento (papel firmado de entrega) |
| Orientado a conexión | No confiable | Llamada telefónica (no se reenvía si hay ruido) |
| Orientado a conexión | Confiable | Transacción con tarjeta de crédito |

### Cuándo conviene cada uno

- **Streaming en vivo / videollamada**: no orientado a conexión y no confiable. Si se pierde un frame, retransmitir solo retrasa lo que viene después.
- **Transferencia de archivo**: orientado a conexión y confiable.

## Estandarización

Los estándares de Internet los desarrolla la **IETF** (Internet Engineering Task Force). Los protocolos se publican como **RFC** (Request For Comments). Son **públicos** y no pertenecen a nadie.

## Aparece en parciales

- **Ej 4 (Guía 1)**: "Un protocolo confiable es aquel que asegura que los datos lleguen correctamente a destino." → **FALSO**. No asegura, **verifica**.
- **Ej 5 (Guía 1)**: "Si una aplicación usa transporte no orientado a conexión, puede recibir datos en orden distinto al enviado." → **VERDADERO**.
- **Ej 37 (preguntas)**: "¿Cuál protocolo me garantiza un mínimo delay?" → **Ninguno**. Ningún protocolo de transporte/red garantiza delay mínimo (eso depende de la red).
- **Ej 38 (preguntas)**: "Carta simple recibida por destinatario, ¿análogo a TCP o UDP?" → **UDP** (se asume que llegó pero no se valida).
- **Ej 36 (preguntas)**: HTTP sobre TCP es ejemplo clásico de capa superior sin conexión sobre capa inferior con conexión.

## Referencias

- PDF, Unidad 1, p. 7-8.
- Guía 1, Ej 2 (puntos 4 y 5).
