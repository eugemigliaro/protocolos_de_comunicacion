# Banco de preguntas teóricas — para el parcial

Compilado de **todas** las preguntas teóricas presentes en `material/examenes/`:

- `Protos Teorico 2C 2025.pdf` (44 preguntas) → ref. `2C25 #n`
- `preguntas-teoricas.docx` — banco de exámenes viejos (47 preguntas) → ref. `banco #n`
- `parcial-teorico-1q2025.docx` (35 preguntas, imágenes) → ref. `1Q25 #n`

Tras deduplicar quedan **60 preguntas distintas**, repartidas en **6 tandas**.
Dentro de cada tanda están ordenadas por unidad (01 → 11). Los subtemas que se repiten
(subnetting, ARP, túneles, NAT, IPv6…) están repartidos entre tandas a propósito: si un
tema no te sale en una tanda, reaparece en otra con otro enunciado.

**Cómo usar:** completá `**Tu respuesta:**` debajo de cada pregunta. Cuando termines, te
corrijo **unidad por unidad** (cada pregunta lleva su etiqueta `[Uxx]`).

Etiquetas de unidad: `U01` Introducción · `U02` HTTP · `U03` DNS y Mail · `U04` TLS/SSL ·
`U05` Transporte · `U06` Red · `U07` Routing · `U08` Enlace · `U10` SSH · `U11` Sockets.

---

## Tanda 1

### 1. `[U01]` OSI — servicio con conexión `(banco #36 · 2C25 #27)`
**V/F.** "Si el protocolo de una capa del modelo OSI es con conexión, entonces la capa superior también ofrecerá un servicio con conexión." Justificar.

**Tu respuesta:**

_…_

### 2. `[U02]` HTTP — IP real del cliente detrás de proxy `(banco #15)`
Es simple usar nginx con HTTPS como proxy reverso para acceder a Tomcat en forma segura. Pero si la app web "levanta" la IP del cliente (para log/control), esa IP será siempre 127.0.0.1, ya que el cliente HTTP de la app es nginx, que corre en el mismo host. ¿Qué puede hacer nginx para informarle a la app la IP del verdadero cliente?

**Tu respuesta:**

_…_

### 3. `[U03]` Mail — ¿cómo sabe el servidor al que conectarse? `(banco #7 · 1Q25 #19)`
Un programa de correo (Thunderbird, Outlook, app de celular, etc.) sólo nos pregunta cuenta de correo y contraseña. ¿Cómo sabe cuál es el servidor al cual tiene que conectarse para enviar un mail?

**Tu respuesta:**

_…_

### 4. `[U05]` UDP — respuesta que no entra en un datagrama `(banco #2 · 2C25 #4 · 1Q25 #29)`
Si una aplicación que utiliza UDP debe enviar una respuesta que no entra en un único datagrama, ¿qué servicio ofrece UDP para que pueda enviar la respuesta? ¿Cómo se resuelve?

**Tu respuesta:**

_…_

### 5. `[U05]` TCP — no llega el ACK `(banco #41 · 2C25 #41)`
En una comunicación TCP, ¿qué sucede si de un segmento enviado no se recibe el ACK en un tiempo determinado?
- A) Se cierra la conexión
- B) Se retransmite el paquete
- C) Nada
- D) Se envía un mensaje de error al otro host
- E) Depende de la aplicación

**Tu respuesta:**

_…_

### 6. `[U06]` ICMP redirect `(banco #3 · 2C25 #16 · 1Q25 #18)`
¿Por qué los hosts por defecto ignoran los ICMP redirect recibidos?

**Tu respuesta:**

_…_

