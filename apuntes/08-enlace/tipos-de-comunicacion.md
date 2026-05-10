# Tipos de Comunicación

La capa de enlace, junto con la capa física, conforma la **capa de acceso**. Su trabajo es resolver la comunicación hacia el siguiente nodo. Los paquetes acá se encapsulan y pasan a llamarse **frames** (tramas).

## Servicios que ofrece la capa de enlace

- **Encapsulamiento**: estructura los datos para que se transmitan organizados y se reconozcan en el destino.
- **Acceso al medio**: decide cuándo y cómo un dispositivo usa el canal compartido sin interferir con otros.
- **Comunicación confiable**: garantiza que los datos lleguen correctamente y en orden, sin duplicados ni pérdidas.
- **Control de flujo**: regula la velocidad de transmisión entre nodos adyacentes para que no se pierdan paquetes.
- **Detección y corrección de errores**.

## Tipos de comunicación

A nivel de enlace existen dos tipos:

- **Red broadcast (LAN)**: un dispositivo envía un mensaje y todos los demás lo reciben. Ejemplo: Ethernet.
- **Red punto a punto (WAN)**: comunicación directa entre dos dispositivos. Ejemplo: dos routers conectados por cable serial o un túnel.

## Protocolos punto a punto

### SLIP

Protocolo muy simple para enviar paquetes IP sobre enlaces seriales (módem, cable serie). Encapsula paquetes IP sin headers complejos: solo agrega un carácter especial para marcar el fin del paquete.

### PPP (Point-to-Point Protocol)

Sucesor de SLIP, definido en la **RFC 1661 (1992)**. Mucho más robusto:

- Encapsulamiento de múltiples protocolos de red, no solo IP.
- Detección de errores con CRC.
- Autenticación de usuarios mediante PAP y CHAP.
- Asignación dinámica de direcciones IP.
- Negociación de opciones (tamaño de trama, compresión).
- Evaluación de la calidad del enlace antes y durante la comunicación.

Se usa en módems, enlaces seriales y VPNs.

## Comunicación broadcast: el problema de las colisiones

En redes broadcast todos los nodos comparten un único canal. Cuando un nodo transmite, la señal la reciben todos los demás. Si dos o más nodos transmiten al mismo tiempo, sus señales se superponen y se pierden: ocurre una **colisión**.

Para evitarlo hace falta un protocolo que defina cuándo y cómo transmiten los nodos. La coordinación se hace usando el mismo canal de datos, así que el mecanismo de acceso al medio tiene que ser eficiente.

## Protocolos de acceso múltiple

Regulan cómo varios nodos comparten el canal sin interferirse. Hay tres familias:

### Acceso aleatorio (Random Access)

- Los nodos transmiten sin coordinación previa.
- Si hay colisión se detecta o se asume, y se retransmite tras esperar un tiempo aleatorio.
- El protocolo define cómo detectar colisiones y cómo actuar frente a ellas.

Ejemplos: ALOHA, CSMA, CSMA/CD.

### Acceso controlado (Controlled Access)

- El acceso al canal es coordinado y ordenado: no hay colisiones.
- Se asignan turnos o permisos para transmitir.

Ejemplo: Token Ring.

### Acceso por división (Channelization)

- El canal se divide en tiempo, frecuencia o códigos.
- Cada nodo tiene su porción asignada.
- No hay colisiones.

## Protocolos concretos

### Token Ring

Tecnología LAN con acceso al medio controlado y determinista, basada en el paso de un **token**:

1. El token circula de nodo a nodo.
2. El nodo que recibe el token puede transmitir sus datos.
3. Después de la transmisión, el token se pasa al siguiente nodo.

Solo el nodo que posee el token transmite, así que no hay colisiones.

### ALOHA Puro

Protocolo de acceso múltiple aleatorio:

1. Un nodo transmite sin verificar si el canal está ocupado.
2. Si no hay colisión, la transmisión es exitosa.
3. Si hay colisión, espera un tiempo aleatorio (**backoff**) y reintenta.
4. Se repite hasta que la transmisión sea exitosa.

### CSMA (Carrier Sense Multiple Access)

Antes de transmitir, el nodo escucha el canal:

1. **Escucha**: verifica si el canal está ocupado.
2. **Transmisión**: si está libre, transmite.
3. **Colisión**: si dos nodos transmiten a la vez, ocurre una colisión.
4. **Repetición**: ambos esperan un tiempo aleatorio (backoff) y reintentan.

### CSMA/CD (Collision Detection)

Variante de CSMA usada en Ethernet. Mejora el manejo de colisiones detectándolas en pleno envío:

1. **Escucha**: verifica si el canal está libre.
2. **Transmisión**: si está libre, transmite.
3. **Detección de colisión**: mientras transmite sigue escuchando. Si detecta otro emisor, hay colisión.
4. **Señal de colisión**: ambos nodos detienen la transmisión y envían una señal de colisión.
5. **Espera y retransmisión**: esperan un tiempo aleatorio y reintentan.

### Detección de colisiones y tamaño mínimo de trama

Si dos nodos transmiten al mismo tiempo una trama de largo `L` sobre un canal con tasa `R`, y el tiempo de propagación entre ellos es `dprop`:

- Si `dprop < L/R`, puede haber colisión y los nodos sí la detectan a tiempo (porque el primer bit del otro llega antes de terminar de enviar el último bit propio).
- Si `dprop ≥ L/R`, puede haber colisión sin que los emisores la noten.

Por esto Ethernet establece **longitud máxima de cable** y **tamaño mínimo de trama**: garantizan que dos hosts en los extremos del cable detecten cualquier colisión.

## Aparece en parciales

- **Función principal de la capa de enlace**: transportar paquetes entre **hosts adyacentes** (no entre routers, no entre hosts finales).
- **Control de flujo en capa de enlace**: regular la velocidad entre nodos adyacentes para no perder paquetes. No confundir con control de congestión (TCP).
- **Confiabilidad por tramos no implica confiabilidad punto a punto**: que cada salto sea confiable significa que cada tramo detecta y corrige errores en su tramo, no que la red entre A y Z lo sea.
- **Longitud máxima de cable Ethernet**: si se supera, no se garantiza el funcionamiento, pero no necesariamente la comunicación es imposible.

## Fuentes

- PDF unidad 8, p. 110-114.
- Guía 7 (Enlace), ej. 12.
- Preguntas teóricas: ej. 16, 20, 23, 35.
