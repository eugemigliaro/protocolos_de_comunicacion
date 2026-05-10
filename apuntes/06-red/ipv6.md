# IPv6

**IPv6** es el reemplazo de IPv4 frente al agotamiento de direcciones de 32 bits. Usa direcciones de **128 bits**, **header de tamaño fijo** (40 B), elimina la fragmentación en routers, no tiene checksum, no necesita ARP (la MAC va en la dirección) y reemplaza broadcast por multicast/anycast. Sigue siendo **sin conexión y sin reconocimiento**, igual que IPv4.

## Comparación rápida con IPv4

| | IPv4 | IPv6 |
|---|---|---|
| Tamaño dirección | 32 bits | 128 bits |
| Header | Variable (≥ 20 B) | **Fijo, 40 B** |
| Checksum del header | Sí | **No** |
| Fragmentación | En origen y routers | **Sólo en origen** |
| Resolución MAC | ARP | NDP (sobre ICMPv6) |
| Broadcast | Sí (255.255.255.255) | **No** — usa multicast |
| Configuración auto | DHCP | DHCPv6 + SLAAC |

## Notación

128 bits → 8 bloques de 4 dígitos hexadecimales separados por `:`.

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

Reglas para acortar:

- **Ceros a la izquierda** en cada bloque se pueden omitir → `2001:db8:85a3:0:0:8a2e:370:7334`.
- **Bloques contiguos de ceros** se reemplazan por `::` (sólo **una vez** en la dirección, para no ambigüizar) → `2001:db8:85a3::8a2e:370:7334`.

## Tipos de direcciones

A diferencia de IPv4, IPv6 distingue tres modelos: **unicast** (1 a 1), **multicast** (1 a muchos, los suscriptos al grupo) y **anycast** (1 al "más cercano" de un grupo). **No hay broadcast.**

### Unicast — subtipos

| Tipo | Prefijo | Uso |
|------|---------|-----|
| **Global Unicast (GUA)** | `2000::/3` | Públicas, ruteables globalmente |
| **Link-Local** | `FE80::/10` | Sólo dentro del enlace, no atraviesa routers |
| **Unique Local (ULA)** | `FC00::/7` | Privadas (análogo a 10/8, 192.168, etc.) — atraviesan routers internos pero no salen a Internet |
| **Loopback** | `::1/128` | localhost |
| **Unspecified** | `::/128` | "No asignada" (configuración inicial) |
| **IPv4-mapped** | `::ffff:0:0/96` | Representar IPv4 en formato IPv6 |

### Multicast — `FF00::/8`

- **Well-known:** funciones predefinidas (ej.: `FF02::1` = todos los nodos del enlace).
- **Transient:** sesiones temporales.
- **Solicited-node multicast:** `FF02::1:FF00:0/104` — usado por NDP en lugar de ARP.

### Anycast

Una dirección asignada a varias interfaces; el paquete va a la **más cercana** según routing. No hay un prefijo dedicado — se usan direcciones unicast normales que se anuncian desde varios nodos (típico en DNS root servers, CDN).

## Interface ID

Los últimos **64 bits** de una dirección unicast son el Interface ID. Dos formas de generarlo:

- **EUI-64** desde la MAC: tomar la MAC (48 bits), insertar `FFFE` en el medio, y flippear el bit U/L. **Problema:** revela el fabricante y el dispositivo concreto → permite tracking del usuario aunque cambie de red.
- **Direcciones temporales / privacidad (RFC 4941):** se genera un Interface ID aleatorio que rota cada cierto tiempo, para mitigar el tracking. Es el default moderno.

## Header IPv6

```
 0                   1                   2                   3
+---------------+---------------+---------------+---------------+
|Versión|Traffic Class |        Flow Label                      |
+-------+-------+-------+-------+---------------+---------------+
|         Payload Length        | Next Header   |   Hop Limit   |
+-------------------------------+---------------+---------------+
|                                                               |
+                       Source Address (128 bits)               +
|                                                               |
+---------------------------------------------------------------+
|                                                               |
+                    Destination Address (128 bits)             +
|                                                               |
+---------------------------------------------------------------+
```

- **Versión (4 b):** siempre `6`.
- **Traffic Class (8 b):** equivalente a ToS de IPv4 (QoS, DSCP).
- **Flow Label (20 b):** identifica un flujo de paquetes (todos los paquetes del mismo flujo deben ser tratados igual por los routers — mismo next-hop, mismo trato de QoS). Sirve para video, voz, y para que **paquetes del mismo flujo no se desordenen**.
- **Payload Length (16 b):** tamaño del payload en bytes (sin contar el header IPv6).
- **Next Header (8 b):** identifica el siguiente protocolo (TCP, UDP, ICMPv6, o un **extension header** de IPv6).
- **Hop Limit (8 b):** equivalente al TTL.
- **Source / Destination (128 b c/u).**

> El header es **fijo en 40 bytes**. Lo que en IPv4 eran "opciones" se mueve a **extension headers** opcionales encadenados con el campo `Next Header`.

## Mejoras de IPv6 sobre IPv4 (pregunta de parcial)

1. **Header de tamaño fijo** → procesamiento más rápido y simple por los routers (no calculan tamaño, no procesan opciones).
2. **MAC integrada en la dirección (EUI-64)** → no hace falta ARP, menos tráfico y se elimina ARP spoofing como vector.
3. **No hay fragmentación en routers** → sólo el host emisor fragmenta (con Path MTU Discovery). Los routers no se sobrecargan reensamblando.
4. **No hay checksum del header** → menos cómputo en cada salto. La integridad la garantizan capas inferiores (Ethernet) y superiores (TCP/UDP).
5. **Cantidad de direcciones** — `2¹²⁸ ≈ 3.4 × 10³⁸`, suficiente para cada átomo en la Tierra.
6. **No hay broadcast** → reemplazado por multicast/anycast, más eficiente.
7. **Mejor soporte de seguridad** (IPSec era opcional en IPv4, originalmente obligatorio en IPv6 — hoy también opcional pero asumido).
8. **Soporte nativo de movilidad** (Mobile IPv6).

## Aparece en parciales

- *Guía 5, Ej. 22:* el campo **Flow Label** etiqueta paquetes de un mismo flujo para que los routers les den el mismo tratamiento (típicamente mismo next-hop). Evita desorden, mejora QoS para voz/video.
- *Guía 5, Ej. 23:* ventaja del header fijo → procesamiento más rápido (no calcula tamaño, no procesa opciones), mejora el rendimiento.
- *Guía 5, Ej. 21.7:* ICMP no sirve para IPv6, se usa ICMPv6.
- *Guía 5, Ej. 21.11:* `::1` es el localhost de IPv6. **Verdadero.**
- *Guía 5, Ej. 21.15:* no existe ARP para IPv6 — la dirección ya contiene la MAC (EUI-64). **Verdadero.**
- *Guía 5, Ej. 21.16:* no existe broadcast en IPv6. **Verdadero.**
- *Guía 5, Ej. 21.10:* los protocolos de transporte **no se enteran** de si la red es v4 o v6 — funcionan igual. **Falso decir lo contrario.**
- *Preguntas teóricas, Ej. 13:* tres ventajas de IPv6 — header fijo, MAC en la dirección (no ARP), más direcciones.
- *Preguntas teóricas, Ej. 27:* dos hosts IPv6 en redes distintas pueden hablar **aunque haya routers IPv4 en el camino** — usando **tunneling** (encapsulan IPv6 dentro de IPv4 en los tramos intermedios).

> Fuente: Apuntes oficiales, Unidad 6, p. 92-97.
