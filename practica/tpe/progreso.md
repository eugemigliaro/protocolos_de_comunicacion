# Progreso — TPE SOCKS5

Estado vivo. Actualizar al cerrar cada bloque de trabajo.

## Estado general

- **Fase actual:** Fase 0 cerrada → lista para arrancar **Fase 1 (SOCKS5 core)**.
- **Repo entregable:** `practica/tpe_protos_g14/`, pusheado a `origin/main`.
- **Última actualización:** scaffold + toolkit de cátedra integrado.

## Hecho

- [x] Repo del grupo clonado en `practica/tpe_protos_g14/` y gitignoreado desde el repo de estudio.
- [x] Contexto curado en `practica/tpe/` (consigna, requisitos, apuntes de clase, análisis nash, plan).
- [x] Scaffold del repo entregable (estructura, Makefile, build, esqueletos de doc). Commit inicial.
- [x] Toolkit de cátedra integrado vía `git am` (buffer, netutils, parser, parser_utils, selector,
      stm) → `src/server/utils/`, atribuido a la cátedra en la historia git. Tests en `test/`.
- [x] Parser de args oficial (`args.c/.h`) integrado en `src/server` y usado por `server.c`.
- [x] `make all` compila y ambos binarios corren. Repo pusheado a GitHub.
- [x] Material de cátedra en `material/tpe-programacion/` (parches, args, makefile, example).

## En curso

- [ ] Arrancar **Fase 1 — SOCKS5 core** (ver `plan.md`).

## Pendientes menores

- Completar `version()` en `src/server/args.c` (dice "Grupo X", "2025/1", "AQUI VA LA LICENCIA").
- Completar integrantes en `README.md` del repo del grupo.
- Decidir qué hacer con los 2 warnings de cátedra bajo `-Wextra` (dejar / relajar flag / adaptar).

## Decisiones abiertas (a tomar con el grupo)

- Pipelining en el protocolo de monitoreo (soportar o no; si no, aclararlo en SPEC).
- Tabla concreta de reply codes SOCKS5 → causa de fallo.
- Tamaños de buffer y máximos de campos del protocolo de monitoreo.

## Decisiones tomadas

- Repo entregable separado del de estudio, gitignoreado. (2026-06-25)
- Todos tienen push a ambos repos → `plan.md`/`progreso.md` quedan en el repo de estudio. (2026-06-25)
- Resolución de nash: solo referencia conceptual, no se copia código. (2026-06-25)
- Protocolo de monitoreo: **binario** (handshake de versión + LEN+STRING + status). (2026-06-25)
- Auth del canal de monitoreo: credencial de admin por flag + bind a 127.0.0.1 por default. (2026-06-25)
- `MAX_USERS = 10` (constante documentada). (2026-06-25)
- Event loop: **selector de cátedra** integrado vía parches `git am`, con atribución. (2026-06-25)
- Métricas volátiles en RAM; **access log persistido a archivo**. (2026-06-25)
- Código de cátedra (selector/buffer/stm/parser/parser_utils/netutils/test.h + args) se integra por
  `git am` y queda atribuido en la historia git. (2026-06-25)

Detalle y justificación: ver `tpe_protos_g14/doc/DECISIONS.md`.
