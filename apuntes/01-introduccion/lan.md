# LAN (Local Area Network)

Una **LAN** es una red local: hosts conectados dentro de un área acotada (oficina, edificio, casa). Existen dos topologías clásicas: **bus** y **estrella**.

## Topología de bus

En las primeras redes había un **cable coaxil** corriendo por toda la oficina, y cada computadora se "colgaba" del cable.

```
   [PC]     [PC]     [PC]     [PC]
    |        |        |        |
====+========+========+========+====   ← cable coaxil compartido
```

Características:

- Cada host **ve todo** lo que se transmite (el medio es compartido).
- Alta posibilidad de **colisiones**.
- Si la red crece, se la divide en **segmentos** para reducir colisiones (cada segmento es un *dominio de colisión*).

### Hub (evolución del bus)

Hoy no se usa el cable directo: se usa un **hub**. Pero el hub trabaja **solo a nivel físico**: replica eléctricamente la señal a todos sus puertos. Funcionalmente es equivalente al bus.

- Todo lo que entra por un puerto sale por todos los demás.
- Sigue habiendo colisiones y *sniffing* trivial.

## Topología de estrella

Cada host se conecta a un **switch** central.

```
        [PC]
         |
[PC] -- [SW] -- [PC]
         |
        [PC]
```

El switch:

- Trabaja a **nivel de enlace** (capa 2, mira MACs).
- Aprende qué MAC está en qué puerto y dirige cada trama solo por el puerto correspondiente.
- Puede **encolar** distintas tramas simultáneamente.
- Reduce colisiones drásticamente (cada puerto es su propio dominio).

### Qué ve un host conectado a un switch

- Las tramas que lo tienen como **destino** (su MAC).
- Las tramas **broadcast**.
- Las tramas cuyo destino el switch **todavía no aprendió** en qué puerto está (las inunda por todos los puertos).

### ¿Es seguro contra sniffing?

**No al 100%**. Aunque un switch limita el tráfico que ve cada host, existen ataques que igual permiten interceptar:
- **ARP spoofing**: hago creer al switch / a otros hosts que mi MAC es la del gateway.
- **MAC flooding**: lleno la tabla del switch para forzarlo a comportarse como hub.

## Aparece en parciales

- **Ej 4 (Guía 1)**:
  - "Si todas las PCs están con un hub, cualquiera puede ver los mensajes de las demás." → **VERDADERO**.
  - "Si todas las PCs están con un switch, una PC sólo ve los mensajes con su MAC como destino." → **FALSO**. También ve broadcast y los que el switch aún no aprendió.
  - "Si reemplazo el hub por un switch es probable que aumente la velocidad." → **VERDADERO**.
- **Ej 21 (preguntas)**: "En modo promiscuo puedo capturar todo el tráfico de mi red cableada." → **FALSO si hay switch**. Solo veo lo dirigido a mí, broadcast, y lo no aprendido.
- **Ej 35 (preguntas)**: "Ethernet establece una longitud máxima de cable. Si en una LAN con hub un host tiene cable más largo, no podrá enviar/recibir." → **FALSO**. No se garantiza el funcionamiento, pero no es necesariamente imposible.
- **Ej 43 (preguntas)**: "Reemplazar hub por switch es 100% seguro contra sniffing." → **FALSO**. Existen ataques como ARP spoofing.

## Referencias

- PDF, Unidad 1, p. 8-9.
- Guía 1, Ej 4.
