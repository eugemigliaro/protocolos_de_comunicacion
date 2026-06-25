# Análisis de la resolución del grupo anterior (nash)

Fuente: `practica/resolucion-tpe-nash/tpe-protos-main/` (Grupo 3, cuatrimestre anterior, consigna
similar). **Uso: referencia conceptual únicamente.**

## Nota de integridad académica

> Es trabajo de **otro grupo**. NO se copia código. La consigna exige poder **defender** cada línea
> y que cada integrante demuestre su participación; copiar reprueba el TP. Lo único reutilizable son
> las **utilidades de cátedra** (selector, stm, buffer, parser de args), permitidas **con atribución**
> y que nash también tomó de la cátedra. Todo lo nuestro lo escribimos nosotros.

## Qué hace bien (vale imitar el enfoque, no el código)

- **Separación por capas**: `src/server` (lógica), `src/client` (cliente de monitoreo),
  `src/shared` (común: args, logger, util). Coincide con lo que pide la cátedra.
- **Máquina de estados explícita** en `src/server/states/`: `state_hello`, `state_auth`,
  `state_request`, `state_resolve`, `state_connect`, `state_forward`. Es exactamente el camino
  SOCKS5: negociación de método → auth user/pass → request → resolución → connect → relay.
- **Utilidades de cátedra** presentes: `selector.c` (event loop no bloqueante), `stm.c` (state
  machine genérica), `buffer.c` (buffer circular para parciales). Son las que se pueden reutilizar.
- **Makefile** con `Makefile.inc`, regla patrón `obj/%.o: src/%.c`, `.PHONY`, separación de
  fuentes server/client/shared. Buena base para el nuestro.
- **Métricas y monitoreo** en `metrics.c` + `monitor_handler.c`, cliente en `monitor_client.c`.
- **Protocolo de monitoreo textual** documentado en `doc/protocol_map.md` (STATS, CONNECTIONS,
  USERS, CONFIG, ACCESS_LOG, QUIT) con handshake de credenciales y ejemplos de conversación.

## Decisiones de ellos a discutir (no copiar a ciegas)

- Protocolo de monitoreo **textual** sobre TCP. Cómodo de debuggear con ncat, pero la consigna
  valora justificar texto vs binario. Decidir nosotros y documentar en `DECISIONS.md`.
- Flags: `-l/-p` (SOCKS addr/port), `-L/-P` (config addr/port), `-u user:pass`, `-v`. Razonable y
  alineado con POSIX; sirve de referencia para nuestro propio parser.
- Límite de 10 usuarios por `-u`. Decisión nuestra a tomar.
- Auth del canal de monitoreo: ellos piden `usuario:password` al conectar. Revisar si es robusto.

## Qué mejorar / vigilar respecto de ellos

- **Robustez RF4** (FQDN con múltiples IPs → probar todas si una falla): verificar que nuestro
  `state_resolve`/`state_connect` realmente itere sobre toda la lista de `getaddrinfo`.
- **Reply codes completos (RF5)**: usar todos los códigos SOCKS5 (no solo success/general failure).
- **getaddrinfo sin bloquear (C3)**: la resolución debe ir en thread aparte que solo resuelve y
  despierta al main, o `getaddrinfo_a`. Confirmar que no bloquea el event loop.
- **Graceful shutdown (RF9)**: confirmar manejo de SIGTERM/SIGINT con espera de conexiones activas.
- **Cliente de monitoreo (RNF5)**: que sea UX cómoda (`client add-user ...`), no un wrapper de netcat.

## Cómo lo usamos

1. Leer un `state_*.c` de nash para entender **qué hace** cada estado y sus transiciones.
2. Diseñar nuestra propia máquina de estados (documentarla en `doc/DESIGN.md` con diagrama ASCII).
3. Escribir nuestro código desde cero, apoyándonos en las utilidades de cátedra con atribución.
4. Comparar al final contra `doc/protocol_map.md` solo para chequear que no nos olvidamos features.
