# Mail

Sistema de mensajería entre usuarios de dominios distintos. Pre-internet, los mensajes existían dentro de un único servidor Unix (cada usuario con un buzón en una carpeta). Con internet aparece la necesidad de mandar mensajes entre dominios distintos, y con eso el `@` para separar usuario de dominio.

## Componentes

Para mover mensajes entre servidores nace **SMTP** (Simple Mail Transfer Protocol) y tres tipos de agentes:

| Agente | Rol |
|--------|-----|
| **MTA** (Mail Transfer Agent) | transfiere el mensaje entre hosts (típicamente entre servidores) |
| **MUA** (Mail User Agent) | cliente de usuario, permite leer y enviar mensajes (Thunderbird, Outlook, una webapp como Gmail) |
| **MDA** (Mail Delivery Agent) | coloca el mensaje en la casilla del destinatario |

## Flujo típico

```
[MUA emisor] --SMTP--> [MTA emisor] --SMTP--> [MTA receptor] --MDA--> [casilla] <--POP3/IMAP-- [MUA receptor]
```

Cuando le mando mail a otra persona, **siempre pasa por mi servidor de mail**. No hay conexión directa entre hosts: alguien del ITBA mandándole a alguien de Fibertel pasa por el server del ITBA → server de Fibertel.

## ¿Cómo sabe el MTA emisor a qué servidor enviar?

Hace una consulta DNS al **registro MX** del dominio que sigue al `@`. El MX devuelve el nombre del servidor SMTP receptor.

## SMTP

- Usa **TCP**, **puerto 25**, transferencia directa cliente → servidor.
- Tres fases:
  1. **Handshaking**
  2. **Transferencia de mensajes**
  3. **Cierre**
- Interacción comando/respuesta:
  - Comando: texto **ASCII**.
  - Respuesta: código de status numérico + comentario.
- Mensajes en **US-ASCII** (7 bits). No se pueden enviar caracteres como `ñ`, acentos, ni binarios (imágenes, audio) directamente — se necesita **MIME**.

## Transacción SMTP típica

```
S: 220 mail.ejemplo.com ESMTP ready
C: HELO miclient.com
S: 250 Hello
C: MAIL FROM: <yo@miclient.com>
S: 250 OK
C: RCPT TO: <otro@ejemplo.com>
S: 250 OK
C: DATA
S: 354 Send message, end with .
C: <headers + cuerpo del mail>
C: .
S: 250 Message accepted
C: QUIT
S: 221 Bye
```

`DATA` contiene el mensaje del mail. Cada línea: máximo **100 caracteres**, US-ASCII. El `.` solo en una línea marca fin del mensaje.

## Si no se puede entregar

Cuando el servidor receptor no acepta el mensaje (destinatario inexistente, casilla llena, etc.), el MTA emisor recibe el error y normalmente genera un **mail de bounce** al remitente notificando el problema.

## Acceso remoto a la casilla

El MTA receptor deja el mail en la casilla. Para que el usuario lo lea desde su MUA, el servidor expone un protocolo de entrega final:

| Protocolo | Puerto |
|-----------|--------|
| POP2 | 109 |
| **POP3** | **110** |
| **IMAP** | **143** |
| IMAP3 | 220 |

Ver `protocolos-de-entrega-final.md`.

## Aparece en parciales

- **Guía 3, ej. 4:** el MUA descubre el servidor SMTP por el registro MX del dominio detrás del `@`. Si el server no escucha en el puerto estándar, falla aunque el DNS resuelva bien.
- **Guía 3, ej. 8:** SMTP es orientado a conexión y confiable (usa TCP).
- **Guía 3, ej. 9 / Preguntas, ej. 19:** "SMTP usa TCP, eso garantiza que el mail llegue al destinatario" → **falso**. TCP solo garantiza que llegó al servidor SMTP. No garantiza que llegue al servidor del destinatario, ni que llegue al destinatario, que incluso podría no existir.
- **Guía 3, ej. 9:** "Uso Gmail desde browser, en mis envíos no interviene SMTP" → **falso**. Para llegar al servidor del destinatario, el server de Gmail usa SMTP (a menos que el destinatario también esté en Gmail).
