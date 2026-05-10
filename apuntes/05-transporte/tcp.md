# TCP (Transmission Control Protocol)

Protocolo de transporte **orientado a conexión** y **confiable**. Ofrece un **circuito virtual** entre dos aplicaciones de usuario final. Divide los mensajes salientes en segmentos, los reensambla en el destino, retransmite lo que no se recibió, y aplica control de flujo (RFC 7323) y control de congestión (RFC 5681).

## Formato del segmento

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|       Source Port             |     Destination Port          |
+-------------------------------+-------------------------------+
|                    Sequence Number                            |
+---------------------------------------------------------------+
|                 Acknowledgment Number                         |
+-------+-----+-----------------+-------------------------------+
|  HLen |  Rsv| U A P R S F     |          Window               |
|       |     | R C S S Y I     |                               |
|       |     | G K H T N N     |                               |
+-------+-----+-----------------+-------------------------------+
|         Checksum              |       Urgent Pointer          |
+-------------------------------+-------------------------------+
|                  Options + Padding                            |
+---------------------------------------------------------------+
|                          Data                                 |
+---------------------------------------------------------------+
```

### Campos clave

- **Sequence Number**: numera los datos en cuanto al **desplazamiento de bytes**. Cada segmento tiene `seq = seq_anterior + bytes_de_data_anterior`.
- **Acknowledgment Number**: número de secuencia hasta el cual el receptor **espera recibir** (es decir, "el próximo byte que espero"). ACK acumulativo.
- **Window**: bytes libres en el buffer de entrada del receptor → control de flujo (`control-de-flujo.md`).
- **Flags**:
  - **URG**: indica datos urgentes (junto con Urgent Pointer).
  - **ACK**: confirma que `Acknowledgment Number` es válido.
  - **PSH**: pedido de que el receptor entregue los datos a la app sin esperar más buffer.
  - **RST**: cerrar la conexión **abruptamente**.
  - **SYN**: pedido de **establecer** conexión.
  - **FIN**: cierre de conexión **polite**.
- **Urgent Pointer**: si URG=1, indica hasta dónde van los datos urgentes.
- **Options**: TCP Window Scaling, timestamps para estimar RTT, MSS, etc.

Hay **paquetes vacíos** en TCP cuyo único contenido útil es el **Acknowledgment**.

## Establecimiento de la conexión — Three-way handshake

El servidor primero crea un **socket pasivo** y queda en estado `LISTEN`.

```
Cliente                                 Servidor
  |                                       |  (LISTEN)
  |─── SYN, seq=x ──────────────────────►|
  | (SYN_SENT)                            | (SYN_RCVD)
  |                                       |
  |◄── SYN, ACK, seq=y, ack=x+1 ──────────|
  |                                       |
  |─── ACK, seq=x+1, ack=y+1 ────────────►|
  | (ESTABLISHED)                         | (ESTABLISHED)
```

1. Cliente: SYN=1, número de secuencia random `x`. Estado: `SYN_SENT`.
2. Servidor: SYN=1, ACK=1, su propio seq `y`, ack `x+1`. Estado: `SYN_RCVD`.
3. Cliente: ACK=1, ack `y+1`. Estado: `ESTABLISHED`. Servidor también pasa a `ESTABLISHED`.

A partir de acá puede haber transferencia de datos. **Los ACKs no son por segmento sino por byte**, porque el canal es un flujo de bytes.

## Retransmisión por timeout

Si se pierde un segmento, los segmentos posteriores **no son confirmados** (porque ACK es acumulativo y refleja el último byte recibido en orden). El emisor detecta el **timeout** y retransmite.

Ejemplo: si llega `seq=120` pero el `seq=100` se perdió, el receptor manda ACK=100 (el primer byte que sigue esperando) y guarda el `seq=120` en buffer. Cuando llega el reenvío de 100, el receptor entrega todo en orden.

## Estimación del timeout

El timeout no es un valor fijo:
- Muy chico → retransmisiones innecesarias.
- Muy grande → ante pérdida queda esperando demasiado.

Se estima a partir del **RTT** (round trip time) medido — empezando por el RTT del three-way handshake — y se va ajustando dinámicamente.

## Cierre de la conexión — FIN

Se envía un segmento con **FIN=1**. El que mandó el FIN ya no puede enviar **datos**, pero sí puede **seguir recibiendo**. Cualquier extremo (cliente o servidor) puede iniciar el cierre. En TCP, **solo importa quién es cliente y quién servidor en el momento de iniciar la conexión** — después la relación es simétrica.

Un cierre completo necesita FIN+ACK en ambas direcciones (cierre "polite"). Para cierre abrupto se usa **RST**.

## Aparece en parciales

- "Si A envía dos segmentos a B sobre TCP. El primero tiene seq=90, el segundo seq=110. (a) ¿Cuántos bytes hay en el primero? (b) Si el primero se pierde y el segundo arriba, ¿cuál será el ACK? (c) ¿Cuántos bytes hay en el segundo?" → (a) **20 bytes** (110 - 90), (b) ACK = **90** (espera el byte que se perdió), (c) **no se puede determinar** sin el seq del tercero (guía 4 ej. 11).
- "En TCP, si no se recibe ACK en tiempo determinado, ¿qué pasa?" → **se retransmite el paquete** (guía 4 ej. 4; preguntas-teoricas ej. 41).
- "El modelo TCP/IP no ofrece nivel de Sesión. HTTP no es orientado a conexión. ¿Cómo mantiene una sesión una app web?" → con **cookies** (preguntas-teoricas ej. 42).

## Fuente

- Apunte oficial, Unidad 5, p. 56-58.
- Guía 4 (Transporte), ej. 4, 11, 14.
- RFC 7323 (TCP Extensions), RFC 5681 (Congestion Control).
