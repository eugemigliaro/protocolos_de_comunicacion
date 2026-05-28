# Protocolos de Comunicación (72.07 — ITBA)

Repo de estudio para la materia. El idioma de trabajo es español.

## Workspaces

- `/inbox` — captura rápida durante la clase. Notas crudas, sin formato, sin clasificar.
- `/material` — material oficial de la cátedra (PDF de apuntes, guías teóricas, parciales viejos). Solo lectura, sirve como fuente de verdad.
- `/apuntes` — mis notas organizadas por unidad. Esta es la base de conocimiento curada.
- `/practica` — mis resoluciones de ejercicios y parciales.

## Unidades (según el PDF oficial de apuntes)

| # | Unidad | Carpeta |
|---|--------|---------|
| 1 | Introducción (capas, encapsulamiento, LAN, routers) | `apuntes/01-introduccion` |
| 2 | HTTP | `apuntes/02-http` |
| 3 | DNS y Mail (incluye SMTP, IMAP, MIME, SPAM) | `apuntes/03-dns-y-mail` |
| 4 | TLS/SSL (incluye HTTPS y proxy) | `apuntes/04-tls-ssl` |
| 5 | Transporte (UDP, TCP, control de flujo y congestión, NAT, firewall, SCTP) | `apuntes/05-transporte` |
| 6 | Red (IPv4, IPv6, DHCP, ICMP, IPSec, iptables) | `apuntes/06-red` |
| 7 | Routing (protocolos de ruteo dinámico) | `apuntes/07-routing` |
| 8 | Enlace (Ethernet, ARP) | `apuntes/08-enlace` |
| 10 | SSH (OpenSSH, port forwarding) | `apuntes/10-ssh` |
| 11 | Socket programming | `apuntes/11-sockets` |

(La unidad 9 no existe en el PDF oficial; se respeta la numeración de la cátedra.)

## Ruteo de tareas

| Tarea | Ir a | Leer primero |
|-------|------|--------------|
| Tomar nota en clase | `/inbox` | `CONTEXT.md` |
| "Organizá mis notas" | `/inbox` → `/apuntes/<unidad>` | `inbox/CONTEXT.md`, `apuntes/CONTEXT.md` |
| Estudiar / repasar un tema | `/apuntes/<unidad>` | `apuntes/CONTEXT.md` |
| Responder una pregunta teórica | `/apuntes/<unidad>` y, si falta info, `/material` | `apuntes/CONTEXT.md` |
| Resolver un ejercicio o parcial | `/practica` | `practica/CONTEXT.md` |

## Convenciones de nombres

- Notas crudas en `inbox/`: `YYYY-MM-DD-tema-corto.md` (la fecha es la del día de la clase).
- Archivos en `apuntes/<unidad>/`: nombre del concepto en kebab-case, en español. Ej: `three-way-handshake.md`, `dns-spoofing.md`, `control-de-congestion.md`.
- Resoluciones en `practica/`: `parcial-YYYYqN-tema.md` o `guia-N-ej-M.md`.

## Workflow para "organizá mis notas"

Cuando el usuario pida organizar el inbox:
1. Leer cada archivo en `inbox/`.
2. Para cada uno, identificar la unidad (ver tabla arriba) y el tema dentro de la unidad.
3. Apender el contenido al archivo correspondiente en `apuntes/<unidad>/<tema>.md` (crearlo si no existe), preservando la fecha original como subtítulo.
4. Mover el archivo procesado a `inbox/_archivado/` (no borrarlo) por si hace falta volver atrás.
5. Mostrar al usuario un resumen de qué nota fue a qué archivo, antes de tocar nada irreversible.

**Regla dura:** nunca se descarta contenido del inbox. Si una nota no encaja claramente en una unidad, se deja en el inbox y se pregunta al usuario.

## Reglas generales

- El material en `/material` es solo lectura. Nunca se edita ni se reescribe; se cita.
- Los binarios (`.pdf`, `.zip`, `.docx`) están gitignoreados. No commitearlos.
- Cuando se responda una pregunta usando el PDF de apuntes, citar la unidad y, si es posible, la página.