### 7. `[U06]` Tabla de ruteo completa + VPN `(banco #14 · 1Q25 #32)`
Escribir la tabla de ruteo completa del host D teniendo en cuenta que:
1. El host B no hace NAT
2. El host E hace NAT para la red 192.168.3.0/24
3. El host G es el gateway de la organización
4. Asumir que las interfaces no especificadas son eth0
5. El host D se conecta a una VPN cuya IP pública es 24.232.1.1, red privada 10.3.0.0/24, gateway 10.3.0.1, para administrar un equipo 10.3.0.5. La interface de la VPN será vpn0.

(Topología: D.2 — E.1 [192.168.3.0/24]; E es eth1 10.1.2.3 al switch 10.1.2.0/24; B.1 eth0 — C.2 [192.168.2.0/24], B eth1 10.1.2.2; G 10.1.2.1 / 200.12.23.1 hacia Internet.) Columnas: Red | Máscara | Interface | Gateway.

**Tu respuesta:**

_…_

### 8. `[U06]` Subnetting — ISP, 3 empresas de 80 IPs `(banco #45)`
Un ISP con cobertura nacional tiene libre el rango 200.1.1.0 a 200.1.1.255 (256 en total). Hay 3 empresas que quieren, cada una, 80 direcciones IP públicas (240 en total). ¿Puede el ISP satisfacer la demanda de las 3 empresas? Justificar.

**Tu respuesta:**

_…_

### 9. `[U06]` Fragmentación en capa de red `(2C25 #18)`
¿Cuándo puede ocurrir la fragmentación de datagramas en la capa de red?
- A) Cuando el tamaño del datagrama excede el MTU del enlace.
- B) Cuando se pierden paquetes en la red.
- C) Cuando se utiliza TCP en lugar de UDP.
- D) Cuando un host intenta enviar más de 65.535 bytes.

**Tu respuesta:**

_…_

### 10. `[U08]` ARP — ¿en qué casos se hace una petición? (IPv4) `(banco #6 · 1Q25 #22)`
Dada una red IPv4. ¿En qué casos se realiza una petición ARP? Marcar todas las correctas.
- A) Un host conoce su dirección MAC pero no su dirección IP
- B) Un host necesita enviar un paquete IP a un host en su segmento pero no conoce su MAC
- C) Un host necesita enviar un paquete IP a un host en otro segmento pero no conoce la MAC del gateway
- D) Un host conoce su propia dirección IP pero no su dirección MAC
- E) Un host necesita enviar un paquete IP a un host en otro segmento y no conoce la MAC de ese host

**Tu respuesta:**

_…_

### 11. `[U08]` ARP — única forma dinámica de actualizar la tabla `(banco #34 · 1Q25 #25)`
**V/F**, justificar en ambos casos: "La única forma en que a la tabla ARP de un host se le agrega o actualiza una entrada en forma dinámica es cuando ese host recibe una respuesta a un ARP request enviado por él."

**Tu respuesta:**

_…_

### 12. `[U10]` VPN — cambios en la tabla de ruteo `(banco #1 · 2C25 #7)`
Mi red local es 192.168.1.0/24. Desde mi host 192.168.1.10 me conecto a una VPN cuya IP pública es 1.2.3.4 y red privada 10.0.0.0/16. La IP pública de mi router es 5.6.7.8, el gateway de la otra red es 10.0.0.1, y la IP que me asignan al conectarme es 10.0.0.15. Detallar qué cambios se producen en la tabla de ruteo de mi host.

**Tu respuesta:**

_…_

---

## Tanda 2

### 13. `[U02]` HTTP — usar la IP en vez del FQDN `(banco #26)`
El IP de un servidor HTTP cambió, pero mi servidor DNS lo sigue resolviendo con la IP vieja. Si yo conozco la IP nueva, ¿puedo usarla directamente en el browser en vez del FQDN? (Siempre / A veces / Nunca). Justificar.

**Tu respuesta:**

_…_

### 14. `[U03]` SMTP — ¿TCP garantiza la entrega? `(banco #19 · 2C25 #32)`
**V/F.** "SMTP usa TCP como protocolo de transporte, eso garantiza que el mail que yo envío será recibido por el destinatario final." Justificar.

