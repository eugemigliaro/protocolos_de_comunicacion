# DNS

Sistema jerárquico distribuido para traducir nombres de hosts a direcciones IP. La información en DNS se modela como un árbol invertido cuya raíz son los root name servers.

## Tipos de servidores DNS

- **Recursivos:** realizan la consulta completa en nombre del cliente (van saltando por la jerarquía hasta encontrar la respuesta).
- **Autorizados:** contienen la información definitiva sobre nombres de un dominio específico.
- **Raíz (root):** nivel más alto de la jerarquía, administrados por ICANN. Operados por la IETF.
- **Locales (default name server):** los del ISP, no necesariamente parte de la jerarquía. Hacen de proxy y cachean.

## Jerarquía

```
                    .  (root servers)
                    |
       ┌────────────┼────────────┐
       ar           com          edu
       |
      edu.ar
       |
      itba.edu.ar
       |
      www.itba.edu.ar
```

- **Root servers:** punto de entrada. Conocen los TLD servers.
- **TLD servers:** responsables de `com`, `org`, `net`, `edu`, `aero`, `jobs`, `museums`, y los códigos de país (`ar`, `uk`, `uy`, `tv`, etc.).
- **Authoritative servers:** uno por organización (propio o de un proveedor). Tienen la respuesta definitiva para su zona.

## Resolución típica (recursiva, primera vez)

Servidor DNS local recién instalado, no sabe nada. Quiero `www.itba.edu.ar`:

1. Pregunto a un **root server** → me devuelve el NS para `ar` (ej. `c.dns.ar`).
2. Pregunto a `c.dns.ar` → me devuelve el NS para `edu.ar`.
3. Pregunto a ese NS → me devuelve el NS autorizado para `itba.edu.ar`.
4. Pregunto al NS autorizado → me devuelve la IP de `www.itba.edu.ar` con respuesta autorizada.

La respuesta que llega al host **no es autorizada** (vino del DNS local, no del servidor autorizado).

Si después pido `mail.itba.edu.ar`, salto directo al paso 4 — el resto está cacheado.

## Caché y TTL

- Cada entrada cachea con un **TTL** (time-to-live) tras el cual expira.
- Los servidores cachean fuerte la dirección de los TLD; los root servers casi nunca se visitan.
- Si cambio la IP de un servidor sin actualizar registros DNS o `/etc/hosts`, los clientes con caché vieja no llegarán hasta que expire.

## Niveles de configuración (BIND)

En Unix/Linux, DNS se implementa típicamente con **BIND** (Berkeley Internet Name Domain). Cuatro niveles:

| Nivel | Qué hace |
|-------|----------|
| **Resolver only** | solo cliente, no responde |
| **Caching-only** | aprende respuestas de otros y las guarda. No es autorizado (info de segunda mano). Programa: `dnscache`. |
| **(Master) Primary** | fuente autorizada de una zona. Lee la zona desde un archivo escrito por el admin. |
| **(Slave) Secondary** | transfiere la zona completa desde un master. Mantiene info actualizada y puede responder de forma autorizada. |

## Split-horizon

Técnica donde un mismo nombre resuelve a IPs distintas según el origen de la consulta. Sirve para:

- **Seguridad:** no exponer hosts internos al exterior (cuanto menos info pública, menos vulnerable).
- **Eficiencia:** un host dentro de la red local recibe la IP privada y se conecta directo, sin pasar por router/firewall.

Ejemplo (guía 3, ej. 1): `pampero.itba.edu.ar` resuelve a una IP `10.x.x.x` desde adentro y a una IP pública desde afuera. Si dentro del ITBA uso DNS de Google (`8.8.8.8`), el DNS del ITBA recibe la consulta "desde afuera" y devuelve la pública, que no es ruteable desde la red interna.

## DNS Dinámico (DDNS)

Servicio que actualiza automáticamente los registros DNS cuando la IP de un dispositivo cambia. Útil para conexiones con IPs dinámicas (la mayoría de los ISPs domiciliarios).

## Transporte: UDP vs TCP

DNS escucha en ambos. El cliente elige.

- **UDP** — para respuestas cortas (entran en un datagrama). Sin overhead de conexión, headers más chicos.
- **TCP** — para respuestas largas (ej. transferencia de zona, muchas respuestas).

Si una respuesta UDP no entra en un datagrama, el servidor envía lo que puede y prende el **bit de truncado**. Es responsabilidad de la **aplicación** (no de UDP) decidir si reintenta por TCP. En general UDP no fragmenta, lo maneja la aplicación.

## Aparece en parciales

- **Guía 3, ej. 1:** split-horizon (caso pampero).
- **Guía 3, ej. 2 y 3:** UDP vs TCP, manejo de truncado.
- **Guía 3, ej. 8:** DNS es no orientado a conexión y no confiable.
- **Guía 3, ej. 10:** justificar split-horizon.
