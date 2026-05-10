# ARP (Address Resolution Protocol)

Protocolo que **resuelve direcciones IP a direcciones MAC** en redes locales como Ethernet. Cuando un host quiere comunicarse con otro de la misma red local y solo conoce su IP, ARP le permite averiguar la MAC para encapsular la trama Ethernet.

> Aunque el PDF lo menciona como "protocolo de la capa de red", ARP en realidad opera entre capa 2 y 3: traduce direcciones de capa 3 (IP) a direcciones de capa 2 (MAC).

## ¿Cuándo se hace una petición ARP?

Cuando un host **necesita enviar un paquete IP y no conoce la MAC del próximo salto**. Casos típicos:

- Necesita enviar un paquete IP a un host **en su mismo segmento** y no conoce su MAC → ARP request por la IP destino.
- Necesita enviar un paquete IP a un host **en otro segmento** y no conoce la MAC del **gateway** → ARP request por la IP del gateway (no por la del host destino remoto: a la capa de enlace solo le importa el próximo salto).

NO se hace ARP cuando el host conoce su IP pero no su MAC (la MAC ya viene del fabricante). NO se hace ARP para descubrir IPs (eso es DNS, o RARP/InARP en casos puntuales).

## Variantes de ARP

| Variante | Caso de uso |
|----------|-------------|
| **ARP** | Conoce IP, busca MAC |
| **RARP** | Diskless workstation conoce su MAC pero "olvidó" su IP |
| **InARP** | Conoce MAC, busca IP |
| **Proxy ARP** | Un host responde ARP en nombre de otro |

## Cómo se envían las tramas ARP

- **ARP request**: se envía a **todos los nodos de la red** (broadcast: MAC destino `FF:FF:FF:FF:FF:FF`).
- **ARP reply**: se envía **directamente al MAC del host que hizo la petición** (unicast). NO se envía al gateway, ni a un servidor, ni a toda la red.

## Tabla ARP

Cada host mantiene una tabla ARP que cachea las relaciones IP↔MAC aprendidas. Una entrada se agrega/actualiza dinámicamente:

- Cuando recibe una **respuesta** a un ARP request enviado por él.
- **También** cuando recibe un ARP **request**: opcionalmente puede guardar la MAC del solicitante (depende de la implementación).

## Ejemplo: A envía un paquete IP a B en otra red

Supongamos que las tablas ARP están vacías. A está en la red de un router, B en otra red detrás del mismo router.

1. A examina su tabla de ruteo: el gateway para llegar a B es, por ejemplo, `192.168.2.1`.
2. A envía **ARP request** preguntando por la MAC de `192.168.2.1`.
3. El router envía **ARP reply** con su MAC.
4. A envía el paquete IP al router (MAC del router, IP destino B).
5. El router examina su tabla de ruteo: B está en la red `10.2.0.0/16`.
6. El router envía **ARP request** preguntando por la MAC de B.
7. B envía **ARP reply** con su MAC.
8. El router envía el paquete IP a B.

En este escenario se generan **2 ARP request + 2 ARP reply = 4 paquetes ARP**.

## Proxy ARP

Técnica donde un dispositivo (router o firewall) responde ARP requests en nombre de otro que está en una red diferente, **como si fuera él mismo**.

Permite que dos dispositivos en subredes distintas se comuniquen como si estuvieran en la misma red local, sin configurar rutas explícitas ni gateway.

> Si el host A solicita la MAC de C o D (que están en otra subred detrás del router), el router responde con **su propia MAC**. Por eso, con Proxy ARP, la relación IP↔MAC **no es 1 a 1**.

## Ataques sobre ARP

ARP **no contempla autenticación**: cualquiera puede responder un ARP request. Esto habilita varios ataques:

### ARP Spoofing (suplantación)

El atacante envía ARP replies falsas asociando IPs legítimas con su propia MAC. Los hosts de la red comienzan a enviarle a él los paquetes destinados a otros.

### Man-in-the-Middle (MITM)

Una vez envenenadas las tablas ARP de las víctimas, el atacante intercepta el tráfico. Si reenvía los paquetes después de mirarlos, las víctimas no notan nada raro.

### Denial of Service (DoS)

El atacante envenena la tabla ARP de B y C, y luego **no reenvía** los paquetes destinados a ellos (o los descarta directamente). También puede mandar replies falsas diciendo que la IP de B "no está disponible". Resultado: bloquea toda comunicación hacia esos hosts.

### Session hijacking

Aprovechando el envenenamiento ARP para interceptar sesiones activas y secuestrarlas.

### Cómo defenderse

- **Tablas ARP estáticas**: configurar entradas fijas IP↔MAC en dispositivos críticos. Imposibilita la sobrescritura por ARP spoofing.
- **Protocolos seguros**: HTTPS, SSH, IPSec — cifran y autentican el tráfico aunque alguien lo intercepte.
- **Switches inteligentes**: Port Security, DHCP Snooping, Dynamic ARP Inspection en switches de capa 2 gestionables.

> Reemplazar un hub por un switch **no es 100% seguro**: existen ataques como ARP spoofing que permiten ver tráfico unicast aunque haya switch.

## Usos legales del ARP spoofing

- Redireccionar equipos no registrados a una página de registro.
- Acceso a Internet en hoteles para dispositivos móviles.
- Implementar redundancia en servicios de red.

## Aparece en parciales

- **¿En qué caso se hace ARP request?** Un host quiere enviar un paquete IP a otro en su segmento y no conoce su MAC, **o** a otro en otro segmento y no conoce la MAC del gateway. NO se hace ARP para que un host con IP descubra su MAC, ni para descubrir IPs (preguntas teóricas, ej. 9; guía 7, ej. 6).
- **A quién se envía un ARP request**: a todos los nodos de la red (broadcast). **A quién se envía la reply**: al MAC del solicitante (guía 7, ej. 7 y 8).
- **Por qué un host busca al router**: porque la IP destino está en otra red (guía 7, ej. 9).
- **ARP spoofing es el ataque que asocia IP legítimas con MACs maliciosas** (preguntas teóricas, ej. 22).
- **La tabla ARP también se actualiza al recibir un ARP request, no solo una reply** (preguntas teóricas, ej. 34).
- **IPv6 incluye la MAC en la dirección, por lo que no necesita ARP** (preguntas teóricas, ej. 13 — ventaja de IPv6).

## Fuentes

- PDF unidad 8, p. 117-120.
- Guía 7 (Enlace), ej. 6-9.
- Preguntas teóricas: ej. 9, 13, 22, 34, 43.
