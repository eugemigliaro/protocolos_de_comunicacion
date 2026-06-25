# Plan de resolución — TPE SOCKS5

Repo entregable: `practica/tpe_protos_g14/` (git aparte). Referencias: `requisitos.md`,
`apuntes-clases-tp.md`, `analisis-nash.md`. Estado vivo en `progreso.md`.

## Arquitectura objetivo

- **Un solo thread**, event loop no bloqueante (selector tipo cátedra). Excepción: resolución de
  nombres en thread aparte / `getaddrinfo_a` (C3).
- **Estado por conexión** con buffers de entrada/salida y máquina de estados (`stm`).
- **Dos sockets pasivos** en el mismo programa: SOCKS5 (default 1080) y monitoreo (otro puerto).
- Layout: `src/server`, `src/client` (cliente de monitoreo), `src/shared` (común).

Máquina de estados SOCKS5 (a documentar en `doc/DESIGN.md`):
```
HELLO (negociación de método, RFC1928)
  -> AUTH (user/pass, RFC1929)        [si se negoció 0x02]
  -> REQUEST (parseo CONNECT + ATYP: IPv4/IPv6/FQDN)
  -> RESOLVE (getaddrinfo no bloqueante; lista de IPs)
  -> CONNECT (probar IPs en orden hasta una que ande — RF4)
  -> RELAY (copia bidireccional con read/write parciales, cierres de medio canal)
  -> CLOSING/CLOSED (liberar recursos)
```

## Fases

### Fase 0 — Scaffold y build  ← *este turno*
- [ ] Estructura `src/{server,client,shared}`, `bin/`, `obj/`.
- [ ] `Makefile` + `Makefile.inc` (all, server, client, clean, .PHONY, regla patrón, `-Wall -pedantic -std=c11 -g`).
- [ ] `.gitignore` (bin/, obj/).
- [ ] `server.c` y cliente "hello world" que compilen y corran (parseo de args mínimo + `-v`/`-h`).
- [ ] Esqueletos de `README.md`, `doc/SPEC.md`, `doc/DECISIONS.md`, `doc/DESIGN.md`.
- [ ] `CLAUDE.md` en el repo del grupo apuntando a este contexto.
- [ ] Verificar `make clean && make all`.

### Fase 1 — SOCKS5 core (RF2, RF3, RF5)
- [ ] Integrar utilidades de cátedra (selector, stm, buffer) con atribución + parser de args oficial.
- [ ] Socket pasivo no bloqueante + accept multiplexado.
- [ ] Estados HELLO → AUTH(RFC1929) → REQUEST.
- [ ] RESOLVE con `getaddrinfo` (IPv4/IPv6/FQDN).
- [ ] CONNECT y RELAY bidireccional con parciales.
- [ ] Reply codes completos.

### Fase 2 — Concurrencia y robustez (RF1, RF9, C3, C4)
- [ ] Soportar ≥500 conexiones concurrentes.
- [ ] `getaddrinfo` fuera del thread principal (no bloquear el loop).
- [ ] RF4: iterar todas las IPs resueltas.
- [ ] Graceful shutdown SIGTERM/SIGINT (2da señal = forzar).
- [ ] Timeouts de conexiones inactivas.

### Fase 3 — Métricas, monitoreo y cliente (RF6, RF7, RF8, RNF5)
- [ ] Métricas: históricas, concurrentes, bytes transferidos.
- [ ] Diseñar protocolo de monitoreo propio (decidir texto/binario y justificar) → `doc/SPEC.md` estilo RFC.
- [ ] Socket pasivo de monitoreo en otro puerto.
- [ ] Gestión de usuarios y config en runtime (add/del user, timeouts, etc.).
- [ ] Registro de accesos por usuario (sitio + timestamp).
- [ ] Cliente de monitoreo con UX cómoda (`client add-user pablito pass1234`).

### Fase 4 — Pruebas de estrés e informe (RNF3, entregables)
- [ ] Pruebas de carga: máx. conexiones simultáneas, degradación de throughput.
- [ ] Casos de prueba (válidos, inválidos, EOF, pipelining si aplica) en README.
- [ ] Informe PDF con las 11 secciones en orden.
- [ ] README raíz completo.

### 2da entrega
- [ ] RF10 — sniffer de credenciales POP3 tipo ettercap.

## Higiene de git (la historia se entrega)

- Commits chicos y descriptivos, en español, mensaje claro por feature/fase.
- Branch por feature si el grupo lo prefiere; mergear a `main`.
- No commitear binarios (`bin/`, `obj/`) ni el informe PDF si pesa (revisar `.gitignore` del repo del grupo).
- Atribuir explícitamente el código de cátedra reutilizado.