**Tu respuesta:**

_…_

### 15. `[U05]` TCP — campo window de 16 bits `(banco #9)`
El campo window en TCP tiene 16 bits, lo cual permite indicar un buffer libre de entrada de hasta 64KB. ¿Este límite es aceptable hoy día? En caso de no serlo, ¿hay forma de manejar buffers de mayor tamaño? Justificar brevemente.

**Tu respuesta:**

_…_

### 16. `[U05]` TCP — ¿`send` es bloqueante? `(2C25 #21)`
**V/F**, justificar: "La función `send` cuando se usa para enviar datos con un socket TCP es bloqueante, ya que la aplicación debe esperar a que TCP reciba el o los ACK para todos los bytes que se intentan enviar."

**Tu respuesta:**

_…_

### 17. `[U06]` Red — IPs no asignables en 10.15.0.0/16 `(banco #4 · 2C25 #2)`
Para la red 10.15.0.0/16, ¿cuáles de las siguientes IPs NO puedo asignarle a los hosts?
- A) 10.15.255.255
- B) 10.17.5.5
- C) 10.15.255.0
- D) 10.15.0.0
- E) 10.15.0.255

**Tu respuesta:**

_…_

### 18. `[U06]` IPv6 — ¿routers intermedios deben ser IPv6? `(banco #27 · 2C25 #25)`
**V/F.** "Si dos hosts en distintas redes usan sólo IPv6, para poder comunicarse necesitan que todos los routers intermedios también usen IPv6." Justificar.

**Tu respuesta:**

_…_

### 19. `[U06]` VPN — conflicto de redes 192.168.1.0/24 `(2C25 #11)`
Para administrar un Postgres en un host de una red distante me habilitan credenciales de VPN. La IP pública de esa red es 2.3.4.5. Mi red local es 192.168.1.0/24, mi gateway 192.168.1.1, mi IP local 192.168.1.13. No tengo la clave de admin del router, así que no puedo cambiarlo. El problema: la otra red también es 192.168.1.0/24 y el host a administrar tiene IP 192.168.1.100. Si intento conectarme a 192.168.1.100:5432 me da error. ¿Por qué no me puedo conectar? ¿Cómo puedo hacer para conectarme?

**Tu respuesta:**

_…_

### 20. `[U06]` Red — misma IP en dos redes /24 y /25 `(2C25 #19)`
Se tienen dos redes distintas: 192.168.10.0/24 y 192.168.10.0/25, que no están ni estarán interconectadas. ¿Cuál de las siguientes IPs pueden ser usadas en **ambas** redes?
- A) 192.168.10.42
- B) 192.168.10.37
- C) 192.168.10.137
- D) 192.168.10.222

**Tu respuesta:**

_…_

### 21. `[U08]` Enlace — propósito del control de flujo `(banco #16 · 2C25 #36)`
¿Cuál es el propósito del control de flujo en la capa de enlace de datos?
- A) Regular la velocidad de transmisión de datos entre nodos adyacentes.
- B) Asegurar que los datos lleguen a su destino sin errores.
- C) Establecer y terminar sesiones de comunicación entre nodos adyacentes.
- D) Detectar y corregir errores en las tramas.

**Tu respuesta:**

_…_

### 22. `[U08]` Ethernet — longitud máxima del cable en un hub `(banco #35 · 2C25 #39 · 1Q25 #15)`
**V/F.** "Ethernet establece una máxima longitud del cable. Si en una LAN todos los hosts se conectan a un hub, y uno de los hosts está conectado por un cable de mayor longitud al máximo establecido, ese host no podrá enviar ni recibir ningún paquete." Justificar.

**Tu respuesta:**

_…_

