# Control de Congestión

Mecanismo de TCP que entra en juego cuando **la red** está dropeando paquetes (no el receptor — eso es control de flujo). Causa típica: cuellos de botella en algún router intermedio.

## ¿Cómo se entera TCP de que hay congestión?

TCP **no recibe ningún feedback explícito** de la red. Detecta la congestión solamente porque vencen los **timeouts** de retransmisión: si los ACK no llegan en tiempo, se asume que algún router descartó los segmentos.

Por eso el RFC del header TCP no tiene un campo dedicado a control de congestión — se basa en **información local** (qué se perdió). Lo único que se puede aprovechar del header es el campo **Options** para ajustar el RTT dinámicamente.

## Algoritmo: Additive Increase / Multiplicative Decrease (AIMD)

El emisor mantiene una **ventana de congestión** (cwnd) interna que limita cuánto puede enviar (en conjunto con la ventana del receptor).

### Additive Increase

Mientras no haya pérdidas, el emisor **aumenta linealmente** la ventana en relación al **MSS** (Maximum Segment Size) por cada **RTT**.

```
cwnd ─── ▁▂▃▄▅▆▇█  (subida lineal)
```

### Multiplicative Decrease

Apenas detecta pérdida (timeout), **divide la ventana a la mitad**.

```
cwnd ─── █▅  (caída a la mitad)
```

El gráfico típico de AIMD es un **diente de sierra**:

```
cwnd
 │     /\        /\        /\
 │    /  \      /  \      /  \
 │   /    \    /    \    /    \
 │  /      \  /      \  /      \
 │ /        \/        \/        \
 └────────────────────────────────► tiempo
```

## Diferencia con control de flujo

| | Control de flujo | Control de congestión |
|---|---|---|
| Causa | Buffer del **receptor** lleno | **Red** congestionada |
| Feedback | Explícito (campo Window) | Implícito (timeouts) |
| Ámbito | End-to-end | Cada host emisor (decisión local) |
| Campo header | `Window` | Ninguno (solo Options para RTT) |

## Aparece en parciales

- "¿Qué campo del header TCP tiene para implementar control de congestión?" → **Ninguno**, se basa en información local (timeouts). Se puede usar **Options** para ajustar el RTT dinámicamente (guía 4 ej. 16).
- "Control de congestión es implementado por TCP para evitar enviar información a la otra punta que no puede procesar." → **Falso**: eso es **control de flujo**. Control de congestión es para evitar el dropeo de paquetes en la red (preguntas-teoricas ej. 17).

## Fuente

- Apunte oficial, Unidad 5, p. 60-61.
- Guía 4 (Transporte), ej. 16.
- RFC 5681.
