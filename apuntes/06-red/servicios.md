# Servicios

La capa de red traslada segmentos de un host a otro. Sus dos funciones mínimas son **identificar a cada host** (direccionamiento jerárquico) y **lograr que la información llegue a destino** (forwarding + routing). Sobre esa base, distintos protocolos ofrecen servicios opcionales como entrega garantizada, orden, ancho de banda mínimo o integridad — IP **no ofrece ninguno** de ellos.

## Funciones de un router

- **Forwarding:** decisión local que toma un host/router en base a su tabla de ruteo. Mira la IP destino y elige por qué interfaz sacar el paquete. Si el host no tiene forwarding habilitado y le llega un paquete cuyo destino no es él, lo descarta.
- **Routing:** cómo se construye esa tabla (algoritmos estáticos o dinámicos).
- **Establecer conexión** (ATM, frame relay, X.25): antes de transmitir datagramas se arma un circuito virtual. **IP no lo hace.**

## Servicios que puede dar un protocolo de red

- **Conmutación de paquetes (datagramas)**
  - Delivery garantizado *(IP no)*
  - Delay mínimo garantizado *(IP no)*
- **Flujo de datagramas (circuito virtual)** — ej.: cajeros Banelco/Link
  - Entrega en orden *(IP no)*
  - Ancho de banda mínimo *(IP no)*
- **Ambos:** integridad, encriptación, etc. *(IP no — esos servicios los provee IPSec u otra capa por encima)*.

## IP en concreto

IP es **sin conexión y sin reconocimiento (sin ACK), no confiable**. No retransmite, no ordena, no verifica entrega. Esos servicios los aporta —si hace falta— la capa de transporte (TCP) o la aplicación.

## Aparece en parciales

- *Guía 5, Ej. 1:* "El protocolo IP ofrece a su capa superior… → un servicio sin conexión sin reconocimiento."
- *Preguntas teóricas, Ej. 5:* las características de IPv4 son: protocolo **no confiable** y **sin ACK**. NO elige el mejor camino (eso es routing dinámico, otro protocolo) y sus datos no necesariamente son de capa de transporte (puede ser ICMP).
- *Preguntas teóricas, Ej. 37:* ningún protocolo de transporte (TCP/UDP/SCTP) ni IP garantizan delay mínimo.
- *Preguntas teóricas, Ej. 23:* que cada enlace de capa 2 sea confiable **no implica** que el servicio de red entre dos extremos sea confiable.
- *Preguntas teóricas, Ej. 36:* que una capa OSI sea con conexión no obliga a la capa superior a serlo (ej.: HTTP sin conexión sobre TCP con conexión).

> Fuente: Apuntes oficiales, Unidad 6, p. 71.