### 23. `[U10]` Proxy — acceder a sitios filtrados `(banco #10 · 2C25 #6 · 1Q25 #30)`
Un alumno del ITBA trabaja en una empresa que aplica dos filtros al tráfico de salida:
- El tráfico HTTP debe pasar por el proxy (sólo el proxy puede conectarse a servidores HTTP).
- Ciertos sitios están filtrados por el proxy.

Este alumno, ¿puede acceder igual a los sitios prohibidos? ¿Cómo?

**Tu respuesta:**

_…_

---

## Tanda 3

### 24. `[U02]` HTTP — mantener una "sesión" `(banco #42)`
El modelo de capas TCP/IP no ofrece un nivel de Sesión. El protocolo HTTP no es orientado a conexión. ¿Cómo puede entonces una aplicación web mantener una "sesión" abierta?

**Tu respuesta:**

_…_

### 25. `[U03]` DNS — ¿por qué resolver nombres por HTTP? `(banco #47)`
¿Cuál sería la razón de usar HTTP para resolver nombres en vez de DNS sobre UDP, si DNS sobre UDP es más rápido y liviano que HTTP?

**Tu respuesta:**

_…_

### 26. `[U05]` TCP — control de congestión `(banco #17 · 2C25 #31 · 1Q25 #17)`
**V/F.** "Control de congestión es implementado por TCP para evitar enviar información a la otra punta que no puede procesar." Justificar.

**Tu respuesta:**

_…_

### 27. `[U05]` SNAT — ¿qué puede cambiar el firewall? `(banco #8 · 1Q25 #27)`
Si mi router con firewall hace SNAT, cuando desde mi host me conecto a un host en otra red, a los paquetes que se **originan** en mi host el firewall **puede** cambiar:
- A) El puerto destino
- B) La IP de destino
- C) El puerto de origen
- D) La IP de origen

**Tu respuesta:**

_…_

### 28. `[U06]` IPv4 — características `(banco #5 · 1Q25 #23)`
Marcar todas las características que se apliquen al protocolo IPv4.
- A) Un protocolo cuyos datos corresponden a un protocolo de transporte
- B) Un protocolo que elige el mejor camino entre dos hosts
- C) Un protocolo no confiable
- D) Un protocolo sin reconocimiento (no envía ACK)

**Tu respuesta:**

_…_

### 29. `[U06]` Red — router dropea y manda ICMP `(banco #30 · 2C25 #33)`
**V/F.** "Al droppear un paquete, un router puede opcionalmente mandar un paquete ICMP al remitente indicando la razón del drop." Justificar.

**Tu respuesta:**

_…_

### 30. `[U06]` Asignación de direcciones IP `(2C25 #12)`
¿Cuál de las siguientes opciones describe correctamente los métodos de asignación de direcciones IP que pueden utilizarse en una red?
- A) Estática, dinámica y automática (manual, vía DHCP, o predeterminada en el sistema).
- B) Pública, multicast y broadcast, según el tipo de tráfico.
- C) Global, local y privada, según el origen del proveedor.
- D) Manual, aleatoria y compartida, según el tipo de conexión inalámbrica.

**Tu respuesta:**

_…_

### 31. `[U06]` DHCP — ¿a qué capa OSI pertenece? `(2C25 #20)`
¿A qué capa del modelo OSI pertenece el protocolo DHCP, utilizado para la asignación automática de direcciones IP?
- A) Capa de transporte
- B) No pertenece a ninguna capa en particular
- C) Capa de enlace de datos
- D) Capa de aplicación
- E) Capa de red

**Tu respuesta:**

_…_

### 32. `[U08]` Enlace — función principal de la capa `(banco #20 · 2C25 #24 · 1Q25 #13)`
¿Cuál es la función principal de la capa de enlace?
- A) Transportar paquetes entre routers adyacentes.
- B) Transmitir paquetes entre los hosts finales.
- C) Transportar paquetes entre hosts adyacentes.
- D) Evitar colisiones en las tramas.
- E) Reservar recursos en la red para la transmisión.

