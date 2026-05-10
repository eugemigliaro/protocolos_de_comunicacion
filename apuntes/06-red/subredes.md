# Subredes

**Subnetting** es lo opuesto a superred: tomar **una red grande y dividirla** internamente en redes más chicas, "robándole" bits a la parte de host para usarlos como ID de subred. Cada subred tiene su propia máscara y se administra como una red aparte.

## Por qué subnetear

- **Topológicas:** hosts distantes, distintas capas físicas, filtrar tráfico entre redes.
- **Organizacionales:** simplificar administración, mapear estructura de la organización, aislar tráfico.
- **Práctico:** una clase B con 65 534 hosts es inadministrable como un único broadcast domain.

## Cómo se hace

Si una red es `/N`, le robo `n` bits a la parte de host y formo `2ⁿ` subredes, cada una con `2^(32 − N − n) − 2` hosts útiles (resto la red y el broadcast de cada subred).

### Ejemplo: subredes de tamaño fijo

Red `192.100.32.0/24` dividida en subredes de **12 hosts** cada una.

- Para 12 hosts útiles necesito al menos `2⁴ − 2 = 14` IPs → 4 bits de host.
- Quedan `8 − 4 = 4` bits para subredes → `2⁴ = 16` subredes.
- Cada subred es `/28`, con 14 hosts útiles.

## VLSM (Variable Length Subnet Masking)

Distintas subredes con **máscaras distintas**, según cuántos hosts necesite cada una. Permite no desperdiciar direcciones — fundamental para enlaces punto a punto.

### Ejemplo: VLSM

Red `172.16.14.0/24` dividida así:

| Subred | Máscara | Direcciones | Hosts útiles | Uso |
|--------|---------|-------------|--------------|-----|
| `172.16.14.32/27` | `/27` | 32 | 30 | Red R1 (LAN) |
| `172.16.14.64/27` | `/27` | 32 | 30 | Red R2 (LAN) |
| `172.16.14.96/27` | `/27` | 32 | 30 | Red R3 (LAN) |
| `172.16.14.132/30` | `/30` | 4 | 2 | Enlace R1 ↔ G |
| `172.16.14.136/30` | `/30` | 4 | 2 | Enlace R2 ↔ G |
| `172.16.14.140/30` | `/30` | 4 | 2 | Enlace R3 ↔ G |

Las `/27` van a LANs con muchos hosts. Las `/30` (sólo 2 hosts útiles) son **ideales para enlaces punto a punto entre routers**.

### Pasos para diseñar VLSM

1. Determinar la subred con la **mayor cantidad de hosts** y armarla primero (`/24`, `/23`, etc.). Repetir para todas las subredes grandes.
2. Tomar bloques restantes y subdividirlos para subredes más chicas.
3. Para enlaces punto a punto, usar `/30` (2 hosts útiles).

## Tabla de ruteo en una red subneteada

Cada router tiene una entrada por cada red conectada. Las redes locales (directly connected) llevan gateway "—" porque el host está en la misma red. Cuando una IP matchea con varias entradas (ej.: una `/30` específica y la default `/0`), gana la **más específica** — *longest prefix match*.

## IP privadas vs públicas en subnetting

Las subredes internas suelen usar **IPs privadas** (RFC 1918: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) y salen a Internet con **una sola IP pública** vía NAT. Si usaran públicas en cada subred sería un desperdicio enorme.

## Aparece en parciales

- *Guía 5, Ej. 7:*
  - Máx bits para subredes en clase C → 6 (8 bits de host − 2 para tener al menos 2 hosts).
  - `204.211.19.162` con `255.255.255.224` → 5 bits libres, 3 para subred (los otros 2 para hosts).
  - Clase B con 4 bits de subred → máscara `255.255.240.0`.
  - IP AND máscara → AND lógico.
- *Guía 5, Ej. 9:* Clase C, 10 subredes con la mayor cantidad de hosts → 4 bits de subred → máscara `255.255.255.240` (`/28`).
- *Guía 5, Ej. 10:* Clase C `/28` → **16 redes con 14 hosts** cada una.
- *Guía 5, Ej. 11:* dirección de red de `172.16.210.0/22` → `172.16.208.0`.
- *Guía 5, Ej. 15:* clase B, 459 hosts/subred → `2⁹ = 512` direcciones → 9 bits de host → 7 bits para subred → máscara `/23` = `255.255.254.0`.
- *Guía 5, Ej. 18:* enlace punto a punto VLSM → máscara `/30` = `255.255.255.252` (2 hosts útiles).
- *Preguntas teóricas, Ej. 45:* ISP con 256 IPs y 3 empresas de 80 → no alcanza.
- *Preguntas teóricas, Ej. 32:* qué subredes son válidas dentro de `10.15.0.0/16`.

> Fuente: Apuntes oficiales, Unidad 6, p. 79-83.
