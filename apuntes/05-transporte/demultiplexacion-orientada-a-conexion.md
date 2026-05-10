# Demultiplexación orientada a conexión

En TCP (y SCTP), un socket no se identifica por `<IP, puerto>` (eso es UDP) sino por la **cuádrupla**:

- IP origen
- Puerto origen
- IP destino
- Puerto destino

El host receptor usa los **4 valores** para dirigir cada segmento al proceso correcto.

## Conexiones simultáneas

Cuando varios clientes se conectan a un mismo servidor, **cada conexión opera con su propio socket**. Si 3 clientes se conectan al puerto 80 del servidor:

- Las **IPs destino y puerto destino son iguales** (server, 80).
- Las **IPs y puertos origen son distintos** (uno por cliente).
- El servidor abre **un socket por cliente**, identificados por la cuádrupla completa.

Si dos conexiones vinieran de la **misma máquina origen**, tendrían la misma IP origen pero **puertos origen distintos**, lo que sigue diferenciándolas.

## Socket pasivo vs socket activo

| Tipo | Para qué sirve | Cuándo se crea |
|---|---|---|
| **Pasivo** (listen) | **Solamente** atender pedidos de conexión nuevos | El servidor lo crea al hacer `listen()` antes de aceptar nada |
| **Activo** | Intercambiar datos y cerrar la conexión con un cliente concreto | Se crea **al aceptar** una conexión (después del three-way handshake), uno por cliente |

El socket pasivo **no se usa para recibir datos** de una conexión ya establecida — solo escucha en `<IP destino, puerto destino>` esperando SYN nuevos.

## Ejemplo: web server con 3 clientes

```
                                            +-----------+
   Cliente A (IP_A:5001) ─────► SYN ────►   |  Server   |
   Cliente B (IP_B:5002) ─────► SYN ────►   |  IP_S:80  |
   Cliente C (IP_A:5003) ─────► SYN ────►   |           |
                                            +-----------+
                                            socket pasivo: <*,*,IP_S,80>
                                            socket activo A: <IP_A,5001,IP_S,80>
                                            socket activo B: <IP_B,5002,IP_S,80>
                                            socket activo C: <IP_A,5003,IP_S,80>
```

Notar que A y C salen de la **misma IP** pero con puertos origen distintos → son dos sockets activos diferentes.

## Aparece en parciales

- "Suponga un web server que escucha en host C en puerto 80, conexiones persistentes, recibe requests de A y B. ¿Todos los requests van al mismo socket? ¿Usan ambos 80 como puerto destino?" → Todos los **pedidos de conexión** van al **socket pasivo**. Tras el handshake se crea un **socket activo** identificado por la cuádrupla. **Sí**, todos siguen usando 80 como puerto destino (guía 4 ej. 8).
- "¿Cuál es la diferencia entre un socket pasivo y un socket activo?" → El pasivo se usa **únicamente para recibir nuevas conexiones**; el activo, para intercambiar datos y cerrar la conexión (guía 4 ej. 14).
- "Un socket pasivo TCP se usa únicamente para atender los pedidos de conexión, no para recibir datos de una conexión establecida." → **Verdadero** (preguntas-teoricas ej. 18).

## Fuente

- Apunte oficial, Unidad 5, p. 54-55.
- Guía 4 (Transporte), ej. 8, 14.
