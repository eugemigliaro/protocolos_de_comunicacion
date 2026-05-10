# Asignación de Direcciones IP

Una interfaz de red puede recibir su dirección IP de forma **estática** (configurada manualmente) o **dinámica** (la pide al conectarse a la red, vía DHCP u otro protocolo). En cualquier caso, no alcanza con tener IP — hace falta también máscara, gateway y DNS para funcionar.

## Lo mínimo que necesita una interfaz

1. **Dirección IP propia** — para identificarse.
2. **Máscara de red** — para saber qué destinos están en la misma red local y cuáles requieren gateway.
3. **Gateway por defecto** — el router al que mandarle todo lo que no es local.
4. **Direcciones de servidores DNS** — para resolver nombres de dominio.

## Asignación estática

La IP, máscara, gateway y DNS se configuran **a mano** en cada dispositivo. Útil para servidores, routers y equipos críticos donde la IP no debe cambiar.

- Pro: estabilidad, control.
- Contra: no escala, propenso a errores y conflictos.

## Asignación dinámica

El dispositivo obtiene su configuración automáticamente al conectarse. Protocolos:

- **RARP** (Reverse ARP) — viejo, pedía la IP a partir de la MAC. Sólo daba IP, nada más.
- **BOOTP** (Bootstrap Protocol) — sucesor de RARP, daba IP + más datos. Era estático: una IP fija por MAC.
- **DHCP** (Dynamic Host Configuration Protocol) — el actual; ver `dhcp.md`. Asigna IP de un pool, con tiempo de préstamo (lease).

## Link-Local

Si no hay servidor DHCP disponible, el sistema operativo puede recurrir a una **dirección link-local**, válida sólo dentro de la red local (no enrutable):

- IPv4: `169.254.0.0/16` (APIPA en Windows).
- IPv6: `FE80::/10`.

Permite comunicación con vecinos de la LAN aunque no haya DHCP.

## Aparece en parciales

(No hay preguntas dedicadas en los parciales recopilados — los temas concretos van bajo DHCP.)

> Fuente: Apuntes oficiales, Unidad 6, p. 83.
