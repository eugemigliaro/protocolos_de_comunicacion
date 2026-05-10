# Routers

El **router** es el equipo encargado de **interconectar redes distintas**. Por cada paquete que recibe, mira el IP destino y decide por qué interfaz lo tiene que reenviar.

## Características clave

- Trabaja a **nivel de red** (capa 3): toma decisiones en base a **direcciones IP**, no MAC.
- Puede conectar **distintas tecnologías** de capa enlace: Ethernet, Token Ring, WiFi, etc.
- **Separa** una red en uno o más segmentos y **regula el tráfico** entre ellos.
- Cada **interfaz** del router pertenece a una red distinta.

## Diferencia con switch y hub

| | Hub | Switch | Router |
|---|---|---|---|
| Capa | Física (1) | Enlace (2) | Red (3) |
| Decide en base a | Nada — replica todo | MAC | IP |
| Interconecta | El mismo segmento | El mismo segmento (con dominios de colisión separados) | **Redes distintas** |

## Cuántas interfaces tiene

Cada interfaz del router conecta a **una red**, no a un host.

Si en una red hay 4 PCs que necesitan acceder a otras redes, el router **no necesita 4 interfaces**: alcanza con **1** del lado de esas PCs (las 4 PCs se conectan al router a través de un switch o hub con al menos 5 puertos: 4 para las PCs + 1 para el router).

## Aparece en parciales

- **Ej 2 (Guía 1)**: "Un router conecta 2 o más redes." → **VERDADERO**.
- **Ej 2 (Guía 1)**: "Si en una red cableada hay 4 computadoras que deben acceder a otras redes, el router tendrá que tener al menos 4 interfaces." → **FALSO**. Cada interfaz conecta una **red**, no un host. Las 4 PCs se conectan a un switch/hub y desde ahí una sola interfaz al router.

## Referencias

- PDF, Unidad 1, p. 9.
- Guía 1, Ej 2.
