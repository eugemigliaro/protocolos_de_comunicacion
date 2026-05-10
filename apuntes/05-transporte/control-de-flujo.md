# Control de Flujo

Mecanismo **end-to-end** de TCP que evita que el emisor sature el **buffer de entrada** del receptor. Tiene que ver únicamente con las **dos puntas** — no le importa la red.

## El problema

Cada extremo TCP tiene buffer de entrada y buffer de salida. Si el emisor manda más rápido de lo que la app del receptor lee, el buffer de entrada se llena → los datos que llegan se **dropean**. Hay que retransmitir desde el emisor → desperdicio.

## Solución: campo Window

El receptor le informa al emisor **cuántos bytes libres** tiene en su buffer de entrada usando el campo `Window` del header TCP (16 bits).

> "El receptor controla al emisor, de modo que el emisor no sobrecargue el buffer del receptor."

El emisor nunca puede tener más de `Window` bytes "en vuelo" sin confirmar.

```
emisor                          receptor
  |─── data (bytes 0-499) ────►|  buffer libre = 64KB
  |                            |  → Window = 65000
  |◄── ACK 500, Window 65000 ──|
  |                            |
  |─── data (bytes 500-999) ──►|  app no leyó, buffer ocupado
  |                            |  → Window = 64500
  |◄── ACK 1000, Window 64500 ─|
```

Cuando `Window = 0` el emisor **frena** y espera a que el receptor reanuncie un valor distinto.

## Limitación de los 16 bits y Window Scaling

El campo `Window` es de **16 bits** → buffer máximo anunciable de **64 KB** sin escalado. Era razonable cuando el ancho de banda máximo eran ~56 Kbps; hoy día se llena instantáneamente y **subutiliza el canal** (el emisor termina esperando ACKs en vez de seguir transmitiendo).

### Solución: TCP Window Scaling (RFC 7323)

En el three-way handshake inicial se intercambia una **opción** (en el campo Options del header) que indica un **factor de shift**: la cantidad de bits a la izquierda con que multiplicar el valor del Window.

- El factor `n` va de **0 a 14**.
- Window efectiva = `Window * 2ⁿ`.
- Con n=14 → ventana máxima de **2³⁰ bytes = 1 GB**.

El factor solo se negocia en el handshake inicial y aplica a toda la conexión.

## Otra técnica complementaria

Estimar el RTT con timestamps y usarlo para ajustar dinámicamente el comportamiento (también está en RFC 7323 — "PAWS" y "Timestamps option").

## Aparece en parciales

- "¿Qué campo del header tiene TCP para implementar control de flujo?" → el campo **Window** de 16 bits (guía 4 ej. 15).
- "El campo window es de 16 bits → buffer máximo 64KB. ¿Es aceptable hoy? ¿Hay forma de manejar buffers más grandes?" → **No** es aceptable (subutiliza el ancho de banda); se usa **TCP Window Scaling** que multiplica por 2ⁿ con n ∈ [0,14] (guía 4 ej. 17; preguntas-teoricas ej. 9).
- "Control de congestión es implementado por TCP para evitar enviar información a la otra punta que no puede procesar." → **Falso**: eso es **control de flujo**, no de congestión (preguntas-teoricas ej. 17).

## Fuente

- Apunte oficial, Unidad 5, p. 59-60.
- Guía 4 (Transporte), ej. 15, 17.
- RFC 7323.
