# Mensajes DNS

DNS usa mensajes binarios entre clientes (resolvers) y servidores. Pueden ser **queries** o **responses**, y comparten estructura.

## Estructura del mensaje

Seis secciones:

1. **Header (encabezado)**
2. **Question** — qué se está consultando
3. **Answer** — RRs que responden la pregunta
4. **Authority** — RRs que apuntan a servidores autoritativos
5. **Additional** — RRs útiles relacionados (ej. IPs de los NS listados en Authority)
6. (En queries solo se usa Header + Question.)

## Header

Contiene flags y contadores. Los campos clave:

### Opcode

| Valor | Significado |
|-------|-------------|
| 0 | consulta estándar |
| 1 | consulta inversa |
| 2 | consulta por "server status" |

### Rcode (response code)

| Valor | Significado |
|-------|-------------|
| 0 | sin error |
| 1 | error de formato |
| 2 | error en el servidor |
| 3 | error en el nombre (NXDOMAIN) |
| 4 | sin implementar |
| 5 | rechazado |

### Bits de control

| Bit | Significado |
|-----|-------------|
| **Q** | 0 = query, 1 = response |
| **A** | respuesta autorizada |
| **T** | respuesta truncada (la respuesta no entró en el datagrama UDP — cliente puede reintentar por TCP) |
| **D** | pregunta con recursividad (recursion desired) |
| **R** | recursión disponible en el servidor |

### ID

Identificador del mensaje. Las respuestas llevan el mismo ID que la pregunta. **Importante para seguridad:** previene que respuestas no solicitadas sean aceptadas (defensa básica contra DNS spoofing).

## Sección Question

Lleva el `qname` (nombre a resolver), `qclass` (típicamente `IN` = Internet), y `qtype`:

| qtype | Significado |
|-------|-------------|
| 1 | host (A) |
| 2 | name server autorizado (NS) |
| 5 | nombre canónico de un alias (CNAME) |
| 15 | mail exchange (MX) |
| 252 | solicitud de transferencia de zona (AXFR) |
| ... | etc. |

## Sección Answer

RRs en formato `(name, value, type, ttl)` que responden la query. Si la query era recursiva y exitosa, acá va la respuesta final.

## Truncado y reintento

Cuando una respuesta no entra en un datagrama UDP, el servidor envía lo que entra y prende el bit **T**. El cliente decide si rehace la pregunta por TCP. Esto es decisión de la **aplicación** — UDP no fragmenta ni reensambla.
