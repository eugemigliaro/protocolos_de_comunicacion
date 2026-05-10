# DNS Spoofing

Ataque que consiste en lograr que un registro DNS apunte a una IP que no le corresponde. El atacante altera la resolución de nombres para redirigir usuarios a sitios falsos sin su conocimiento.

## Mecánica del ataque

1. El atacante introduce datos falsificados en la **caché de un servidor DNS**.
2. Cuando un usuario pide un dominio legítimo, el servidor responde con la IP del atacante.
3. El usuario llega a un sitio controlado por el atacante creyendo que es el real.

## Por qué funciona

DNS sobre UDP es no confiable y sin autenticación. La defensa básica del protocolo es que los mensajes de pregunta y respuesta llevan un mismo **ID** que los identifica — el resolver verifica que la respuesta corresponda a una pregunta abierta. Si el atacante adivina/observa el ID antes que llegue la respuesta legítima, la suya gana.

## DNS Sniffer

Herramienta que captura, analiza e inspecciona el tráfico DNS de una red. Permite ver consultas y respuestas, y es uno de los preludios típicos a un ataque de spoofing (para conocer IDs, tiempos, etc.).

## DNS Cache Poisoning

Variante específica donde el atacante introduce **datos falsos en la caché de un servidor DNS resolver**. Una vez "envenenada", la caché sirve la IP falsa a todos los clientes que consulten ese dominio durante el TTL del registro envenenado. Es más dañino que el spoofing puntual porque afecta a muchos clientes desde un único ataque exitoso.

## Defensas

- **DNSSEC** — firma criptográfica de respuestas DNS. Si las respuestas no están firmadas correctamente, el resolver las descarta.
- **Aleatorización del puerto fuente** y del ID de transacción — dificulta que el atacante adivine ambos a tiempo.
- **DNS sobre HTTPS / TLS** — encripta el canal y autentica al servidor, evitando MITM (también respuesta a la pregunta de "por qué usar HTTPS para resolver nombres en vez de DNS sobre UDP").

## Aparece en parciales

- **Preguntas teóricas, ej. 47:** "¿Por qué resolver nombres con HTTP en vez de DNS sobre UDP?" → seguridad: UDP es vulnerable a MITM/spoofing, HTTPS no.
