# Circuitos Virtuales

Un **circuito virtual (CV)** es un esquema de capa de red **con conexión**: antes de mandar datos, se establece un camino lógico entre origen y destino, y todos los paquetes lo siguen en orden. Cada paquete lleva un identificador de CV y cada router en el camino mantiene estado por cada CV. **IP no usa circuitos virtuales** (es por datagramas).

## Fases

1. **Establecer la conexión** — cada router agrega una entrada a su tabla, opcionalmente reserva recursos (bandwidth, buffers).
2. **Transferencia de paquetes** — cada paquete viaja con el ID del CV; los routers ya saben por dónde mandarlo.
3. **Cierre de la conexión** — se libera el estado en cada router.

## Ventajas y costo

- Ventaja: garantiza orden, puede reservar recursos, headers más chicos (alcanza con el ID del CV).
- Costo: estado por CV en **todos** los routers del camino. Si un router se cae, se pierden todos los CV que lo atravesaban.

Por ese costo, **no se usa para Internet** — sería trivial tirar la red. Sí se usa donde las sesiones son cortas y los hosts son pocos: **redes Banelco / Link** para cajeros bancarios, ATM, Frame Relay, X.25.

## Comparación con conmutación de paquetes

| | Circuito virtual | Conmutación de paquetes (IP) |
|---|---|---|
| Conexión previa | Sí | No |
| Estado en routers | Sí (por CV) | No (sólo tabla de ruteo) |
| Orden | Garantizado | No (depende de capa superior) |
| Header por paquete | Chico (ID de CV) | Grande (IP origen, IP destino, TTL, etc.) |
| Falla de un router | Cae el CV | Sólo se pierden paquetes encolados |

> Fuente: Apuntes oficiales, Unidad 6, p. 71-72.
