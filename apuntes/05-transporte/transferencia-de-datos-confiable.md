# Transferencia de Datos Confiable

Mecanismo general que usa **TCP** para garantizar que los bits no se alteran, los paquetes no se pierden y llegan en orden, **aunque la red subyacente sea no confiable**.

## ¿Qué hace falta para ser confiable?

Si la capa de red ya garantizara confiabilidad, no haría falta nada — pero IP no garantiza nada. Entonces, el protocolo de transporte debe:

1. **Numerar los paquetes** → para detectar pérdidas y desorden.
2. **Reordenar** los que llegan desordenados antes de pasarlos a la app.
3. **Reenviar** los que se pierden.
4. **Verificar bits** (checksum) para detectar corrupción.

## Mecanismo básico: ACK / NAK

- El receptor manda **ACK** cuando recibe correctamente.
- El receptor manda **NAK** (o no manda nada) si el paquete llegó corrupto.
- El emisor **retransmite** ante NAK o ante **falta de ACK** (timeout).

## Stop & Wait

Esquema más simple: el emisor manda un paquete y **espera el ACK** antes de enviar el siguiente.

```
emisor          receptor
  |─── pkt 0 ───►|
  |             |
  |◄── ACK 0 ───|
  |             |
  |─── pkt 1 ───►|
  |             |
  |◄── ACK 1 ───|
```

- Garantiza el orden trivialmente.
- Si no llega respuesta en un **tiempo máximo** (timeout), el emisor retransmite.
- **Muy ineficiente**: la mayor parte del tiempo el canal está ocioso.

## Pipelining (varios envíos sin esperar)

En la práctica se hacen **varios envíos seguidos** y el destino manda ACK indicando **hasta dónde recibió** (ACK acumulativo). Esto es lo que usa TCP — ver `tcp.md`.

## Números de secuencia

Sirven para:
- Que el receptor pueda **ordenar** los segmentos antes de pasárselos a la app.
- Detectar segmentos **duplicados**.
- Detectar **huecos** (qué segmentos faltan).

En TCP el número de secuencia se cuenta **en bytes**, no en segmentos: cada segmento tiene `seq = seq_anterior + cantidad_de_bytes_de_data_anterior`.

## Timers

Cada segmento enviado tiene un **timer** asociado. Si vence antes del ACK correspondiente, se asume pérdida y se **retransmite**. El valor del timer no es fijo — se estima a partir del **RTT** medido (ver `tcp.md`).

## Aparece en parciales

- "¿Para qué se utilizan números de secuencia en un protocolo confiable?" → Para entregar los datos **en orden** a la capa de aplicación y **detectar duplicados** (guía 4 ej. 9).
- "¿Para qué se utilizan timers en un protocolo confiable?" → Para detectar que un ACK no llegó en tiempo razonable y **reenviar** el segmento (guía 4 ej. 10).
- "En TCP, si de un segmento enviado no se recibe el ACK en un tiempo determinado, ¿qué sucede?" → **Se retransmite el paquete** (guía 4 ej. 4; preguntas-teoricas ej. 41).

## Fuente

- Apunte oficial, Unidad 5, p. 55-56.
- Guía 4 (Transporte), ej. 4, 9, 10.
