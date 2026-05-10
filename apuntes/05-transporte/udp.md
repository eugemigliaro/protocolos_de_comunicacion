# UDP (User Datagram Protocol)

Protocolo de transporte **no confiable** y **no orientado a conexión**. Transporta datagramas de manera "best effort" entre hosts. Cabecera mínima.

## Formato del segmento

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|       Source Port           |     Destination Port            |
+-------------------------------+-------------------------------+
|         Length                |         Checksum              |
+-------------------------------+-------------------------------+
|                          Data                                 |
+---------------------------------------------------------------+
```

- **Source / Destination Port** (16 bits cada uno).
- **Length**: longitud total del datagrama UDP (header + data).
- **Checksum**: verifica si el mensaje llegó correcto o se corrompió en el trayecto. Es opcional en IPv4, obligatorio en IPv6.

Solo 8 bytes de header.

## Lo que NO ofrece UDP

- ❌ Verificación de software para la **entrega** de segmentos.
- ❌ Reensamblaje de mensajes entrantes.
- ❌ Acuses de recibo (ACK).
- ❌ Control de flujo.
- ❌ Control de congestión.
- ❌ Garantía de **orden** de llegada.
- ❌ **Fragmentación**: si un mensaje no entra en un datagrama, debe fragmentarlo la **aplicación**.

## ¿Cuándo conviene usarlo?

- DNS (queries cortos donde retransmitir desde la app es trivial).
- Streaming en vivo / VoIP / videojuegos (preferimos perder paquetes a esperar retransmisiones).
- DHCP, SNMP, NTP.
- Cuando la app implementa su propia confiabilidad (ej. QUIC encima de UDP).

## Aparece en parciales

- "¿Cuál de las siguientes afirmaciones describe mejor a UDP?" → **"Un protocolo que intercambia datagramas sin acuse de recibo ni garantía de entrega"** (guía 4 ej. 5).
- "Una aplicación que utiliza UDP debe enviar una respuesta que supera el máximo permitido por cada datagrama, ¿se puede? ¿Cómo?" → **Sí**: la aplicación debe **fragmentar** la respuesta en varios datagramas. La fragmentación NO es servicio de UDP (guía 4 ej. 13; preguntas-teoricas ej. 2).
- "Cuando envía por correo una carta simple, asumiendo que fue recibida por el destinatario. Esto es análogo a:" → **UDP** (preguntas-teoricas ej. 38).

## Fuente

- Apunte oficial, Unidad 5, p. 53-54.
- Guía 4 (Transporte), ej. 1, 2, 5, 13.
