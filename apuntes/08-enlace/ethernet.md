# Ethernet

Tecnología de red de área local (LAN) para transmisión de datos entre dispositivos. Sus principales ventajas son que es muy **económica** y **simple**.

Usa CSMA/CD como protocolo de acceso al medio (ver `tipos-de-comunicacion.md`).

## Topologías

### Bus

Una de las primeras topologías. Todos los dispositivos están conectados a un único cable compartido (**bus** o **backbone**).

- Alta probabilidad de colisiones.
- Si la red crece, se la divide en **segmentos** (dominios de colisión).

### Estrella

Topología moderna. Cada host se conecta a un **switch central**.

- Cada host se puede conectar/desconectar sin afectar al resto.
- El switch dirige los datos al puerto del destinatario y puede encolar tramas.
- Se basa en el número de placa (**MAC address**).
- Hay que evitar ciclos.

## Hub vs Switch

| | Hub | Switch |
|---|---|---|
| Capa OSI | 1 (física) | 2 (enlace) |
| Decisión de envío | Repite a todos los puertos | Mira la MAC y reenvía solo al puerto del destino |
| Dominio de colisión | Compartido | Por puerto |
| Sniffing en modo promiscuo | Captura todo el tráfico de la LAN | No captura tráfico ajeno (en condiciones normales) |

**Un switch opera en capa 2 y usa direcciones MAC para tomar decisiones.**

El TTL de IP **no** evita ciclos entre switches: el switch trabaja en capa 2 y no analiza headers IP.

## Segmentar una LAN

Para segmentar una LAN se usa un **router**: separa la red en uno o más segmentos y regula el tráfico.

Beneficios principales de segmentar:

- Los **mensajes broadcast no pasan de un segmento a otro** (beneficio principal).
- Los broadcast no son rechazados, simplemente no atraviesan el router.
- Si había muchas colisiones, segmentar también puede hacer que los paquetes lleguen más rápido (porque hay que retransmitir menos).

> Ejemplo típico (guía 7, ej. 1): si al cambiar un hub por un switch el tráfico sigue lento, probablemente algún host esté generando muchos broadcasts. Segmentar la red en subredes con un router corta la propagación de esos broadcasts.

## Dirección MAC

Identificador único asignado a la interfaz de red de un dispositivo (NIC). Permite identificar y comunicarse en la capa de enlace (capa 2 de OSI).

### Formato

- **48 bits** (6 bytes) de longitud.
- Se representa en hexadecimal, en 6 grupos de 2 dígitos separados por `:` o `-`. Ej: `00:1A:2B:3C:4D:5E`.
- **Primer bloque (3 bytes)**: **OUI** (Organizationally Unique Identifier). Identifica al fabricante.
- **Últimos 3 bytes**: identificador único del dispositivo asignado por el fabricante.
- **Global vs local**: si el primer bit del primer byte es `0` la dirección es globalmente única (asignada por el fabricante); si es `1`, es localmente administrada (configurada por el admin de red).

### Cambiar la MAC con `ifconfig`

`ifconfig` permite cambiar temporalmente la MAC de una interfaz. Si dos hosts terminan con la misma MAC, cuando se envíe un paquete IP a cualquiera de los dos, **a nivel de enlace lo recibirán ambos**.

### Para qué sirven las direcciones MAC

Sirven para **identificar dispositivos en una red local**. No sirven para enrutar entre redes (eso lo hace IP) ni para traducir direcciones (eso lo hace ARP).

## Trama Ethernet

Estructura del frame:

```
+-----------+-----+-------+--------+-----------+----------+--------+-----+
| Preámbulo | SFD | MAC   | MAC    | EtherType | Datos    | CRC    |     |
|           |     | dest  | origen |           | (46-1500)|        |     |
+-----------+-----+-------+--------+-----------+----------+--------+-----+
```

- **Preámbulo**: secuencia de bits alternando 1 y 0 que sincroniza emisor y receptor. Permite al receptor detectar el inicio de la trama.
- **SFD (Start Frame Delimiter)**: secuencia especial `10101011` que marca el comienzo real de la trama y el final del preámbulo.
- **MAC destino**: dirección MAC del receptor.
- **MAC origen**: dirección MAC del emisor.
- **EtherType**: protocolo de la capa superior. Valores comunes:
  - `0x0800` → IPv4
  - `0x0806` → ARP
  - `0x86DD` → IPv6
- **Datos (payload)**: 46 a 1500 bytes (de ahí el MTU de 1500 de Ethernet).
- **CRC**: valor de chequeo para asegurar la integridad de la trama.

## Aparece en parciales

- **Switch en capa 2 con direcciones MAC** (guía 7, ej. 2 — opción correcta).
- **Para segmentar una LAN se usa un router** (guía 7, ej. 3).
- **Beneficio de segmentar: los broadcast no pasan de un segmento a otro** (guía 7, ej. 4).
- **Cambiar la MAC para que coincida con otro host** → ambos reciben el mismo paquete a nivel de enlace (guía 7, ej. 5).
- **Modo promiscuo en una red con switch**: NO captura todo el tráfico (preguntas teóricas, ej. 21).
- **Switch + unicast no es 100% seguro**: existen ataques como ARP spoofing (preguntas teóricas, ej. 43).
- **TTL e IP no protegen ciclos entre switches**: capas distintas (preguntas teóricas, ej. 40).
- **Para qué sirven las MAC**: identificar dispositivos en una red local (preguntas teóricas, ej. 24).

## Fuentes

- PDF unidad 8, p. 114-117.
- Guía 7 (Enlace), ej. 1-5.
- Preguntas teóricas: ej. 21, 24, 40, 43.
