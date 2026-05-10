# Caso Práctico — Enlace, ARP y arranque de red

Caso típico de parcial: se enciende un host A en una red privada y se conecta por HTTP a `campus.itba.edu.ar`. **Se asume que las tablas ARP están vacías** y que A todavía no tiene IP.

## Escenario

Red privada con:

- **Host A** → recién encendido, todavía sin IP.
- **DHCP / DNS** → `10.1.1.5` (DHCP) y `10.1.1.4` (DNS).
- **Gateway / router** → `10.1.1.1`.
- A obtendrá la IP `10.1.1.3`.

## Secuencia completa de paquetes

| Protocolo      | Detalle                | Origen          | Destino                  |
|----------------|------------------------|-----------------|--------------------------|
| DHCP discover  | id/mac                 | `0.0.0.0`       | `255.255.255.255`        |
| DHCP offer     | yaddr: `10.1.1.3`      | `10.1.1.5`      | `10.1.1.3` (MAC: broadcast) |
| DHCP request   | —                      | `0.0.0.0`       | `255.255.255.255`        |
| DHCP ack       | —                      | `10.1.1.5`      | `10.1.1.3` (MAC: broadcast) |
| ARP request    | ¿MAC de `10.1.1.4`?    | MAC de `10.1.1.3` | `FF:FF:FF:FF:FF:FF`    |
| ARP reply      | MAC de `10.1.1.4`      | MAC de `10.1.1.4` | MAC de `10.1.1.3`      |
| DNS query      | ¿IP de `campus`?       | `10.1.1.3`      | `10.1.1.4`               |
| DNS response   | IP de `campus`         | `10.1.1.4`      | `10.1.1.3`               |
| ARP request    | ¿MAC de `10.1.1.1`?    | `10.1.1.3`      | `FF:FF:FF:FF:FF:FF`      |
| ARP reply      | MAC de `10.1.1.1`      | `10.1.1.1`      | MAC de `10.1.1.3`        |
| TCP SYN        | puerto destino: 80     | `10.1.1.3`      | IP de `campus`           |
| TCP SYN-ACK    | —                      | IP de `campus`  | `10.1.1.3`               |
| TCP ACK        | —                      | `10.1.1.3`      | IP de `campus`           |
| HTTP           | GET                    | `10.1.1.3`      | IP de `campus`           |
| HTTP           | 301                    | IP de `campus`  | `10.1.1.3`               |
| TCP RST        | —                      |                 |                          |

## Por qué cada paso

### 1. DHCP (4 paquetes: discover, offer, request, ack)

A no tiene IP. Manda **DHCP discover** con origen `0.0.0.0` (no tiene IP) y destino broadcast (no sabe la IP del DHCP). El servidor DHCP `10.1.1.5` responde con un **offer** ofreciendo la IP `10.1.1.3`. A confirma con **DHCP request** (todavía broadcast, porque puede haber varios DHCP servers y debe elegir uno) y el servidor cierra con **DHCP ack**. La MAC destino sigue siendo broadcast hasta que A confirma su IP.

### 2. ARP request al DNS

A ya tiene IP, pero quiere resolver `campus.itba.edu.ar` por DNS. Necesita hablar con `10.1.1.4` (DNS), que está en su misma subred. Como no tiene la MAC, hace un **ARP request broadcast** preguntando por la MAC de `10.1.1.4`. El DNS responde con su MAC.

### 3. DNS query/response

Ahora A puede mandar el DNS query unicast al server DNS y recibe la IP pública de `campus`.

### 4. ARP request al gateway

`campus` es una IP pública, está en otra red. A le va a mandar el paquete IP al **gateway** `10.1.1.1`. Como tampoco tiene su MAC, hace otro **ARP request** por la MAC del gateway. El gateway responde.

### 5. Three-way handshake TCP al puerto 80

Con todo lo anterior listo, A abre una conexión TCP al puerto 80 de `campus`: SYN → SYN-ACK → ACK.

### 6. HTTP GET → 301 → TCP RST

A manda el GET por HTTP. `campus.itba.edu.ar` está configurado para forzar HTTPS, así que devuelve **301 Moved Permanently** redirigiendo a `https://...`. Después se cierra esa conexión con TCP RST y se debería repetir todo el proceso TCP **al puerto 443** en lugar del 80. Si A hubiera ido directo a HTTPS desde el principio, se ahorraba toda esta vuelta.

## Variantes posibles en parciales

- **El DNS no conoce la respuesta**: tiene que ir a `8.8.8.8` (u otro servidor recursivo). Es lo que ya se vio en la unidad de DNS.
- **Pasos en desorden o de más**: el parcial puede dar la lista alterada para que se identifique el error.
- **HTTPS desde el principio**: se evita el 301 y el RST. La secuencia hasta el TCP es igual; cambia el puerto destino (443) y se agrega el handshake TLS antes del HTTP.

## Por qué este caso integra todo

Este ejercicio toca:

- **DHCP** (unidad 6 — red).
- **ARP** (unidad 8 — enlace) en sus dos roles: resolver MAC en la misma red (DNS) y resolver MAC del gateway para tráfico hacia afuera.
- **DNS** (unidad 3) tanto en resolución directa como recursiva.
- **TCP** (unidad 5) con three-way handshake.
- **HTTP** (unidad 2) con redirect 301 a HTTPS.

Es un ejercicio canónico para repasar antes del parcial.

## Aparece en parciales

- **Detallar los paquetes que se generan al encender un host y conectarse por HTTP a un dominio**, asumiendo tablas ARP vacías. Es el ejercicio prototípico de la unidad.
- Variantes con la lista en desorden: hay que identificar qué falta o qué sobra.

## Fuentes

- PDF unidad 8, p. 120-122.
