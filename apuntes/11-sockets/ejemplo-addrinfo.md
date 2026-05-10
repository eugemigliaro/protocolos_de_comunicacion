# Ejemplo `addrinfo.c`

Programa de ejemplo para resolver nombres de host y servicios a direcciones IP usando `getaddrinfo()` y mostrar info sobre las direcciones que devuelve.

## Firma de `getaddrinfo`

```c
int getaddrinfo(const char *node,         // FQDN o IP en string (puede ser NULL)
                const char *service,      // puerto numérico ("80") o servicio ("http")
                const struct addrinfo *hints,
                struct addrinfo **res);   // lista enlazada de resultados
```

- `service` puede ser un número de puerto (`"80"`) o un nombre de servicio (`"http"`, `"smtp"`); el sistema lo resuelve al puerto por defecto.
- Para `localhost` se pueden devolver dos estructuras: `127.0.0.1` y `::1`.
- `hints` guía al SO sobre qué tipo de socket buscar.
- `res` queda apuntando a la **primera** estructura `addrinfo` de una lista enlazada.

## Estructura `addrinfo`

```c
struct addrinfo {
    int              ai_flags;
    int              ai_family;     // AF_INET, AF_INET6, AF_UNSPEC
    int              ai_socktype;   // SOCK_STREAM, SOCK_DGRAM
    int              ai_protocol;
    socklen_t        ai_addrlen;
    struct sockaddr *ai_addr;
    char            *ai_canonname;  // nombre canónico del host (con AI_CANONNAME)
    struct addrinfo *ai_next;       // siguiente nodo de la lista
};
```

## Flags posibles (`ai_flags`)

- `AI_CANONNAME` → llena `ai_canonname` con el nombre canónico del host.
- `AI_PASSIVE` → para sockets pasivos (servidor); si `node` es NULL, usa la dirección "any".
- `AI_NUMERICHOST` → no resolver, `node` ya es IP numérica.
- `AI_NUMERICSERV` → `service` ya es numérico.

## Pasos del programa

1. **Lee argumentos de línea de comandos:** un host obligatorio (ej. `google.com`) y un servicio opcional (`http`, `ftp`, `80`...).
2. **Prepara el hint:** `memset` para inicializar en cero. Activa `AI_CANONNAME`.
3. **Llama a `getaddrinfo()`:** resuelve el host (y opcionalmente el servicio) y guarda la lista en `ailist`.
4. **Recorre la lista** con `for (aip = ailist; aip != NULL; aip = aip->ai_next)`. Imprime:
   - flags (`printFlags`)
   - familia IPv4/IPv6 (`printFamily`)
   - tipo de socket TCP/UDP (`printType`)
   - protocolo (`printProtocol`)
   - nombre del host (`ai_canonname`)
   - dirección y puerto (`printAddressPort`)
5. **Libera memoria** con `freeaddrinfo(ailist)`.

> Las funciones `print*` están en `util.c`.

## Por qué iterar la lista

`getaddrinfo` puede devolver **varias** direcciones. Si la primera IP no funciona (ej. servidor con 3 IPs públicas y una está caída), hay que probar la siguiente. Por eso el patrón general es **recorrer la lista hasta que una funcione**.

## Variante asincrónica

Existe una variante asincrónica de `getaddrinfo` para no bloquearse — útil si la usamos en un servidor, donde la versión bloqueante sería problemática. En el cliente se puede usar la bloqueante sin problema, porque al cliente no le molesta esperar la respuesta.

## Fuente

Apunte oficial Unidad 11, p. 138-140.
