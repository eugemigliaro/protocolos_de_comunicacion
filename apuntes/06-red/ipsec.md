# IPSec

**IPSec (Internet Protocol Security)** es un conjunto de protocolos que da seguridad a nivel de **capa de red**: autenticación, integridad y confidencialidad de paquetes IP. Funciona en IPv4 e IPv6 y opera en dos modos (**transporte** y **túnel**) con dos protocolos principales (**AH** y **ESP**). Es la base de la mayoría de las VPN site-to-site.

## Servicios que provee

- **Control de acceso:** sólo dispositivos autorizados acceden a los recursos.
- **Confidencialidad:** los datos viajan cifrados, sólo el destinatario puede leerlos.
- **Autenticación:** verifica la identidad de origen — el paquete viene de quien dice venir.
- **Integridad:** detecta alteración del paquete en tránsito (vía MAC — Message Authentication Code).
- **Anti-replay:** protege contra paquetes reenviados (números de secuencia).

## Modos: Transporte vs Túnel

### Modo Transporte

Protege **sólo el payload** del paquete IP — el header original IP queda intacto.

```
[ IP header ][ IPSec (AH/ESP) ][ Payload protegido ]
```

- Comunicación **end-to-end** entre dos hosts (host ↔ host).
- El header IP se conserva → el routing es transparente, los routers ven IPs reales.
- No oculta las direcciones reales, sólo el contenido.

### Modo Túnel

**Encapsula el paquete IP completo** (header + payload) dentro de un nuevo paquete IPSec, con un nuevo header IP por afuera.

```
[ Nuevo IP header ][ IPSec (AH/ESP) ][ IP header original + Payload original ]
```

- Comunicación **gateway-to-gateway** (router ↔ router) o host-to-gateway.
- Las IPs originales viajan **cifradas** dentro del paquete IPSec — sólo se ve la IP del túnel.
- Es la base de las **VPN site-to-site**: dos sucursales con redes privadas pueden hablar a través de Internet como si estuvieran en la misma red.

## Protocolos: AH vs ESP

### AH — Authentication Header

- Provee **autenticación + integridad**.
- **No cifra** el payload.
- Cubre el paquete completo (incluyendo gran parte del header IP).
- Se identifica por `protocolo = 51` en el header IP.

### ESP — Encapsulating Security Payload

- Provee **confidencialidad + autenticación + integridad** del payload.
- **Cifra el payload** (y opcionalmente lo autentica).
- En modo túnel, cifra todo el paquete original.
- Se identifica por `protocolo = 50` en el header IP.
- Es lo que se usa el 99% del tiempo (ESP en modo túnel = VPN clásica).

> Se pueden combinar AH + ESP, pero es raro — ESP solo ya da todo lo que se necesita normalmente.

## Resumen rápido

| | Transporte | Túnel |
|---|---|---|
| Qué protege | Sólo payload | Paquete IP completo |
| Header IP visible | Sí (original) | Sí (nuevo, oculta el original) |
| Uso típico | Host ↔ host | VPN site-to-site (gateway ↔ gateway) |

| | AH | ESP |
|---|---|---|
| Autenticación + integridad | Sí | Sí |
| Confidencialidad (cifrado) | **No** | **Sí** |
| Protocolo IP | 51 | 50 |

## Aparece en parciales

(Sin preguntas dedicadas en los parciales recopilados — IPSec aparece como concepto en Unidad 6 pero las preguntas concretas suelen ir hacia VPN, túneles SSH y NAT.)

> Fuente: Apuntes oficiales, Unidad 6, p. 100-101.
