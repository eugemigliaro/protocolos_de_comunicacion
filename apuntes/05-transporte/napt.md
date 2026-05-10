# NAPT (Network Address & Port Translation)

Técnica usada por los routers para que **muchos hosts con IP privada** puedan compartir una **única IP pública** al salir a internet. Implementada típicamente como parte del firewall del router. También se la conoce como **SNAT** (Source NAT) cuando se modifica la dirección de origen.

## El problema histórico

Hasta mediados de los 90, para usar internet se necesitaba una **IP pública en el host**. En las universidades, solo las máquinas de los laboratorios con IP pública podían salir; las demás (con IP privada) no. Los routers solamente forwardeaban: si el origen tenía IP privada, no se podía rutear.

Como solución a largo plazo se diseñó **IPv6** (más bits de direccionamiento), pero migrar la red entera era inviable a corto plazo. **NAT/NAPT fue el parche** que permitió seguir usando IPv4.

## Diferencia: NAT vs NAPT

- **NAT** (a secas, también llamado SNAT en su forma básica): cambia **únicamente la IP**.
- **NAPT** (Network Address & Port Translation): cambia **IP y puerto** de origen. Esto es lo que permite que **varios hosts internos** elijan el mismo puerto de origen — el router los desambigua reescribiendo el puerto.

## Funcionamiento

Cuando un host privado quiere salir a internet, el router:

1. Toma el paquete con `<IP_priv : puerto_priv>` como origen.
2. Asigna un **puerto público libre** y reescribe el origen a `<IP_pública : puerto_pub>`.
3. Guarda la asociación en una **tabla de NAT**.
4. Forwardea el paquete reescrito.
5. Cuando llega la respuesta a `<IP_pública : puerto_pub>`, busca en la tabla, **reescribe el destino** a `<IP_priv : puerto_priv>` y lo entrega al host interno.

## Tabla de NAPT

Tiene la forma:

| IP origen interna | Puerto interno | IP destino externa | Puerto destino | IP pública | Puerto público |
|---|---|---|---|---|---|
| 192.168.1.10 | 5001 | 1.2.3.4 | 80 | 200.1.1.1 | 40001 |
| 192.168.1.11 | 5001 | 1.2.3.4 | 80 | 200.1.1.1 | 40002 |

La **IP pública de salida** se guarda en la tabla porque un router puede tener asignada **más de una IP pública**.

Las entradas son **por conexión**, no por host. Un host con varias conexiones simultáneas tendrá varias entradas.

## Limitaciones

- Rompe la idea de **end-to-end**: los hosts internos no son alcanzables desde afuera **sin configuración explícita** (DNAT / port forwarding — ver `dnat.md`).
- Requiere **estado** en el router → si el router se reinicia, se pierden las traducciones activas.
- Complica protocolos P2P (ver `udp-hole-punching.md`).
- Hay distintos **tipos de NAT** según cuán restrictivo es el reuso de la asignación: full cone, restricted cone, symmetric.

## Aparece en parciales

- "Si mi router con firewall hace SNAT, cuando desde mi host me conecto a un host en otra red, a los paquetes que se ORIGINAN en mi host el firewall puede cambiar:" → el **puerto de origen** y la **IP de origen** (preguntas-teoricas ej. 8).
- "Sea una red local con IP privada, 4 hosts conectados a un router que hace NAT. ¿Es correcto afirmar que el router tendrá a lo sumo 4 entradas en su tabla de NAT?" → **Falso**: la tabla tiene una entrada **por conexión activa**, no por host (preguntas-teoricas ej. 28).
- "Si un ISP de un pueblo de 500 casas adquiere 256 IPs públicas, ¿no podrá venderle internet a todas las casas?" → **Falso**: con NAT varias casas pueden compartir una IP pública (preguntas-teoricas ej. 33).

## Fuente

- Apunte oficial, Unidad 5, p. 61.
- Preguntas teóricas 8, 28, 33.
