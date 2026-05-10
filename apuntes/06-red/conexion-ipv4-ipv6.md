# Conexión IPv4 e IPv6

La transición de IPv4 a IPv6 no es instantánea — coexisten. Hay tres mecanismos de transición principales: **dual stack** (cada host habla los dos), **tunneling** (encapsular paquetes de un protocolo dentro del otro para atravesar redes intermedias) y **NAT64** (traducir direcciones entre los dos protocolos para que un host IPv6 hable con un servicio IPv4).

## IPv4-Mapped IPv6 Address

Una forma de **representar una IPv4 dentro de una dirección IPv6**, útil para que sockets y APIs trabajen con un único formato.

```
::ffff:<IPv4 Address>
```

Ej.: `::ffff:192.168.1.1`. Los primeros 80 bits en 0, los siguientes 16 en 1, los últimos 32 son la IPv4. Prefijo: `::ffff:0:0/96`.

> Esto **no permite enrutamiento** entre redes — sólo es una notación. Para que un host IPv6 hable con un servicio IPv4 hay que usar NAT64 o dual stack.

## NAT64

**Network Address Translation 64** traduce paquetes IPv6 a IPv4 (y viceversa) en un dispositivo de borde. El host IPv6 le manda paquetes a una **dirección IPv6 sintética** (con un prefijo NAT64 + la IPv4 real codificada en los últimos 32 bits); el NAT64 los desencapsula y los manda como IPv4 al servidor real.

- Suele ir acompañado de **DNS64**, que sintetiza registros AAAA para hosts que sólo tienen A.
- Permite que una red IPv6-only acceda a servicios IPv4-only.

## Tunneling

**Encapsular paquetes de un protocolo dentro del payload de otro**, para que viajen por una red que sólo soporta el otro protocolo. En el contexto de transición, lo más común es **IPv6-en-IPv4**: un router en el borde de una isla IPv6 encapsula los paquetes IPv6 en paquetes IPv4 normales, los manda por la red IPv4 hasta el otro extremo, donde se desencapsulan.

```
[ Red IPv6 ] ─→ [ Router A ] ──── (IPv4 con IPv6 adentro) ──→ [ Router B ] ─→ [ Red IPv6 ]
```

### Tipos de túneles

- **Túneles configurados (manuales):** ambos extremos están preconfigurados.
- **Túneles automáticos** (6to4, ISATAP): los extremos se descubren dinámicamente.
- **Túneles de protocolos desconocidos:** encapsular protocolos no soportados nativamente por la infraestructura, dentro de un protocolo compatible (ej.: encapsular cualquier protocolo dentro de IP para que viaje por Internet).
- **IP-en-IP:** caso particular donde se encapsulan paquetes IP (cualquier versión) dentro de otros paquetes IP.

### IP-en-IP

Encapsulación clásica usada también para VPNs y transición. El paquete original (IPv6) se mete entero en el payload de un paquete IPv4 nuevo, con su propio header. El receptor lo desencapsula.

## Estrategias resumidas

| Estrategia | Cómo funciona | Cuándo |
|------------|---------------|--------|
| **Dual stack** | El host habla v4 y v6 simultáneamente | Transición temprana, mientras haya servicios v4 |
| **Tunneling** | Encapsular v6 en v4 (o al revés) | Atravesar una red intermedia que sólo habla un protocolo |
| **NAT64 + DNS64** | Traducir headers entre v4 y v6 | Red IPv6-only que necesita acceder a servicios IPv4 |

## Aparece en parciales

- *Guía 5, Ej. 24:* dos hosts en distintas redes (uno IPv4, otro IPv6) pueden hablar UDP/TCP **si hay un router que cree un túnel u otro mecanismo de traducción**.
- *Guía 5, Ej. 27:* un host con un protocolo de red distinto (IPv4 vs IPv6) puede comunicarse con otro **si hay un firewall intermedio que implemente NAT64**. Para protocolos de transporte distintos (ej.: TCP vs UDP) **no hay forma directa**.
- *Preguntas teóricas, Ej. 27:* dos hosts IPv6 pueden comunicarse aunque algún router intermedio sea IPv4-only — usando **tunneling**, que encapsula IPv6 en IPv4 para atravesar ese tramo.

> Fuente: Apuntes oficiales, Unidad 6, p. 97-99.
