# UDP Hole Punching

Técnica de **NAT traversal** que permite la **comunicación directa** entre dos hosts que están detrás de NAT, sin pasar todo el tráfico por un servidor intermedio. Solo funciona con **UDP** y aprovecha que el firewall del NAT, una vez que ve un paquete saliente, deja pasar las respuestas que vengan a esa misma asignación.

## El problema: NAT impide conexiones P2P

En aplicaciones P2P (llamada por WhatsApp, juego online, videollamada) **no hay** un cliente fijo y un servidor — cualquiera de las dos puntas puede enviar. Pero ambas suelen estar **detrás de NAT**:

- Tienen IP **privada**, no enrutables desde internet.
- Para que un host externo le mande un paquete a un host interno, el NAT necesita **ya tener** una entrada en su tabla que mapee `<IP_pub : puerto_pub> → <IP_priv : puerto_priv>`.
- Esa entrada solo se crea cuando el host **interno** envía algo primero.

Si **ambos** están detrás de NAT, **¿quién manda primero?**

## La idea

Si conseguimos que **los dos hosts manden un paquete saliente al mismo tiempo** hacia la IP/puerto público del otro, ambos NATs crean la entrada en su tabla simultáneamente. Después, los paquetes que lleguen "hacen calzar" la entrada y se entregan.

## NAT Traversal — tipos de NAT

Las técnicas que funcionan dependen del **tipo de NAT** de cada extremo:

### Full Cone NAT

Una vez que un host interno hace una conexión saliente, **cualquier host externo** puede enviar paquetes de vuelta a `<IP_pub : puerto_pub>`. Sin restricciones por IP origen ni puerto origen del externo. **Es el más permisivo** — el hole punching es casi gratis.

### Restricted Cone NAT

Permite que un host externo envíe paquetes al host interno **solo si** el host interno ya le envió datos a esa **IP** previamente. **Restringe por IP** (no por puerto): cualquier puerto de esa IP externa puede responder.

### Symmetric NAT

El más restrictivo. Asigna **un puerto público diferente** para cada destino externo distinto:

- Si el host interno manda a `1.2.3.4:80`, el NAT le asigna `puerto_pub_A`.
- Si el host interno manda a `5.6.7.8:80`, el NAT le asigna `puerto_pub_B`.

Y solo el host externo que **recibió esa conexión** puede responder. Hace **muy difícil** el hole punching: el puerto público que ven es distinto del que vería un peer al hacer el rendez-vous.

## Procedimiento de UDP Hole Punching

Asume que existe un **servidor de rendez-vous** (signaling) al que ambos hosts pueden conectarse — en WhatsApp, ese rol lo cumplen los servidores de WhatsApp.

```
       ┌────────────┐                      ┌────────────┐
       │  Host A    │                      │  Host B    │
       │ (priv NAT) │                      │ (priv NAT) │
       └─────┬──────┘                      └──────┬─────┘
             │                                    │
             │     ┌──────────────────────┐       │
             ├────►│   Servidor signaling │◄──────┤
             │     └──────────────────────┘       │
             │                                    │
             │  ── intento directo (hole punch) ──│
             │◄──────────────────────────────────►│
```

Pasos (según el apunte oficial):

1. **Host A** le pide al servidor: "quiero conectarme a B".
2. **Servidor** le devuelve a A las **credenciales (IP pública + puerto)** de B, y a B las de A.
3. **Host A** envía un paquete UDP desde su puerto al `<IP_pub_B : puerto_pub_B>`. **El NAT de B lo rechaza** porque B nunca envió nada a A todavía (cruz roja). **Pero** el NAT de A acaba de crear una entrada hacia B.
4. **Host B** envía un paquete UDP a `<IP_pub_A : puerto_pub_A>`. **El NAT de A lo deja pasar**, porque ya tiene la entrada que dice "estoy esperando respuesta de B".
5. A partir de ahí, **A y B se comunican directamente**.

El "agujero" perforado en el firewall es la entrada que creamos en la tabla del NAT al mandar el paquete inicial, aunque el primero haya sido rechazado.

## ¿Por qué solo UDP?

TCP requiere un three-way handshake con SYN/SYN-ACK/ACK. Los NATs estrictos (especialmente symmetric) no toleran que el SYN-ACK venga "del aire" antes de haber visto el SYN saliente con el mapeo correcto. UDP es **stateless** — al firewall del NAT solo le importa que ya exista la entrada en la tabla, no negociar nada.

## Aparece en parciales

- (No aparece preguntado en las preguntas teóricas relevadas, pero conceptualmente se cruza con el manejo de NAT de los ejercicios 8, 28, 33.)

## Fuente

- Apunte oficial, Unidad 5, p. 68-70.
