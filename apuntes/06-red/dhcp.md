# DHCP (Dynamic Host Configuration Protocol)

**DHCP** permite a un host obtener automáticamente su configuración de red al conectarse: IP, máscara, gateway, DNS, tiempo de préstamo. El cliente envía un Discover en broadcast, un servidor responde con un Offer, el cliente confirma con Request y el servidor finaliza con ACK. Corre **sobre UDP** (puertos 67 servidor, 68 cliente).

## Por qué UDP y no TCP

- El cliente **todavía no tiene IP** cuando arranca el intercambio → no puede establecer un three-way handshake con destino claro.
- Los mensajes iniciales son **broadcast** (`255.255.255.255`) — TCP no puede ir en broadcast.
- DHCP necesita ser rápido, sin estado.

## El ciclo DORA

```
Cliente                        Servidor
   │                              │
   │ ─── Discover (broadcast) ──→ │
   │                              │
   │ ←─── Offer (broadcast L2) ── │   ofrece IP, lifetime
   │                              │
   │ ─── Request (broadcast) ──→  │   acepta esa IP
   │                              │
   │ ←──── ACK (broadcast L2) ─── │   confirma asignación
```

1. **Discover:** cliente busca servidores DHCP. Origen `0.0.0.0`, destino `255.255.255.255`.
2. **Offer:** servidor ofrece una IP libre con su lifetime. Aunque la IP destino podría ser unicast, **a nivel de enlace va en broadcast** porque el cliente todavía no tiene IP — el cliente filtra por su MAC.
3. **Request:** cliente solicita formalmente esa IP (también broadcast — así otros servidores DHCP se enteran de que no se eligió la suya).
4. **ACK:** servidor confirma. El cliente empieza a usar la IP.

## Formato del mensaje DHCP

| Campo | Tamaño | Función |
|-------|--------|---------|
| `op` | 1 B | 1 = solicitud (cliente), 2 = respuesta (servidor) |
| `htype` | 1 B | Tipo de hardware (1 = Ethernet) |
| `hlen` | 1 B | Longitud de la MAC (6 para Ethernet) |
| `hops` | 1 B | Saltos (relay agents) |
| `xid` | 4 B | ID de transacción (lo genera el cliente) |
| `secs` | 2 B | Segundos desde que el cliente empezó a pedir IP |
| `flags` | 2 B | Bit MSB = 1 → forzar broadcast |
| `ciaddr` | 4 B | IP actual del cliente (si ya tiene) |
| `yiaddr` | 4 B | "Your IP address" — IP ofrecida por el servidor |
| `siaddr` | 4 B | IP del servidor que da archivo de arranque |
| `giaddr` | 4 B | IP del gateway / relay agent |
| `chaddr` | 16 B | MAC del cliente (los primeros 6 bytes en Ethernet) |
| `sname` | 64 B | Nombre del servidor DHCP (opcional) |
| `file` | 128 B | Archivo de arranque (opcional) |
| `options` | variable | Opciones DHCP (gateway, DNS, lifetime, tipo de mensaje, etc.) |

## Reservas

Aunque DHCP es dinámico, se pueden reservar IPs por MAC: el server siempre da la misma IP a esa MAC. Útil para impresoras, NAS, etc.

## Renovación del lease

- A la **mitad** del lifetime, el cliente envía un **renew request** al servidor (unicast esta vez, ya conoce la IP del server).
- Si no hay respuesta, vuelve a intentar a **3/4 del lifetime**.
- Si tampoco contesta, sigue usando la IP hasta el vencimiento; si vence sin renovar, **deja de usar la IP** y reinicia DORA.
- Si llega `renew ack` → renovado.
- Si llega `renew nack` → el server rechazó; el cliente vuelve a empezar con Discover.

## Relay Agents

Si tengo 20 subredes, sería absurdo poner 20 servidores DHCP. Solución: un **router con relay agent** en cada subred, que **forwardea unicast** los broadcasts de DHCP hacia un servidor central, y traduce las respuestas de vuelta.

- El cliente hace Discover en broadcast (`255.255.255.255`).
- El router (relay) lo manda **unicast** al servidor DHCP, agregando `giaddr` (su propia IP en la subred del cliente) — así el servidor sabe de qué subred asignar IP.
- Las respuestas vuelven al relay, que las retransmite a la LAN.

## DDNS (DNS dinámico)

Como las IPs de DHCP cambian, los registros DNS se desincronizan. El **DDNS** permite que el server DHCP avise al server DNS cada vez que asigna o renueva una IP, manteniendo `nombre → IP` actualizado. Útil para que otros hosts encuentren al cliente por nombre aunque cambie de IP.

## Aparece en parciales

- *Guía 5, Ej. 26:* qué pasa si el host pide renovar y nadie contesta.
  - Si es a la mitad del lease → reintenta a 7/8 del tiempo.
  - Si ya venció → sigue usando la IP por un rato; si tampoco hay respuesta, reinicia DORA. (En la práctica deja de usarla cuando vence definitivamente.)

> Fuente: Apuntes oficiales, Unidad 6, p. 83-87.
