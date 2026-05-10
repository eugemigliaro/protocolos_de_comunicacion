# Ejemplo Cliente/Servidor TCP (ECHO)

Servicio **ECHO**: el servidor repite por TCP lo que el cliente le envía.

Archivos involucrados:
- `tcpEchoClient.c` — cliente
- `tcpEchoServer.c` — servidor iterativo
- `tcpClientUtil.c`, `tcpServerUtil.c` — utilidades

## Cliente (`tcpEchoClient.c`)

Se conecta a un servidor (nombre/IP + puerto), le envía un string, espera el eco e imprime la respuesta.

### `tcpClientSocket()` (en `tcpClientUtil.c`)

Crea el socket cliente TCP y establece la conexión:

1. **Resolver dirección** con `getaddrinfo` (host + puerto).
2. **Recorrer la lista de resultados.** Para cada nodo:
   - Crear el socket con `socket()`. Puede fallar si los argumentos son inconsistentes (ej. pedir TCP con protocolo UDP) — en este caso el código siempre forma bien los argumentos, así que solo falla si **no quedan recursos**.
   - Intentar `connect()` al servidor.
   - Si la conexión funciona → devolver el FD.
   - Si no → cerrar y probar el siguiente nodo.
3. Si **ningún** nodo funciona, devolver -1.
4. **Liberar la lista** con `freeaddrinfo` antes de retornar (en ambos casos).

### Flujo del cliente luego de obtener el socket

```
send(sockfd, mensaje, len, 0)        // bloqueante por defecto, sin flags
                                      // devuelve negativo si hay error
while (bytes_recibidos < len_enviado) {
    n = recv(sockfd, buf, ..., 0);   // bloqueante por defecto
    if (n == 0) break;               // la otra punta hizo close
    bytes_recibidos += n;
}
```

> `recv` es bloqueante porque al principio no recibió nada.
> Si `recv` devuelve **0**, es porque la otra punta hizo `close`.

## Servidor (`tcpEchoServer.c`)

Recibe el puerto donde escuchar y entra en un **ciclo infinito** atendiendo conexiones.

### `setUp(servicio)` (en `tcpServerUtil.c`)

Recibe el servicio (ej. puerto numérico o `"ECHO"`).

Flags del `hints` para `getaddrinfo`:
- `AI_PASSIVE` → sockets pasivos.
- `AI_NUMERICSERV` → el servicio se interpreta como número.
- familia `AF_UNSPEC`, `SOCK_STREAM`.

> El primer argumento de `getaddrinfo` (el `node`) se pasa **NULL**: queremos crear sockets pasivos, no escuchar solo en una IP. Devuelve estructuras con IP "vacía" (any). Si quisiéramos escuchar solo localmente, pasaríamos `"localhost"`.

Luego itera la lista (igual que el cliente):
1. Crea el socket.
2. `bind()` — puede fallar si:
   - El puerto ya está ocupado por otro proceso.
   - El puerto es **menor a 1024** y el proceso **no es root**.
3. `listen(sock, MAXPENDING)` — `MAXPENDING` es el tamaño de cola de conexiones pendientes. No debería fallar, pero se valida.
4. Si `bind` y `listen` funcionan → ese nodo sirve, devolver el FD.
5. Si ninguno funciona → -1.

### `acceptTCPConnection()`

Llama a `accept()`, que devuelve el **socket activo** sobre el cual leer/escribir con ese cliente.

### `handleTCPEchoClient()` — específico del servicio ECHO

```
1. recv() del cliente
2. send() de vuelta lo mismo
3. repetir mientras recv() > 0
4. cerrar el socket cuando recv() devuelve 0 (cliente cerró)
```

> **DoS trivial:** como el ciclo espera hasta `recv() <= 0`, basta con conectarse y nunca enviar nada para mantener al servidor (iterativo) ocupado.

## Iterativo vs concurrente

El servidor de este ejemplo es **iterativo**: atiende a un solo cliente a la vez. Para concurrencia se podría usar `fork()`, pero no es el enfoque elegido en la materia. El proyecto se piensa **escalable** — por ejemplo, primero se hace bloqueante y después se mejora.

## Fuente

Apunte oficial Unidad 11, p. 141-145.
