# Ejemplo Cliente/Servidor UDP (ECHO)

Servicio **ECHO** sobre UDP: el servidor responde con el datagrama que recibió.

Archivo: `udpEchoClient.c`.

## Diferencia clave con la versión TCP

En el cliente UDP **no se itera la lista** que devuelve `getaddrinfo`.

**¿Por qué?** Porque con UDP **es imposible saber si la información llegó o no** — no hay establecimiento de conexión ni ACK que confirme entrega. Si la primera dirección no anduvo, no nos enteraríamos: `sendto` devuelve éxito apenas el datagrama sale a la red.

Entonces:
- Si la lista no es vacía → se devuelve el socket sobre el **primer** valor de la lista.
- No tiene sentido probar la segunda dirección "si la primera falla", porque no se puede detectar ese fallo.

## Esquema general (recordatorio de [`./creacion-de-sockets.md`](./creacion-de-sockets.md))

### Servidor UDP

```
1. socket(AF_INET, SOCK_DGRAM, 0)
2. bind(sock, IP, puerto)
3. while (true) {
       recvfrom(sock, buf, ..., &cliente_addr, &len);
       sendto  (sock, buf, ..., &cliente_addr, len);
   }
```

No hay `listen` ni `accept`: UDP no es orientado a conexión. La dirección del cliente sale como parámetro de salida de `recvfrom` y se reusa en `sendto`.

### Cliente UDP

```
1. getaddrinfo(host, puerto, hints, &res)
2. socket sobre res (sin iterar)
3. sendto(sock, mensaje, ..., res->ai_addr, res->ai_addrlen)
4. recvfrom(sock, buf, ...)
```

## Bloqueo

`recvfrom` también es **bloqueante** por defecto: si nunca llega nada, el proceso queda esperando indefinidamente. Esto es problemático en el cliente UDP, porque si el datagrama de ida o de vuelta se pierde, el cliente queda colgado sin saberlo. Soluciones típicas:
- Timeout (`SO_RCVTIMEO` con `setsockopt`).
- Reintentos.
- Sockets no bloqueantes + `select`/`poll`.

## Fuente

Apunte oficial Unidad 11, p. 146.
