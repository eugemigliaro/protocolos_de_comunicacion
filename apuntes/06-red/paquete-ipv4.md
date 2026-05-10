# Paquete IPv4

El **paquete IPv4** consta de un encabezado de tamaño variable (mínimo 20 bytes, sin opciones) seguido del payload. El header lleva toda la metadata para que el paquete llegue a destino y para soportar fragmentación.

## Estructura del header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Versión|  HLen |     ToS       |       Longitud Total          |
+-------+-------+-------+-------+-------------------------------+
|        Identificación         |Flags|   Despl. Fragmento      |
+-------------------------------+-----+-------------------------+
|     TTL       |   Protocolo   |       Header Checksum         |
+---------------+---------------+-------------------------------+
|                       Dirección de Origen                     |
+---------------------------------------------------------------+
|                       Dirección de Destino                    |
+---------------------------------------------------------------+
|                Opciones                       |   Padding     |
+---------------------------------------------------------------+
|                          Datos...                             |
+---------------------------------------------------------------+
```

## Campos

- **Versión (4 bits):** versión del protocolo IP. Para IPv4 → `4`.
- **HLen / Header Length (4 bits):** tamaño del header en bloques de 32 bits (mínimo 5 = 20 bytes).
- **ToS / Type of Service (8 bits):** prioridad del paquete (QoS, DSCP).
- **Longitud Total (16 bits):** tamaño total del paquete (header + datos) en bytes. Máximo **65 535 bytes**.
- **Identificación (16 bits):** ID único del paquete. Si se fragmenta, todos los fragmentos comparten el mismo ID para poder ser reensamblados.
- **Flags (3 bits):**
  - bit 0: reservado.
  - bit 1: **DF** (Don't Fragment) — prohibe fragmentar.
  - bit 2: **MF** (More Fragments) — hay más fragmentos después.
- **Desplazamiento de Fragmento (13 bits):** posición del fragmento dentro del paquete original, medida en **bloques de 8 bytes**.
- **TTL / Time To Live (8 bits):** máximo de saltos. Cada router lo decrementa en 1; si llega a 0, se descarta.
- **Protocolo (8 bits):** protocolo del payload. `1` = ICMP, `6` = TCP, `17` = UDP, `50` = ESP, `51` = AH.
- **Header Checksum (16 bits):** verifica integridad **del header solamente**, no del payload. Si no coincide, el paquete se descarta. Como el TTL cambia en cada salto, **se recalcula en cada router**.
- **Dirección Origen / Destino (32 bits c/u):** IPs.
- **Opciones:** seguridad, ruta registrada, timestamp, etc. Suelen estar vacías.
- **Padding:** ceros para que el header termine en múltiplo de 32 bits.
- **Datos:** payload (típicamente un segmento TCP/UDP o un mensaje ICMP).

## Fragmentación

Cada enlace tiene un **MTU (Maximum Transmission Unit)** — el tamaño máximo de paquete que puede transportar (ej.: Ethernet ≈ 1500 bytes). Si un paquete IP excede el MTU del próximo enlace, **un router lo fragmenta**:

- Cada fragmento es un paquete IP con su propio header.
- Comparten el mismo campo **Identificación**.
- Todos menos el último tienen `MF = 1`.
- El **Desplazamiento** indica dónde empieza dentro del paquete original (en bloques de 8 bytes — por eso los fragmentos intermedios deben tener un tamaño de datos múltiplo de 8).
- Reensamblan **sólo en el destino final**, nunca en routers intermedios.

Si el paquete tiene `DF = 1` y no entra en el MTU → el router lo descarta y opcionalmente envía un ICMP "Fragmentation Needed".

### Ejemplo (parcial)

Datagrama de 3100 bytes a un enlace con MTU 800. Asumiendo IPv4 (20 B) + UDP (8 B) + Ethernet (24 B), el espacio útil para datos por paquete es 800 − 24 − 20 = 756 B (sin contar UDP, que va sólo en el primer fragmento). Pero como los desplazamientos van en bloques de 8 bytes, hay que **redondear los datos a múltiplo de 8** → en la práctica 744 B de datos por fragmento. El resultado: **4 fragmentos de 744 B + 1 último con 124 B**.

## Aparece en parciales

- *Guía 5, Ej. 3:* fragmentación de 3100 B con MTU 800.
- *Preguntas teóricas, Ej. 21.8:* si DF=1 y no entra en el MTU, el router **descarta** y opcionalmente manda ICMP. Verdadero para IPv4. **Falso para IPv6** porque IPv6 no fragmenta en routers.
- *Preguntas teóricas, Ej. 30:* al droppear, el router **puede opcionalmente** mandar ICMP indicando la razón.
- *Preguntas teóricas, Ej. 40:* el TTL existe para evitar **loops a nivel IP**, no a nivel switch (el switch no mira IP — usa otra cosa: STP).
- *Parcial Teórico 1Q2025, Ej. 12:* traceroute aprovecha el TTL — manda paquetes con TTL incrementales y cada router responde ICMP Time Exceeded al descartarlos.

> Fuente: Apuntes oficiales, Unidad 6, p. 75-77.