**Tu respuesta:**

_…_

### 33. `[U08]` Switch — ¿100% seguro contra sniffing? `(banco #43)`
**V/F.** "En una LAN donde los hosts están interconectados con un hub, el resto de los hosts pueden 'sniffear' los contenidos de las tramas. Reemplazar el hub por un switch es un método 100% seguro e infalible para que los mensajes unicast no puedan ser vistos por otro." Justificar.

**Tu respuesta:**

_…_

### 34. `[U10]` SSH — acceso a SMTP local desde afuera `(banco #29 · 2C25 #42)`
**V/F.** "En una red privada hay un host con un servidor SSH y otro con un servidor SMTP que acepta conexiones únicamente de los hosts de la red local. Si desde afuera tengo acceso al servidor SSH, entonces también podré acceder al servidor SMTP." Justificar.

**Tu respuesta:**

_…_

---

## Tanda 4

### 35. `[U02]` HTTP — caché del recurso y POST `(banco #44)`
**V/F**, justificar: "Al solicitar un recurso a un servidor HTTP, si el recurso existe y es recibido correctamente, el mismo se guarda en la caché local, y en caso de usar un proxy también será 'cacheado' por el proxy."

**Tu respuesta:**

_…_

### 36. `[U04]` TLS vs SSL `(banco #25 · 2C25 #37)`
**V/F.** "TLS se diferencia de SSL principalmente en que la conexión se establece en el puerto estándar y sin seguridad. Una vez establecida se puede negociar el uso de TLS y se comienza a usar sobre la misma conexión, no sobre otro puerto especial como requiere SSL." Justificar.

**Tu respuesta:**

_…_

### 37. `[U05]` NAT — ¿4 hosts → 4 entradas? `(banco #28 · 1Q25 #16)`
**V/F.** "Sea una red local con IP privada donde 4 hosts están conectados a un router con firewall que hace NAT (y tiene una IP pública). Al ser sólo 4 hosts, es correcto afirmar que el router tendrá a lo sumo 4 entradas en su tabla de NAT." Justificar.

**Tu respuesta:**

_…_

### 38. `[U05]` SNAT — campos modificados `(2C25 #14)`
Durante el proceso de SNAT, ¿qué campos de los encabezados IP y de transporte son modificados?
- A) La IP de destino (IP) y el puerto de destino (TCP/UDP).
- B) Solo el TTL y el checksum del encabezado IP.
- C) Ningún campo; el SNAT solo registra la sesión en una tabla.
- D) La IP de origen (IP) y, si es necesario, el puerto de origen (TCP/UDP).

**Tu respuesta:**

_…_

### 39. `[U06]` Tabla de ruteo — entradas incorrectas `(banco #11 · 1Q25 #26)`
Marcar cuál/es de las siguientes entradas para una tabla de ruteo son **incorrectas**.
- A) 0.0.0.0/0
- B) 10.0.0.128/24
- C) 192.168.0.1/24
- D) 10.1.2.128/25
- E) 127.0.0.1/32
- F) 0.0.0.0 mask 0.0.0.0

**Tu respuesta:**

_…_

### 40. `[U06]` Subredes válidas de 10.15.0.0/16 `(banco #32 · 2C25 #10 · 1Q25 #17)`
Dada la red 10.15.0.0/16, marcar cuál/es de las siguientes son subredes válidas que se pueden crear.
- A) 10.15.1.0/16
- B) 10.15.0.0/24
- C) 10.15.255.0/24
- D) 10.15.15.0/24
- E) 10.17.1.0/24

**Tu respuesta:**

_…_

### 41. `[U06]` Red /30 (255.255.255.252) `(2C25 #13)`
¿Para qué se utiliza normalmente una red con máscara 255.255.255.252 (prefijo /30)?
- A) Para asignar IPs públicas a servidores web.
- B) Para segmentar una red inalámbrica en cuatro subredes iguales.
- C) Para conectar hasta 254 hosts dentro de una misma red local.
- D) Para crear enlaces punto a punto (WAN) entre dos dispositivos de red, como routers.

