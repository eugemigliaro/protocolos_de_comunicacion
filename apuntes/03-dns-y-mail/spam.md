# SPAM

Envío masivo e indiscriminado de mensajes no solicitados, generalmente con fines publicitarios, fraudulentos o maliciosos. Las siglas no significan nada en particular (referencia a un sketch de Monty Python).

## Para qué se usa el spam

- **Obtener direcciones de correo** (cosechar quién responde / quién hace bounce).
- **Envío de mensajes con malware**:
  - Troyanos, bots.
  - Servidores SMTP mal configurados que aceptan **relay** (reenviar mails de cualquier origen a cualquier destino).
- **Verificación de recepción** (confirmar que la cuenta existe — a veces).

## Defensas

### Listas de bloqueo / permitidos

- **Deny list (antes "lista negra"):** IPs sospechosas de enviar spam. Se rechazan automáticamente.
- **Allow list (antes "lista blanca"):** IPs confiables, siempre se aceptan.

### Greylisting

Cuando llega un mail desde una IP que no está en ninguna lista, el server responde con un **error temporal** y pide que se reenvíe. Los servers legítimos reintentan automáticamente; los spam bots típicamente no están configurados para reintentar y se pierde el mensaje.

### Filtros bayesianos

Analizan el **contenido** del mail (palabras, patrones) para clasificarlo como spam con un score probabilístico. Aprenden de feedback del usuario (marcar/desmarcar como spam). Problema: no evitan que el mail llegue, solo lo clasifican después.

## Phishing

Según Verizon: **30% de los mails de phishing se abren**, y en hasta el **12%** se accede a los enlaces o archivos maliciosos.

## Autenticación de mails

Dos técnicas principales para verificar que un mail proviene legítimamente del dominio que dice ser:

### SPF (Sender Policy Framework)

Protocolo que **especifica qué servidores están autorizados a enviar mail en nombre de un dominio**. Se publica como un registro **TXT** en el DNS del dominio:

```
ejemplo.com.   IN  TXT  "v=spf1 ip4:192.0.2.0/24 include:_spf.ejemplo.com ~all"
```

Cuando un MTA receptor recibe un mail de `usuario@ejemplo.com`, consulta el SPF de `ejemplo.com` y verifica que la IP del MTA emisor esté autorizada. Ayuda a evitar **falsificación de direcciones** (mail spoofing).

### DKIM (DomainKeys Identified Mail)

Método que usa **firmas digitales** en los mails. El servidor emisor firma el mensaje (header + cuerpo) con una clave privada. La clave pública se publica en DNS. El receptor verifica:

1. Que la firma sea válida → **el mensaje no fue alterado** en tránsito.
2. Que la firma corresponda al dominio que dice ser el remitente → **el mail proviene de un remitente legítimo**.

### SPF + DKIM + DMARC

DMARC (no aparece explícito en el PDF pero es la pieza típica del rompecabezas) es una política publicada en DNS que le dice al receptor qué hacer cuando SPF/DKIM fallan (cuarentena, rechazar, nada) y a dónde reportar.

## Aparece en parciales

- (No hay preguntas específicas de SPAM en el material de exámenes consultado, pero el tema es típico de la unidad y conviene saber SPF/DKIM al menos a nivel concepto.)
