# Direccionamiento IPv4

Una **dirección IP** es un número de 32 bits que identifica unívocamente una interfaz en una red IP. Se divide en dos partes: **Net ID** (qué red) y **Host ID** (qué dispositivo dentro de esa red). La división Net/Host se determina con la **máscara de subred** (o, modernamente, el prefijo CIDR).

## Notación

Se escribe como cuatro octetos decimales separados por puntos: `192.168.1.10`. Cada octeto va de 0 a 255 (8 bits).

## Clases de direcciones (esquema histórico)

| Clase | Primeros bits | Bits Net ID | Bits Host ID | Rango primer octeto |
|-------|---------------|-------------|--------------|---------------------|
| A     | `0`           | 7           | 24           | 0–127 |
| B     | `10`          | 14          | 16           | 128–191 |
| C     | `110`         | 21          | 8            | 192–223 |
| D     | `1110`        | — (multicast)| —           | 224–239 |
| E     | `1111`        | — (reservada)| —           | 240–255 |

A medida que se va de A a C, hay **más redes posibles** pero **menos hosts por red**. Las clases D y E no se usan para identificar hosts: D es multicast, E es experimental.

> Hoy las clases están **obsoletas** — se usa CIDR (ver `superredes.md`). Pero la pregunta de parciales muchas veces las da por dato.

## Máscara de subred

Cadena de 32 bits con los bits de **red en 1** y los de **host en 0**. Se aplica con un AND lógico contra la IP para obtener la dirección de red. Por defecto, según la clase:

| Clase | Máscara default | Notación CIDR |
|-------|-----------------|---------------|
| A     | 255.0.0.0       | /8            |
| B     | 255.255.0.0     | /16           |
| C     | 255.255.255.0   | /24           |

## Direcciones especiales (no asignables a hosts)

Dada una red, **no se puede asignar a un host**:

- La dirección de red (todos los bits de host en 0). Ej.: en `10.15.0.0/16`, la `10.15.0.0`.
- La dirección de broadcast (todos los bits de host en 1). Ej.: en `10.15.0.0/16`, la `10.15.255.255`.

Direcciones reservadas a nivel global:

- `127.0.0.0/8` — loopback (típicamente `127.0.0.1`).
- `255.255.255.255` — broadcast limitado.
- `224.0.0.0` a `239.255.255.255` — **multicast** (Clase D), sólo como destino.
- IPs privadas (RFC 1918): `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

## Tabla de ruteo

Todos los hosts y routers la usan. Cada entrada: red, máscara, gateway/interfaz. Si una IP matchea con más de una entrada → gana la **más específica** (la de máscara con más 1s — *longest prefix match*).

Si el router tuviera que tener una entrada por **cada** red posible (`2^7` clase A + `2^14` clase B + `2^21` clase C), el lookup sería intratable. Por eso se introdujo CIDR.

## Aparece en parciales

- *Guía 5, Ej. 5:* ID de red de 24 bits con CIDR → ninguna de las opciones cumple (la `/24` aplica máscara `255.255.255.0` y los últimos 8 bits deben ser 0).
- *Guía 5, Ej. 6:* IPs en la subred `192.168.15.16/28` → válidas: `.17`, `.29` (las `.16` y `.31` son red y broadcast; el resto cae fuera).
- *Guía 5, Ej. 14, 16, 17:* IPs válidas para hosts dada una máscara — se evalúa si los bits de host son todos 0 (red), todos 1 (broadcast), o caen fuera del rango.
- *Guía 5, Ej. 20:* entradas con IP que arrancan en 224 → multicast.
- *Preguntas teóricas, Ej. 4:* en `10.15.0.0/16` no se pueden asignar `10.15.0.0` (red) ni `10.15.255.255` (broadcast) ni nada fuera del `/16`.
- *Preguntas teóricas, Ej. 32:* subredes válidas de `10.15.0.0/16` — no vale `10.17.x.x` (fuera) ni `10.15.x.x/16` (igual prefijo que la red madre).
- *Preguntas teóricas, Ej. 11:* validar entradas de tabla de ruteo aplicando máscara.

> Fuente: Apuntes oficiales, Unidad 6, p. 73-75.