**Tu respuesta:**

_…_

### 42. `[U08]` Modo promiscuo por cable `(banco #21 · 2C25 #22)`
**V/F.** "Si mi red es por cable (no wifi), al habilitar el 'modo promiscuo' de mi placa puedo capturar todo el tráfico que circula por mi red." Justificar.

**Tu respuesta:**

_…_

### 43. `[U08]` Confiabilidad enlace → red `(banco #23 · 2C25 #30)`
**V/F.** "Si un paquete debe llegar desde el host A hasta el host Z pasando antes por M y N, y se sabe que el protocolo de enlace entre A-M, M-N y N-Z son confiables, entonces el protocolo de red entre A y Z también será confiable." Justificar.

**Tu respuesta:**

_…_

### 44. `[U10]` Túnel SSH remoto — ejemplo `(banco #31 · 2C25 #3 · 1Q25 #28)`
Mencionar un ejemplo donde sea necesario crear un túnel SSH **remoto**. Indicar cómo un túnel remoto resuelve lo que se necesita hacer y cómo se usaría.

**Tu respuesta:**

_…_

---

## Tanda 5

### 45. `[U02]` HTTP — recurso sin acceder al servidor `(banco #46 · 2C25 #8)`
**V/F**, justificar: "La única posibilidad de solicitar un recurso desde mi browser y obtenerlo sin acceder al servidor HTTP es porque el proxy tiene una copia del mismo."

**Tu respuesta:**

_…_

### 46. `[U05]` Transporte — mínimo delay `(banco #37 · 2C25 #28)`
¿Cuál/es de los siguientes protocolos me garantiza un mínimo delay en el envío de paquetes?
- A) SCTP
- B) TCP
- C) Ninguno
- D) IP
- E) UDP

**Tu respuesta:**

_…_

### 47. `[U06]` traceroute — `***` en la salida `(banco #12 · 2C25 #5 · 1Q25 #31)`
Asumiendo que traceroute utiliza ICMP echo request (ping), indicar **V/F** y justificar si es falsa: "Si al ejecutar traceroute en algunas líneas aparece `***` en vez de un IP, eso quiere decir que ese router no está respondiendo a los ICMP echo request."

**Tu respuesta:**

_…_

### 48. `[U06]` ISP — 256 IPs para 500 casas `(banco #33 · 2C25 #9)`
**V/F**, justificar: "Si un ISP de un pueblo de 500 casas adquiere 256 direcciones IP públicas, entonces no podrá venderle Internet a todas las casas del pueblo."

**Tu respuesta:**

_…_

### 49. `[U06]` Router — múltiples coincidencias en la tabla `(2C25 #15)`
Cuando un router encuentra varias coincidencias posibles entre la IP de destino de un paquete y las entradas de su tabla de ruteo, ¿qué criterio usa para decidir por cuál interfaz reenviar?
- A) La ruta con el menor número de interfaz disponible.
- B) Selecciona aleatoriamente una para balancear la carga.
- C) La ruta que apareció primero en la tabla.
- D) La ruta con el prefijo más largo (la coincidencia más específica).

**Tu respuesta:**

_…_

### 50. `[U07]` Routing — objetivo de un protocolo de routing `(2C25 #23 · 1Q25 #24)`
El objetivo de un protocolo de routing es:
- A) Brindar información a un administrador para poder modificar las tablas de forwarding
- B) Juntar información de los hosts de una red
- C) Forwardear paquetes a través de múltiples redes
- D) Juntar información que permita un armado óptimo de las tablas de ruteo

**Tu respuesta:**

_…_

