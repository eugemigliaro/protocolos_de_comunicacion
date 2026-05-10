# SCTP (Stream Control Transmission Protocol)

Protocolo de transporte similar a TCP/UDP pero diseñado para ofrecer **mayor fiabilidad y control**. Sus características distintivas son **multi-streaming**, **multi-homing**, y un **inicio protegido** contra ataques.

## Multi-homing

Un único host puede estar asociado a **múltiples direcciones IP** dentro de la **misma asociación** SCTP (en SCTP no se habla de "conexión" sino de "asociación"). Tanto emisor como receptor pueden tener varias interfaces de red activas en la misma sesión.

**Ventaja: tolerancia a fallos.** Si una IP/interfaz falla, SCTP **redirige automáticamente** el tráfico por otra dirección disponible **sin cerrar ni restablecer** la asociación → **alta disponibilidad**, especialmente útil en telecom y sistemas industriales.

## Multi-streaming

Permite enviar **múltiples flujos de datos independientes** dentro de una misma asociación. A diferencia de TCP (un solo flujo ordenado), SCTP divide la comunicación en **streams** separados, lo que **evita el head-of-line blocking**: que un paquete perdido en un stream **no detiene** la entrega del resto.

```
TCP                          SCTP
───────────                  ┌── stream 1 ──
[byte stream]                ├── stream 2 ──
                             └── stream 3 ──
```

## Orientado a mensajes (no a bytes)

SCTP **preserva los límites** de los mensajes: cada chunk llega **tal como fue enviado**, sin dividirse ni fusionarse. Es lo opuesto a TCP, que es orientado a bytes y obliga a la app a delimitar mensajes por su cuenta.

Esto lo hace adecuado para **señalización en telecomunicaciones** y protocolos de control donde importa que cada mensaje llegue íntegro y separado.

## Inicio protegido — Cookie mechanism (4-way handshake)

TCP usa un handshake de 3 pasos vulnerable a **SYN flood**: el atacante envía SYNs sin completar el handshake, el server reserva recursos, y los recursos se acaban → DoS.

SCTP usa **4 pasos** con **cookie criptográfica**:

1. Cliente → Servidor: `INIT`.
2. Servidor → Cliente: `INIT-ACK` con una **cookie criptográfica** (estado firmado, server **no guarda nada** todavía).
3. Cliente → Servidor: `COOKIE-ECHO` (reenvía la cookie).
4. Servidor verifica la cookie; si es válida, **recién ahí asigna recursos** y responde `COOKIE-ACK`.

```
Cliente                              Servidor
  |─── INIT ──────────────────────►|
  |                                | (no guarda estado)
  |◄── INIT-ACK + cookie ──────────|
  |─── COOKIE-ECHO + cookie ──────►|
  |                                | (verifica cookie, asigna recursos)
  |◄── COOKIE-ACK ─────────────────|
```

Como el server no guarda estado hasta el COOKIE-ECHO válido, los SYN flood son inofensivos: no consumen memoria.

## Cierre seguro — 4 pasos

Simétrico y confiable, evita que un solo extremo cierre unilateralmente:

1. Un extremo envía `SHUTDOWN` indicando que no enviará más datos.
2. El otro responde `SHUTDOWN-ACK` confirmando.
3. El primero responde `SHUTDOWN-COMPLETE`.
4. Se liberan recursos en ambos extremos.

## Comparación TCP / UDP / SCTP

| | TCP | UDP | SCTP |
|---|---|---|---|
| Orientado a conexión | ✓ | ✗ | ✓ (asociación) |
| Confiable | ✓ | ✗ | ✓ |
| Orientado a | bytes | mensajes | **mensajes** |
| Control de flujo / congestión | ✓ | ✗ | ✓ |
| Multi-streaming | ✗ | ✗ | ✓ |
| Multi-homing | ✗ | ✗ | ✓ |
| Resistente a SYN flood | ✗ | n/a | ✓ |

## ¿Por qué no se usa más?

A pesar de las ventajas, SCTP no terminó imponiéndose:

- **Compatibilidad limitada**: muchos OS y dispositivos de red no lo soportan nativamente.
- **Restricciones en firewalls y NATs**: usa un número de protocolo IP **distinto** del de TCP/UDP, y los dispositivos que no lo reconocen lo bloquean por defecto.
- **Falta de soporte en navegadores y aplicaciones**: la web está construida sobre TCP, migrar implicaría cambios enormes.
- **HTTP/2** mitigó parcialmente el problema: introduce multiplexación de flujos sobre **una sola** conexión TCP. Reduce el HOL **a nivel de aplicación** pero no a nivel de transporte (sigue siendo TCP).
- **HTTP/3** lo resuelve por otro camino: usa **QUIC** sobre **UDP**, con múltiples flujos independientes y confiabilidad implementada en la capa de aplicación. Elimina HOL en ambos niveles.

## Aparece en parciales

- "Si una aplicación cliente/servidor que intercambia recursos quiere evitar el Head of Line Blocking, ¿cómo lo haría en la forma más simple?" → Una opción es usar **SCTP** y pedir cada recurso en un **flujo separado**. Otra: **una conexión TCP por recurso**. Otra (más compleja): **UDP** y reensamblar en la app (guía 4 ej. 12).
- "¿Cuál/es de los siguientes protocolos me garantiza un mínimo delay?" → **Ninguno**, ni siquiera SCTP (preguntas-teoricas ej. 37).

## Fuente

- Apunte oficial, Unidad 5, p. 64-67.
- Guía 4 (Transporte), ej. 6, 12.
