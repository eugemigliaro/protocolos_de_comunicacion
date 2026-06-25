# Requisitos del TPE — checklist trazable

Extraídos de `consigna.md`. Nivel RFC2119 entre [corchetes]. Cada ítem tiene un id para
referenciarlo desde commits, el informe y `progreso.md`.

> Hay **dos entregas**. Lo marcado `[2da]` es solo para la segunda.

## 1. Requerimientos funcionales (servidor)

| id | Nivel | Requisito |
|----|-------|-----------|
| RF1 | DEBE | Atender múltiples clientes concurrente y simultáneamente (**≥ 500**). |
| RF2 | DEBE | Autenticación usuario/contraseña (**RFC1929**). GSSAPI NO es requisito. |
| RF3 | DEBE | Conexiones salientes TCP a **IPv4, IPv6 y FQDN** que resuelvan a esos tipos. |
| RF4 | DEBE | Robustez: si un FQDN resuelve a varias IPs y una falla, **intentar con las otras**. |
| RF5 | DEBE | Reportar fallos al cliente usando **todos los reply codes** del protocolo SOCKS5. |
| RF6 | DEBE | Métricas: conexiones históricas, conexiones concurrentes, bytes transferidos, + las que sumen. Pueden ser **volátiles**. |
| RF7 | DEBE | **Protocolo de monitoreo/config propio** en **otro puerto** (mismo programa): gestionar usuarios y cambiar config en runtime sin reiniciar. NO es extensión de SOCKS. |
| RF8 | DEBE | **Registro de accesos** por usuario (quién se conectó a qué sitio y cuándo). |
| RF9 | DEBE | **Graceful shutdown** con SIGTERM/SIGINT: dejar de aceptar nuevas conexiones, esperar a que terminen las activas. Una 2da señal PUEDE forzar el apagado. |
| RF10 | DEBE `[2da]` | Sniffer de credenciales (usuarios/passwords) tipo ettercap, al menos para **POP3**. |

## 2. Requerimientos no funcionales

| id | Nivel | Requisito |
|----|-------|-----------|
| RNF1 | DEBE | Lenguaje **C11** (ISO/IEC 9899:2011). |
| RNF2 | DEBE | Sockets **no bloqueantes multiplexados**. |
| RNF3 | DEBE | Performance/escalabilidad/disponibilidad: no cargar mensajes enormes en memoria, parser eficiente. El **informe DEBE** incluir pruebas de estrés (máx. conexiones simultáneas, degradación del throughput). |
| RNF4 | DEBE | Args de línea de comandos según **IEEE Std 1003.1-2008 (POSIX) / Utility Conventions**. La cátedra publica una implementación del parsing. |
| RNF5 | DEBERÁ | Documentar el protocolo de monitoreo e implementar un **cliente** propio. Puede usar I/O bloqueante. UX cómoda desde terminal (ej. `client add-user pablito pass1234`). **No se acepta** protocolo de texto + netcat como cliente. |
| RNF6 | DEBERÁ | Compilable con `make`, proveer **Makefile**. |
| RNF7 | DEBERÁ | Servidor y cliente manejan args de forma uniforme (ej. `-p <puerto>`). |
| RNF8 | PUEDE | Librerías/fragmentos de terceros solo si: (a) NO resuelven el fondo del TP, (b) licencia **OSI**, (c) **aprobadas por la cátedra** vía foro. Código de los docentes de la cursada actual: permitido con atribución. |
| RNF9 | — | Ante ambigüedad, tomar decisiones de diseño razonables y justificarlas. |

## 3. Condiciones de aprobación (todas obligatorias)

- C1 — Material **completo** (sin informe o sin código → no se corrige).
- C2 — Solo librerías permitidas para los usos definidos.
- C3 — **I/O no bloqueante correcta**: lecturas, escrituras y nuevas conexiones por **suscripción**, multiplexadas en **un único thread**.
  - Única excepción: resolución de nombres con `getaddrinfo` en un thread aparte que **solo** resuelve y despierta al thread principal (o usar `getaddrinfo_a`).
- C4 — Manejo correcto de **lecturas/escrituras parciales**.
- C5 — Sumar **≥ 4 / 10**.
- Entregas tardías: 0–24 h → nota máx. 4.

## 4. Entregables (mínimo)

- **Informe** (PDF o text/plain UTF-8) con secciones, **en orden**:
  1. Índice
  2. Descripción de protocolos diseñados y apps desarrolladas
  3. Problemas encontrados (diseño e implementación)
  4. Limitaciones
  5. Posibles extensiones
  6. Conclusiones
  7. Ejemplos de prueba
  8. Guía de instalación
  9. Instrucciones de configuración
  10. Ejemplos de configuración y monitoreo
  11. Documento de diseño (arquitectura)
  - El protocolo de monitoreo propio se describe **en estilo RFC** (agnóstico al lenguaje, completo para reimplementarlo). Justificar transporte, texto vs binario, serialización.
  - NO hace falta describir SOCKS5 en sí; enfocarse en lo que hicimos.
- **Código fuente + archivos de build.**
- **README en la raíz** con: ubicación de todo el material, cómo generar ejecutables, ubicación de artefactos, cómo ejecutar cada artefacto y sus opciones.
- Entrega = **tarball/zip del repo git clonado, con su historia.** → la historia de commits importa.