### 51. `[U08]` ARP spoofing `(banco #22 · 2C25 #40)`
¿Qué tipo de ataque se puede llevar a cabo mediante la manipulación de mensajes ARP para asociar IPs legítimas con direcciones MAC maliciosas?
- A) Ataque de denegación de servicio (DoS)
- B) Ataque de suplantación de identidad (spoofing)
- C) Ataque de fuerza bruta
- D) Ataque de inundación de la red

**Tu respuesta:**

_…_

### 52. `[U08]` ARP en IPv6 (trampa) `(2C25 #38)`
Dada una red **IPv6**, ¿en qué casos se realiza una petición ARP? Marcar todas las correctas.
- A) Un host necesita enviar un paquete IP a un host en otro segmento pero no conoce la MAC del gateway
- B) Un host necesita enviar un paquete IP a un host en su segmento pero no conoce su MAC
- C) Un host conoce su propia dirección IP pero no su MAC
- D) Un host necesita enviar un paquete IP a un host en otro segmento y no conoce la MAC de ese host
- E) Un host conoce su dirección MAC pero no su dirección IP

**Tu respuesta:**

_…_

### 53. `[U10]` Túnel remoto vs túnel local `(banco #39)`
**V/F**, justificar: "Usando SSH, si puedo acceder a un servidor usando un túnel remoto, entonces también podré acceder con un túnel local."

**Tu respuesta:**

_…_

---

## Tanda 6

### 54. `[U05]` Carta simple — analogía `(banco #38 · 2C25 #29)`
Cuando envía por correo una carta simple, asumiendo que fue recibida por el destinatario, esto es análogo a:
- A) UDP
- B) TCP
- C) Ninguno

**Tu respuesta:**

_…_

### 55. `[U06]` IPv6 — tres ventajas sobre IPv4 `(banco #13 · 2C25 #1 · 1Q25 #33)`
Nombrar tres ventajas del protocolo IPv6 sobre IPv4.

**Tu respuesta:**

_…_

### 56. `[U06]` TTL y switches en ciclo `(banco #40 · 2C25 #43)`
**V/F**, justificar: "El TTL en IP existe para evitar que un paquete circule indefinidamente en el caso de tener switches conectados en ciclo."

**Tu respuesta:**

_…_

### 57. `[U06]` iptables — tabla filter `(2C25 #17)`
¿Cuál es la función principal de la tabla `filter` en iptables?
- A) Realizar NAT para permitir el acceso entre redes privadas y públicas.
- B) Modificar campos del encabezado IP, como el TTL o el TOS.
- C) Administrar la prioridad del tráfico y el ancho de banda entre interfaces.
- D) Controlar el filtrado de paquetes (aceptar, rechazar o descartar según reglas de firewall, por puerto o IP).

**Tu respuesta:**

_…_

### 58. `[U07]` Routing — OSPF vs RIP `(2C25 #26)`
**V/F.** "Si una empresa usa OSPF como protocolo de routing, entonces no usa RIP (ni v1 ni v2)." Justificar.

**Tu respuesta:**

_…_

### 59. `[U08]` Direcciones MAC — ¿para qué sirven? `(banco #24 · 2C25 #35)`
¿Para qué sirven las direcciones MAC?
- A) Identificar dispositivos en una red local.
- B) Traducir direcciones IP a direcciones físicas.
- C) Enrutar paquetes a través de múltiples redes.
- D) Establecer sesiones de comunicación entre hosts.

**Tu respuesta:**

_…_

### 60. `[U11]` Socket pasivo TCP `(banco #18 · 2C25 #34 · 1Q25 #12)`
**V/F.** "Un socket pasivo TCP se usa únicamente para atender los pedidos de conexión, no para recibir datos de una conexión establecida." Justificar.

(Variante 1Q25 #12: "En TCP el socket pasivo escucha en un puerto determinado, pero luego cada socket activo tendrá que escuchar en un puerto distinto." V/F.)

**Tu respuesta:**

_…_
