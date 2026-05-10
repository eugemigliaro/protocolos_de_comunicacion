# TLS (Transport Layer Security)

Protocolo basado en SSL 3.0 (a veces llamado "SSL 3.1"). RFC 5246 define TLS 1.2. Brinda el mismo nivel de encriptación que SSL; la diferencia clave está en **cómo se inicia la conexión segura**.

## Dos modos de uso

### Conexión segura por puerto

Se reserva un puerto distinto del estándar para la versión segura del protocolo. Toda la conexión es segura desde el primer byte.

| Protocolo | Puerto seguro |
|-----------|---------------|
| HTTP      | 443 (HTTPS)   |
| IMAP      | 993 (IMAPs)   |
| POP3      | 995 (POPs)    |

Este modo es el que históricamente venía de SSL.

### Conexión segura por protocolo (STARTTLS)

Se usa el **puerto estándar** del protocolo y la conexión arranca **insegura**. Después, mediante un comando del propio protocolo de aplicación, se "negocia" el upgrade a TLS y a partir de ahí la misma conexión pasa a ser segura.

Ejemplo: el comando **STARTTLS** en SMTP. Empieza una sesión SMTP normal en el puerto 25 (o 587), y cuando ambos extremos lo soportan se cambia a una conexión cifrada sobre la misma TCP.

## Restricción: solo TCP

TLS funciona sobre **conexiones TCP** (necesita un canal confiable subyacente). No se puede usar directamente sobre UDP, por lo tanto **no aplica para DNS** tradicional. Para usar DNS de forma segura se aprovecha HTTPS / TLS de otra manera (ver `dns-over-tls.md`).

## TLS vs SSL — diferencia conceptual

- **SSL**: requiere un puerto distinto para la versión segura (443, 993, etc.).
- **TLS**: puede usarse igual ("por puerto") pero también permite **upgrade en la misma conexión** (STARTTLS), arrancando insegura en el puerto estándar.

## Aparece en parciales

- "TLS se diferencia de SSL principalmente en que la conexión se establece en el puerto estándar y sin seguridad. Una vez establecida se puede negociar el uso del TLS y se comienza a usar sobre la misma conexión, no sobre otro puerto especial como requiere SSL." → **Verdadero** (preguntas-teoricas, ej. 25).

## Fuente

- Apunte oficial, Unidad 4, p. 48.
- RFC 5246 (TLS 1.2).
