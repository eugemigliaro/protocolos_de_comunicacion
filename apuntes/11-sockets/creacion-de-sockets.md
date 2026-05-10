# Creación de Sockets

```c
int socket(int domain, int type, int protocol);
```

Devuelve el **file descriptor** del socket (≥ 0) o **-1** si falla.

## Parámetros

### `domain` — naturaleza de la comunicación y formato de dirección

| Valor | Significado |
|-------|-------------|
| `AF_INET` | IPv4 |
| `AF_INET6` | IPv6 |
| `AF_UNIX` | Comunicación interna UNIX (no por TCP/IP) |
| `AF_UNSPEC` | Sin especificar (que el SO decida) |

### `type` — tipo de canal

| Valor | Uso |
|-------|-----|
| `SOCK_STREAM` | Canal confiable orientado a bytes (TCP) |
| `SOCK_DGRAM` | Datagramas (UDP) |
| `SOCK_RAW` | Paquete directamente sobre IP, sin transporte. Para mandar algo "mal formado" o particular que un protocolo de transporte modificaría |
| `SOCK_SEQPACKET` | No se usa en la materia |

### `protocol` — protocolo concreto

Generalmente **0**: el SO elige el default según `domain` + `type`. Si se quiere especificar:

- `IPPROTO_IP`, `IPPROTO_IPV6`
- `IPPROTO_ICMP`
- `IPPROTO_TCP`, `IPPROTO_UDP`, `IPPROTO_SCTP`
- `IPPROTO_RAW`

## Esquema TCP (analogía con teléfono)

| Operación | Analogía |
|-----------|----------|
| `socket()` pasivo | Tener el teléfono pero apagado |
| `bind(IP, puerto)` | Publicar el número de teléfono |
| `listen()` | Sacar el modo avión, ponerse a escuchar |
| `connect()` (cliente) | Llamar |
| `accept()` (servidor) | Atender la llamada |
| `send` / `recv` | Hablar |

> No se puede rechazar la conexión de una IP sin antes hacer `accept()` (que es bloqueante). `accept()` devuelve el FD del **socket activo** que se va a usar para esa conexión.

## Servidor TCP iterativo (orientado a conexión)

1. Crear un socket y ligarlo (`bind`) a un puerto.
2. `listen()` y aceptar un pedido de conexión (`accept`).
3. Obtener un nuevo socket (activo) para la conexión.
4. Leer un request del cliente (`recv`).
5. Enviar una respuesta (`send`).
6. Si el cliente no terminó, volver al paso 4.
7. Cerrar el socket creado en el paso 3.
8. Volver al paso 2.

> El `send` de TCP **es bloqueante** porque puede tener el buffer de salida ocupado.
> Un servidor **iterativo** atiende a un solo cliente a la vez. Para concurrencia, se usa `fork()` (no es la forma elegida en la materia) u otros mecanismos.

## Servidor UDP (no orientado a conexión)

1. Crear un socket y ligarlo (`bind`) a un puerto.
2. Leer un datagrama request de un cliente (`recvfrom`).
3. Enviar datagrama/s como respuesta (`sendto`).
4. Volver al paso 2.

## Cierre parcial: `shutdown`

La comunicación es bidireccional. `shutdown` permite deshabilitar I/O parcialmente (no se usa en la materia):

```c
int shutdown(int sockfd, int how);
```

| `how` | Cierra |
|-------|--------|
| `SHUT_RD` | Lectura |
| `SHUT_WR` | Escritura |
| `SHUT_RDWR` | Ambas |

## Direcciones — `sockaddr`

Una dirección identifica un endpoint. El formato es específico para cada familia y se castea a `struct sockaddr` (genérica) para pasársela a las syscalls.

```c
struct sockaddr_in {       // IPv4
    sa_family_t    sin_family;   // AF_INET
    in_port_t      sin_port;     // puerto en BIG ENDIAN
    struct in_addr sin_addr;     // IP
};

struct sockaddr_in6 {      // IPv6
    sa_family_t     sin6_family;
    in_port_t       sin6_port;
    uint32_t        sin6_flowinfo;  // flow label
    struct in6_addr sin6_addr;
    uint32_t        sin6_scope_id;
};
```

> **Cuidado con el endianness:** asignar `sin_port = 80` directamente está mal porque el estándar de red es big endian y la máquina puede ser little endian. Usar `htons()` / `htonl()`.

> El campo `flow` (IPv6) sirve para identificar una secuencia de paquetes que comparten características como IP origen/destino, puerto, protocolo, etc.

## Resolución de nombres (cliente que conoce solo el FQDN)

| Función | Qué hace |
|---------|----------|
| `inet_addr` | Convierte notación con puntos (`"200.132.2.15"`) a representación binaria |
| `gethostbyname` | Dado un FQDN, retorna estructura con la IP del host |
| `getaddrinfo` | Más completa que las anteriores. **La que se usa.** |

Ver [`./ejemplo-addrinfo.md`](./ejemplo-addrinfo.md) para detalles de `getaddrinfo`.

## Fuente

Apunte oficial Unidad 11, p. 135-138.
