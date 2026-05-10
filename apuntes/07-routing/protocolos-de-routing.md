# Protocolos de Routing

Los **protocolos de routing** son los mecanismos que usan los routers para intercambiar información sobre las rutas y armar/actualizar sus tablas de enrutamiento. No hay que confundirlos con el acto de rutear en sí.

## Routing vs forwarding

Dos cosas distintas que conviene tener separadas:

- **Routing / forwarding**: el acto concreto de reenviar un paquete a un host basándose en lo que dice la tabla de ruteo. Es lo que pasa por cada paquete.
- **Protocolos de routing**: los programas que corren en los routers e intercambian información entre sí para *construir* esas tablas. Es lo que pasa "por debajo" para que el forwarding tenga datos sobre los que decidir.

## Configuraciones de routing

Tres configuraciones típicas (Unidad 7, p. 104):

- **Routing mínimo**: una red aislada de otras redes TCP/IP. La tabla casi no necesita entradas porque no hay otras redes a las que rutear.
- **Routing estático**: la tabla la construye un administrador a mano. Se usa cuando la topología es chica y estable, o cuando se quiere control absoluto sobre por dónde sale el tráfico.
- **Routing dinámico**: la tabla se construye con la información que intercambian protocolos de routing (RIP, OSPF, BGP, etc.). Ver `routing-dinamico.md`.

Además de estas tres, las tablas también se pueden actualizar a partir de paquetes **ICMP Redirect**.

## ICMP Redirect

Mecanismo de actualización de tablas que no requiere correr un protocolo de routing dinámico. Cuando un router recibe un paquete y se da cuenta de que el host que lo originó podría haber usado un *next hop* mejor (otro router en la misma red), le manda un ICMP Redirect avisándole. El host actualiza su tabla y, de ahí en más, manda el tráfico para ese destino directo al next hop óptimo.

Apunte oficial muestra el formato del paquete (Unidad 7, p. 104).

## Cuándo usar cada uno

| Caso | Configuración recomendada |
|------|---------------------------|
| Red aislada, sin salida a otras redes | Mínimo |
| Pocos routers, topología fija | Estático |
| Red con varios caminos posibles, topología cambiante | Dinámico |

## Aparece en parciales

- Guía 6, ej. 1.1: el routing que usa **ingreso manual de datos** en la tabla de forwarding es el **estático**.
- Guía 6, ej. 1.2: el objetivo de un protocolo de routing es **determinar la ruta óptima** desde un router origen a una red destino (no forwardear, eso lo hace el router con la tabla ya armada).

## Referencias

- Apunte oficial Unidad 7, p. 104.
- Guía 6 — Routing, ej. 1.
