# Superredes

**Superred** es la agregación de varias redes contiguas (típicamente clase C) bajo un único prefijo más corto, para que los routers de afuera vean un único bloque y mantengan **una sola entrada** en su tabla en vez de varias. La técnica de fondo es **CIDR (Classless Inter-Domain Routing)**, que descarta las clases y reemplaza la máscara default por una arbitraria.

## El problema que resuelve

Cuando la cantidad de redes empezó a crecer:

- Las **direcciones clase B se agotaban** (eran muy pocas).
- Una sola **clase C** podía no alcanzarle a una organización (sólo 254 hosts útiles).
- Si se le daban N clases C contiguas, los routers de afuera tenían que tener **N entradas** distintas para esa organización → tablas explotaban.

## La solución: CIDR

Se descartan las clases y se introduce una **máscara explícita** (longitud variable) en cada entrada de tabla:

```
Dirección IP / número de bits de red
```

Ej.: `192.100.0.0/22` significa "los primeros 22 bits son red, los 10 restantes son host". Desde afuera, esa empresa parece **una única red de 2¹⁰ direcciones**, aunque por dentro tenga 4 redes clase C contiguas.

> Esto pudo desplegarse gradualmente porque los routers viejos podían seguir usando las máscaras default de clase A/B/C y los nuevos usaban CIDR. **Compatibilidad para atrás.**

## Cómo armar una superred

1. Tomar el rango de direcciones (primera y última).
2. Convertir a binario y ver **cuántos bits coinciden por la izquierda**.
3. Esos bits son la **parte de red**. El resto es host.
4. La máscara: tantos 1s a la izquierda como bits de red.

### Ejemplo

ISP recibe el rango `192.100.0.0` a `192.100.7.255`.

```
192.100.0.0:    11000000 01100100 00000000 00000000
192.100.7.255:  11000000 01100100 00000111 11111111
                ^^^^^^^^ ^^^^^^^^ ^^^^^                  ← coinciden 21 bits
```

- Bits coincidentes: 21 → red `/21`.
- Bits para host: 11 → `2¹¹ − 2 = 2046` hosts.
- Máscara: `11111111 11111111 11111000 00000000` → `255.255.248.0`.
- Resultado: red `192.100.0.0/21`, máscara `255.255.248.0`, 2046 hosts.

## Longest Prefix Match

Con CIDR, una IP puede matchear con varias entradas en la tabla de ruteo. Gana la **más específica** (la que tiene más bits de red en 1 — la máscara más larga). Por ejemplo, ante un paquete a `172.16.14.141`:

- `172.16.14.140/30` (30 bits) → matchea
- `172.16.14.0/24` (24 bits) → matchea
- `0.0.0.0/0` (default) → matchea

Se elige la `/30`.

## Aparece en parciales

- *Guía 5, Ej. 8:* dada `192.15.205.7` con máscara `255.255.252.0` (`/22`), la red original es `192.15.204.0` (clase C, primeros bits 110), la **superred** es `192.15.204.0/22` y el número de host es 263.
- *Guía 5, Ej. 12:* representar rangos como CIDR.
  - `192.16.0.0` a `192.16.7.0` → 8 redes clase C → `/21` → `192.16.0.0/21`.
  - `192.16.0.0` a `192.16.2.0` → 3 redes (no es potencia de 2) → se descompone: `192.16.0.0/23` + `192.16.2.0/24`.
  - `192.16.0.0` a `192.16.9.0` → 10 redes → no entran en `/21` (cubre 8); se usa `192.16.0.0/21` + `192.16.8.0/23`.
- *Preguntas teóricas, Ej. 45:* ISP con 256 IPs (`/24`), 3 empresas piden 80 IPs c/u. Para 3 subredes hacen falta 2 bits, dejando 6 bits para hosts → `2⁶ = 64 < 80`. **No alcanza.**

> Fuente: Apuntes oficiales, Unidad 6, p. 77-79.
