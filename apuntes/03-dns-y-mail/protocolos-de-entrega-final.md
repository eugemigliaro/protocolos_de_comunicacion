# Protocolos de Entrega Final al Usuario

SMTP entrega el mail al servidor receptor, pero el usuario lo lee con un protocolo distinto. Hay dos principales: **POP3** e **IMAP**.

## POP3 (Post Office Protocol v3)

- Puerto **110**.
- Objetivo: **bajar los mensajes del servidor al host local** y guardarlos ahí.
- Modelo "buzón": el cliente baja todo y, por defecto, el server borra. Existen versiones que permiten **dejar copia** en el server.
- Pensado para un único cliente por casilla.

## IMAP (Interactive Mail Access Protocol)

- Puerto **143**.
- Pensado para que un usuario consulte su correo **desde varios hosts**.
- El servidor mantiene un almacenamiento central accesible desde cualquier cliente.
- El cliente IMAP **no copia** el correo al host local.
- Distingue mensajes leídos de no leídos.
- Permite crear carpetas en el servidor (organización del lado del server).
- El servidor puede hacer backups de los mails.

Algunos comandos típicos: `LOGIN`, `LIST`, `SELECT`, `FETCH`, `STORE`, `LOGOUT`.

## Webapps (webmail)

Antes de los webmails, IMAP era muy útil para ordenar mails en el servidor. Hoy un **webapp** es un MUA con interfaz web (Gmail, Outlook Web). Le da al usuario acceso al mailbox vía browser y reemplaza buena parte del valor de IMAP.

## POP3 vs IMAP — cuándo cada uno

| Caso | Mejor |
|------|-------|
| Un único dispositivo, quiero los mails offline | POP3 |
| Multi-dispositivo (PC + celular + web) | IMAP |
| Quiero organizar carpetas en el server | IMAP |
| Server con espacio limitado | POP3 (descarga y borra) |

## Aparece en parciales

- **Guía 3, ej. 8:** POP3 es orientado a conexión y confiable (usa TCP).
- **Guía 3, ej. 9:** "El uso de IMAP perdió popularidad desde Gmail y otros webmails" → **verdadero**. IMAP servía para ordenar mails en el server, lo que hoy hacen los webmails directamente.
