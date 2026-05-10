# DNS over TLS (DoT)

Forma de usar DNS de manera segura aprovechando la encriptación de TLS. El cliente DNS encapsula sus consultas dentro de una conexión TLS, evitando que un tercero las espíe o modifique en el camino.

## Por qué hace falta

DNS tradicional viaja en claro sobre UDP (puerto 53). Esto permite:

- Que un atacante vea qué dominios resuelve el cliente.
- Hacer DNS spoofing inyectando respuestas falsas (ver `apuntes/03-dns-y-mail/dns-spoofing.md`).

Como TLS exige TCP, no se puede aplicar directamente sobre el DNS clásico (UDP). DoT corre sobre **TCP + TLS**.

## Características

- **Puerto:** 853 por defecto.
- Soporte **nativo** en algunos Linux modernos (systemd-resolved, etc.).
- El cliente DNS abre una conexión TLS al resolver y mete las queries DNS dentro de esa conexión.

## Modos

- **Modo estricto:** toda la parte recursiva del DNS es siempre segura. Si el resolver tiene que ir a buscar la respuesta a otro servidor, esa otra conexión también debe ser segura. Si no se puede establecer una conexión segura, la consulta falla.
- **Modo de privacidad oportunista:** intenta usar DoT; si no se puede, cae a DNS sin cifrar (mejor que nada, pero no garantiza confidencialidad).

## Variante: DNS over HTTPS (DoH)

Otra forma de proteger DNS: el cliente usa **GET o POST** para enviar la query DNS codificada dentro de una conexión HTTPS normal (puerto 443). Tiene la ventaja de mezclarse con el tráfico HTTPS general y ser más difícil de bloquear.

## Resultado

Sirve para tener tranquilidad de que **todos los nombres que resolvemos son seguros** (no observables ni modificables por un tercero en la red).

## Fuente

- Apunte oficial, Unidad 4, p. 48-49.
