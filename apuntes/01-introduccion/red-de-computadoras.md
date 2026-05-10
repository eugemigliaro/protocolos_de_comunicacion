# Red de Computadoras

Una red de computadoras es un conjunto interconectado de **hosts**, donde cada host es un equipo autónomo con sistema operativo y los protocolos necesarios para conectarse vía Internet a otro host. No tienen que ser PCs como las que usamos: cualquier dispositivo autónomo con stack de red cuenta como host.

## Cómo se transmite información

Los hosts envían datos en forma de **paquetes**. El procedimiento es:

1. Toman datos de la aplicación.
2. Dividen la información en paquetes de **L** bits de longitud.
3. Transmiten el paquete en la red a una tasa de transmisión **R** (ancho de banda o *bandwidth*).

A cada paquete se le agrega dirección de **origen** y **destino** para que pueda ser ruteado.

## Demora de transmisión, latencia y ancho de banda

- **Demora de transmisión** (de un paquete): tiempo necesario para transmitir L bits desde el host al medio. Se calcula como `L / R`.
- **Latencia**: velocidad a la que viaja un bit por el medio (no es lo mismo que el ancho de banda).
- **Ancho de banda (bandwidth, R)**: tasa de transmisión, en bits por segundo.

### Analogía intuitiva

Supongamos que vamos a Mar del Plata. Tenemos dos rutas:
- Ruta A: 200 km/h, **1 carril**.
- Ruta B: 100 km/h, **10 carriles**.

Si mandamos un solo auto, conviene la ruta A (mayor latencia → llega antes).
Si mandamos muchos autos, conviene la ruta B (mayor ancho de banda → más volumen por unidad de tiempo).

→ Latencia y ancho de banda son cosas distintas.

## Encolamiento y *store-and-forward*

A un router le llegan paquetes que tiene que reenviar. Si la tasa del link de entrada es superior a la tasa de transmisión durante un período:

- Se **encolan** paquetes esperando ser transmitidos.
- Si el buffer del router está lleno, los paquetes entrantes se **droppean** (se descartan).

**Store-and-forward**: el paquete tiene que arribar al router en forma completa antes de ser retransmitido. Esto agrega `L/R` segundos de demora por cada salto.

## Aparece en parciales

- **Guía 1, Ej 1**: dado un setup con dos hosts y un router con anchos de banda conocidos, calcular el tiempo total de transmisión. *Trampa: no se puede calcular exacto* porque no se conocen ni la latencia ni el tiempo de procesamiento del router (y si hace store-and-forward o cut-through).

## Referencias

- PDF, Unidad 1, p. 5-6.
- Guía 1, Ej 1.
