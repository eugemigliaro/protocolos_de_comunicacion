# ICMP

**ICMP (Internet Control Message Protocol)** es un protocolo de la familia IP (no es TCP, no es UDP) que sirve para enviar **mensajes de control y error** entre dispositivos de red — no para datos de usuario. Va encapsulado directamente sobre IP (campo Protocol = 1). Es la base de herramientas como `ping` y `traceroute`.

## Formato del mensaje

```
+--------+--------+----------------+
|  Type  |  Code  |   Checksum     |
+--------+--------+----------------+
|         Datos (depende type)     |
+----------------------------------+
```

- **Type (1 B):** tipo de mensaje (ej.: `0` = Echo Reply, `8` = Echo Request, `3` = Destination Unreachable, `11` = Time Exceeded, `5` = Redirect).
- **Code (1 B):** subtipo. Ej.: con Type 3, Code 0 = Network Unreachable, Code 1 = Host Unreachable, Code 3 = Port Unreachable.
- **Checksum (2 B):** verifica integridad del mensaje ICMP.
- **Datos:** dependen del Type/Code; típicamente incluyen parte del paquete IP que disparó el mensaje (header IP + primeros 8 bytes del payload).

## Mensajes principales

### Echo Request / Echo Reply (Type 8 / 0) — `ping`

`ping` manda Echo Request al destino; el destino responde Echo Reply. Mide latencia (RTT) y pérdida de paquetes. Útil para diagnosticar conectividad.

> Si un host no responde a ping, **no significa** que no exista — puede tener Echo Request bloqueado por firewall o configuración.

### Destination Unreachable (Type 3)

El paquete no pudo entregarse. El Code dice por qué: red inalcanzable, host inalcanzable, puerto cerrado, fragmentación necesaria con DF=1, etc. Se manda **al origen** del paquete que falló.

### Time Exceeded (Type 11) — base de `traceroute`

Se manda cuando el TTL de un paquete IP llega a 0, o cuando se vence el timeout de reensamblado de fragmentos. **Lo emite el router que descarta el paquete** (no el destino final).

### Redirect (Type 5)

Un router le avisa a un host que **hay una ruta mejor** para un destino. El router envía un Redirect cuando ve que un paquete que recibió debería haberse mandado por otro router en la misma subred.

> **Por defecto, los hosts ignoran ICMP Redirect** — porque es un vector de spoofing fácil. Un atacante en la misma red podría falsificar Redirects para desviar tráfico (MITM).

### Timestamp Request / Reply

Sincronizar relojes y medir RTT.

## Cómo funciona `traceroute`

1. Manda un paquete con TTL = 1.
2. El primer router lo descarta y responde **ICMP Time Exceeded** → se descubre el primer salto.
3. Manda paquete con TTL = 2 → se descubre el segundo salto.
4. Repite hasta que el TTL alcanza al destino, que responde **ICMP Echo Reply** o **ICMP Port Unreachable** (depende de la implementación).

> En Linux clásicamente `traceroute` usa **UDP** (a un puerto alto improbable, espera Port Unreachable como señal de "llegamos"). En Windows `tracert` usa **ICMP Echo**. Ambos dependen de **Time Exceeded** para los saltos intermedios.

> Si en el output de traceroute aparece `* * *`, es que **el router de ese salto no respondió Time Exceeded** (o lo bloquea un firewall en el camino). **No** significa que no responda Echo Request — el Echo lo responde sólo el destino final.

## ICMP e IPv6

ICMPv4 e ICMPv6 son protocolos distintos (aunque conceptualmente parecidos). ICMPv6 además absorbe funciones que en IPv4 hacía ARP (Neighbor Discovery). **ICMP no es compatible con IPv6** — hay que usar ICMPv6.

## Aparece en parciales

- *Guía 5, Ej. 21.4:* `ping` usa ICMP tipos 0 y 8. **Verdadero.**
- *Guía 5, Ej. 21.5:* "si pingueo y no responde, es porque el host no existe o hay problema de conectividad" → **falso**, podría tener bloqueado Echo.
- *Guía 5, Ej. 21.6:* descarte por TTL → router envía ICMP tipo 11. **Verdadero, pero opcional.**
- *Guía 5, Ej. 21.7:* ICMP no es compatible con IPv6, hay que usar ICMPv6. **Verdadero.**
- *Guía 5, Ej. 21.2:* `traceroute` es un utilitario que usa ICMP combinado con TTL. **Verdadero** (no es un protocolo, es una herramienta).
- *Preguntas teóricas, Ej. 3:* hosts ignoran Redirect por defecto → **por seguridad (anti-spoofing)**.
- *Preguntas teóricas, Ej. 12:* `* * *` en traceroute = el router de ese salto no notifica Time Exceeded; **no** es que no responda ICMP Echo (el Echo lo responde sólo el destino final).
- *Preguntas teóricas, Ej. 30:* al droppear un paquete, el router puede **opcionalmente** mandar ICMP indicando la razón. **Verdadero.**

> Fuente: Apuntes oficiales, Unidad 6, p. 89-91.
