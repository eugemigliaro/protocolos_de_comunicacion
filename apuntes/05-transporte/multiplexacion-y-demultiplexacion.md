# Multiplexación y Demultiplexación

Cómo la capa de transporte le entrega a **muchos procesos** los datos que viajan **sobre la misma máquina**.

## Multiplexación (lado emisor)

El host recibe datagramas IP, donde cada uno tiene IP origen, IP destino, y un segmento de transporte con **puerto origen** y **puerto destino**. El emisor toma datos de varios sockets (varios procesos) y los empaqueta en segmentos antes de bajárselos a la capa de red.

## Demultiplexación (lado receptor)

El receptor usa la combinación **IP + puerto** para dirigir cada segmento al **socket correcto**.

### En UDP

El socket se identifica por el par `<IP destino, puerto destino>`. Los datagramas que llegan con **distinto IP origen** son atendidos por **el mismo socket** (la aplicación se entera del IP origen leyéndolo del datagrama).

### En TCP / SCTP (orientado a conexión)

El socket se identifica por la **cuádrupla**:
- IP origen
- Puerto origen
- IP destino
- Puerto destino

Conexiones simultáneas → cada una opera con **su propio socket**. Aunque el puerto destino sea el mismo (ej. 80 en un web server), las cuádruplas distintas garantizan que cada conexión va a un socket distinto. Ver `demultiplexacion-orientada-a-conexion.md`.

## Puertos

- Rango 0-65535 (16 bits).
- **Well-known**: 0-1023 (asignados por IANA: 80 HTTP, 443 HTTPS, 25 SMTP, 53 DNS, 22 SSH...).
- **Registered**: 1024-49151.
- **Dinámicos / efímeros**: 49152-65535.

Casi todos los puertos están definidos tanto para TCP como para UDP, aunque la aplicación use solo uno.

## Aparece en parciales

- "Suponga que un proceso en el host C tiene un socket UDP con puerto 6789. Los hosts A y B envían un segmento UDP a C con puerto destino 6789. ¿Ambos serán dirigidos al mismo socket? ¿Cómo distingue C el origen?" → **Sí**, ambos al mismo socket UDP. C los distingue por **IP y puerto de origen** (guía 4 ej. 7).
- "¿Qué utilizan TCP, SCTP y UDP para mantener un registro de las distintas conversaciones?" → la **cuádrupla** IP origen, puerto origen, IP destino, puerto destino (guía 4 ej. 3).

## Fuente

- Apunte oficial, Unidad 5, p. 52-53.
- Guía 4 (Transporte), ej. 3, 7.
