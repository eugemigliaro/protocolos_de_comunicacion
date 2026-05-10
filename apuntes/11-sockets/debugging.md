# Debugging de Sockets

Para encontrar errores en programas con sockets, no alcanza con un debugger tradicional: hay que ver las **system calls** que el proceso le hace al kernel (qué `socket`, `bind`, `connect`, `recv`, `send` se ejecutan, con qué argumentos y qué devuelven).

## Herramientas según el SO

| SO | Herramienta |
|----|-------------|
| GNU/Linux | `strace` |
| Solaris, FreeBSD | `truss` |
| macOS | `dtruss` |

## Uso típico de `strace`

```
strace ./tcpEchoServer 8080
strace -e trace=network ./tcpEchoServer 8080   # solo syscalls de red
```

Muestra cosas como:
```
socket(AF_INET, SOCK_STREAM, IPPROTO_TCP) = 3
bind(3, {sa_family=AF_INET, sin_port=htons(8080), ...}, 16) = 0
listen(3, 5) = 0
accept(3, ...) = 4
recv(4, "hola", 1024, 0) = 4
send(4, "hola", 4, 0) = 4
```

Sirve para detectar:
- `bind` fallando con `EADDRINUSE` (puerto ya en uso).
- `connect` fallando con `ECONNREFUSED`.
- `recv` devolviendo 0 (el otro lado cerró) vs -1 (error).

## Filosofía del proyecto

La idea es hacerlo **escalable**: empezar simple (bloqueante, iterativo) y luego mejorar.

## Fuente

Apunte oficial Unidad 11, p. 146.
