# SNAT / NAT (Network Address Translation)

**NAT** modifica direcciones IP (y opcionalmente puertos) en los headers de los paquetes que atraviesan un router/firewall. Su uso típico es permitir que muchos hosts de una red privada salgan a Internet con **una sola IP pública**, conservando direcciones IPv4 y ocultando la topología interna. Las dos variantes principales son **SNAT** (modifica origen, en paquetes salientes) y **DNAT** (modifica destino, en paquetes entrantes).

## SNAT (Source NAT)

Cambia la **IP de origen** (y normalmente el **puerto de origen**) de los paquetes que salen de la red privada hacia Internet.

```
[ Host privado 192.168.1.10:54321 ]
            ↓
[ Router con SNAT — IP pública 200.45.12.5 ]
            ↓ (re-escribe origen)
[ Internet — el destino ve 200.45.12.5:60000 ]
```

- El router mantiene una **tabla de conexiones**: `(IP privada, puerto privado) ↔ (IP pública, puerto público)`.
- Cuando llega la respuesta a `200.45.12.5:60000`, el router consulta la tabla, hace **NAT inverso** y reenvía al host interno correcto.
- Lo que cambia SNAT en un paquete saliente: **IP origen** y **puerto origen**.
- Lo que **no** cambia: IP destino, puerto destino.

### Una IP pública, muchos hosts privados

La forma más común de SNAT es **NAPT (NAT con sobrecarga / PAT — Port Address Translation)**: el router multiplexa muchos hosts internos sobre **una sola IP pública**, distinguiéndolos por **puerto**. Un router con 4 hosts puede tener **muchísimas más** entradas que 4 en su tabla NAT, porque una entrada es **por conexión activa**, no por host (cada host puede tener varias conexiones simultáneas — varias pestañas, varias apps).

## DNAT (Destination NAT)

Cambia la **IP de destino** (y/o puerto destino) de paquetes que entran a la red. Sirve para exponer servicios internos a Internet.

```
Internet → [ 200.45.12.5:80 ] (router)
                    ↓ (re-escribe destino)
                    [ 192.168.1.20:8080 — servidor web interno ]
```

### Port Forwarding

Caso clásico de DNAT: el router escucha en un puerto público y reenvía a un puerto/host interno. Muy usado para exponer servidores web, SSH, juegos, etc.

## NAT y los protocolos sin puerto

ICMP no tiene puerto, así que NAT lo trackea por el campo **Identifier** del header ICMP (y la tupla de IPs). Por eso un ping desde adentro de una red NAT funciona aunque el router multiplexe hosts.

## Beneficios y problemas

**Pro:**
- Conservar IPs públicas (clave dado el agotamiento de IPv4).
- Aislamiento: la red interna no es directamente alcanzable desde afuera (impide conexiones entrantes salvo DNAT explícito).

**Contra:**
- Rompe el principio extremo a extremo: hay un middlebox tocando paquetes.
- Complica protocolos que llevan IPs en el payload (FTP activo, SIP) — necesitan **ALG** (Application Layer Gateway).
- Conexiones entrantes requieren DNAT manual o **IGD** (UPnP) para que la app abra puertos automáticamente.
- IPv6 lo evita por diseño (hay direcciones de sobra).

## SNAT en escenarios mixtos

Una organización con varios routers internos puede tener **NAT en algunos y no en otros**. El que conecta con Internet siempre hace NAT (típicamente con firewall). Los routers internos sólo rutean IPs privadas.

## Aparece en parciales

- *Guía 5, Ej. 4:* un host con IP privada **sí puede** hablar con uno con IP pública, **siempre que haya un firewall que haga NAT** entre la red privada y la pública.
- *Guía 5, Ej. 24:* hosts con redes privadas pueden hablar UDP/TCP atravesando NAT.
- *Guía 5, Ej. 25:* tabla de ruteo de un host detrás de un firewall NAT — el host **no necesita saber** que hay NAT, simplemente manda al gateway.
- *Preguntas teóricas, Ej. 8:* SNAT cambia **puerto origen** y/o **IP origen**. **No** toca destino.
- *Preguntas teóricas, Ej. 28:* "el router con NAT y 4 hosts internos tiene a lo sumo 4 entradas" → **falso**. La tabla NAT trackea **conexiones**, no hosts; un host puede tener decenas de conexiones simultáneas.
- *Preguntas teóricas, Ej. 33:* un ISP con 256 IPs públicas puede vender Internet a 500 casas usando NAT, multiplexando varias casas detrás de cada IP pública.
- *Preguntas teóricas, Ej. 21.12-13 (Guía 5):* IGD permite que un host pida dinámicamente al firewall que haga DNAT/port forwarding desde la IP pública hacia él.
- *Preguntas teóricas, Ej. 21.14:* asignar una sola IPv6 pública al hogar **no es suficiente** para evitar NAT — IPv6 da prefijos, no una sola IP, y ahí sí cada host puede tener su pública.

> Fuente: Apuntes oficiales, Unidad 6, p. 87-89.
