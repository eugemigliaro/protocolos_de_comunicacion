# Routing Dinámico

Routing en el que la tabla de ruteo se construye automáticamente a partir de información intercambiada entre routers, en lugar de cargarla a mano. Se basa en **algoritmos adaptativos**: las decisiones de ruteo cambian según el estado real de la red (congestión, ancho de banda, demoras, caídas de enlace).

A diferencia de los algoritmos no adaptativos (rutas fijas), los adaptativos reaccionan a los cambios y mantienen el rendimiento estable.

## IGP vs EGP

Los protocolos de routing dinámico se dividen en dos categorías según el alcance:

- **Routing interno (IGP — Interior Gateway Protocol)**: para una red controlada por una sola organización. Los más usados: **RIP** y **OSPF**.
- **Routing externo (EGP — Exterior Gateway Protocol)**: para intercambiar información entre redes controladas por organizaciones distintas (sistemas autónomos). Se usa **BGP**.

## Algoritmos: vector distancia vs link state

Dos familias grandes de algoritmos. Cada una tiene su protocolo representativo.

### Vector distancia

Cada router mantiene una tabla con la mejor ruta conocida hacia cada red destino. La entrada incluye:

- destino,
- distancia (típicamente número de saltos),
- siguiente salto (*next hop*).

Periódicamente, cada router intercambia su **tabla completa** con sus vecinos. Con eso, cada uno actualiza la suya.

Idea clave: un router solo sabe lo que le cuentan sus vecinos. No conoce la topología completa.

#### Problema: contar hasta el infinito

Cuando una ruta se vuelve inalcanzable, los vecinos pueden seguir intercambiando información vieja entre sí, creyendo que la red sigue siendo accesible vía otro camino. La métrica (saltos) crece de a poco con cada update y el destino real ya no existe — pero nadie se entera rápido.

Soluciones:

- **Split horizon**: no le envío a mi vecino lo que mi vecino me está mandando.
- **Ruta envenenada (poisoned reverse)**: lo que mi vecino me manda, se lo devuelvo con métrica infinita, así no la actualiza.
- **Triggered updates ("enviar rápido las malas noticias")**: si un router detecta que un enlace cayó, manda inmediatamente la entrada envenenada en vez de esperar al próximo update periódico.

### Link state

Cada router descubre **directamente** el estado de sus enlaces (activos/no, ancho de banda, costo) y distribuye esa información a **todos** los routers de la red mediante **LSAs (Link State Advertisements)**.

Con todos los LSAs, cada router arma un grafo completo de la red y calcula los caminos más cortos (Dijkstra).

Pasos que sigue cada router:

1. Descubrir sus vecinos.
2. Medir la distancia (demora o costo) a cada vecino.
3. Construir un paquete con lo que aprendió.
4. Enviar el paquete a todos los routers de la red.
5. Calcular el camino más corto a cada router.

### Comparación rápida

| | Vector distancia | Link state |
|---|---|---|
| Qué conoce cada router | Sólo lo que le dicen sus vecinos | La topología completa |
| Qué intercambia | Tabla de rutas completa | Estado de sus enlaces (LSAs) |
| Frecuencia | Periódica (ej. RIP cada 30s) | Cuando hay cambios de topología |
| A quién le manda | A los vecinos directos | A todos los routers |
| Algoritmo de cálculo | Acumular distancia recibida + 1 | Dijkstra sobre el grafo |
| Protocolo típico | RIP | OSPF |

## RIP (Routing Information Protocol)

Pertenece a vector distancia. Selecciona la ruta por **número de saltos**. Simple de configurar, adecuado para redes chicas.

### RIP v1

- Solo IPv4.
- Anuncios por **broadcast**.
- Updates cada **30 segundos** intercambiando con vecinos.
- Diámetro máximo: **15 saltos** (16 = inalcanzable).
- Sin autenticación.

### RIP v2

- Soporta **subnetting / VLSM**.
- Updates por **multicast** (en vez de broadcast → más seguro).
- Informa direcciones **CIDR**.
- **Autenticación opcional**.
- Compatible con RIP v1.

## OSPF (Open Shortest Path First)

Protocolo IGP de tipo **link state**. Cada router intercambia información sobre sus vecinos con toda la red, y manda updates **solo cuando se detecta un cambio** en la topología.

- Asigna **peso** a los enlaces que unen áreas o routers.
- Cada router arma un grafo de la red y corre **Dijkstra** para sacar el camino más corto.

### Áreas

OSPF permite dividir la red en **áreas**: colecciones interconectadas de redes. Cada área intercambia información con otras a través de un **router de borde (ABR — Area Border Router)**.

```
              ┌──────────┐
              │ Área 0   │  (backbone)
              │          │
              └──┬────┬──┘
                 │    │
              ABR     ABR
                 │    │
            ┌────┴┐  ┌┴────┐
            │Área1│  │Área2│
            └─────┘  └─────┘
```

- Toda área está conectada al **área 0 (backbone)** vía ABRs.
- Los ABRs **sumarizan** las redes del área hacia afuera → reduce el tamaño de las tablas de ruteo.
- El impacto de cambios de topología queda **localizado** en el área donde ocurre.

### Áreas stub

Áreas que no necesitan ABRs con gran capacidad. Su tabla de ruteo se configura típicamente **estática**, con info sobre la subred local y una **ruta por defecto** hacia el resto.

### Implementación

OSPF se implementa con el demonio **`gated`**, que soporta múltiples protocolos (RIP v1, RIP v2, OSPF, BGP). Configuración: `/etc/gated.conf`.

## BGP

Protocolo **EGP** que se usa entre sistemas autónomos (organizaciones distintas). Lo menciona el apunte como el de uso estándar para routing externo, sin entrar en detalle.

## Aparece en parciales

- **Guía 6, ej. 1.3**: enrutamiento dinámico = se adapta automáticamente al tráfico o a cambios de topología.
- **Guía 6, ej. 1.4**: la métrica de RIP es **número de saltos**.
- **Guía 6, ej. 2**: red empresarial con 4 subredes principales × 5 sub-subredes × 50 hosts (1000 hosts totales). Como la cantidad de saltos y subredes no es muy grande, alcanza con **RIP v2** (necesita VLSM por las sub-subredes, RIP v1 no sirve).
- **Guía 6, ej. 3**: si la red está en constante crecimiento, conviene **OSPF** con 4 áreas (una por subred principal), y dentro de cada área RIP v2. Argumento: OSPF escala mejor a muchas subredes y permite localizar el impacto de los cambios.

## Referencias

- Apunte oficial Unidad 7, p. 105-109.
- Guía 6 — Routing, ej. 1, 2 y 3.
