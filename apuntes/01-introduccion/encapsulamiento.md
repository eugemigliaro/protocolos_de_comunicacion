# Encapsulamiento

**Encapsulamiento** es el proceso por el cual cada capa, al recibir datos de la capa superior, les agrega su propio encabezado (y a veces *trailer*) antes de pasarlos a la capa inferior. Cuanto más confiable y completo el protocolo de una capa, **más metadata se agrega**.

## Cómo se arma un mensaje (ejemplo: enviar un mail)

Tomando como ejemplo enviar un correo electrónico:

```
Aplicación   |                 [DATOS]                                |
Transporte   |        [Hdr Trans][DATOS]              ]               |  → segmento
Red          | [Hdr IP][Hdr Trans][DATOS]                             |  → paquete
Enlace       | [Hdr Eth][Hdr IP][Hdr Trans][DATOS][Trailer Eth]       |  → trama (frame)
```

1. **Aplicación** entrega un bloque de datos a transporte.
2. **Transporte** le agrega su encabezado y forma un **segmento**. Si el protocolo de transporte es confiable, este encabezado tiene mucha más metadata (números de secuencia, ACKs, ventanas).
3. **Red** agrega su propio encabezado y forma un **paquete**.
4. **Enlace** agrega encabezado (y trailer) y forma una **trama** (*frame*).

## Nombres por capa

| Capa | Nombre de la PDU |
|------|------------------|
| Aplicación | Mensaje / datos |
| Transporte | Segmento (TCP) / Datagrama (UDP) |
| Red | Paquete / datagrama IP |
| Enlace | Trama (frame) |
| Física | Bits |

## Por qué importa

- Cada capa **solo lee su propio encabezado** (más o menos). No mira hacia adentro.
- Esto permite que un router, que opera a nivel de red, no tenga que entender el contenido de transporte.
- Por la misma razón, un switch (capa enlace) **no puede** mirar el TTL del header IP: no es de su capa.

## Aparece en parciales

- **Ej 40 (preguntas)**: "El TTL en IP existe para evitar que un paquete circule indefinidamente cuando hay switches conectados en ciclo." → **FALSO**. El switch no analiza headers IP — están en distintas capas. El TTL protege contra ciclos a nivel de red (entre routers), no de enlace.

## Referencias

- PDF, Unidad 1, p. 8.
