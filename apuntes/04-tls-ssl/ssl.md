# SSL (Secure Sockets Layer)

Protocolo desarrollado por Netscape en 1994 que se ubica como capa intermedia entre los protocolos de aplicación (ej. HTTP) y transporte (TCP) para encriptar la comunicación. Permite que servidor y cliente se autentiquen mutuamente y cifren la conversación.

## Motivación

HTTP y TCP por sí solos no ofrecen ningún tipo de seguridad. La idea es agregar una capa intermedia que encripte y desencripte sin que cambien las capas de arriba ni de abajo. Cuando vemos el candado en el browser (típico al entrar a un banco) es porque la conexión usa SSL.

## Qué provee

- Autenticación del servidor frente al cliente (y opcionalmente del cliente).
- Confidencialidad: la conversación viaja cifrada.
- Integridad: se detecta si los datos fueron modificados en tránsito.

Para lograr esto SSL usa criptografía asimétrica (para autenticarse y compartir una clave) y luego pasa a criptografía simétrica (para encriptar el tráfico real, que es más eficiente). Ver `encriptacion.md`.

## Pasos generales del handshake SSL

1. El cliente abre la conexión y propone parámetros (versión, algoritmos soportados).
2. El servidor responde con sus parámetros y envía su certificado (con su clave pública).
3. El cliente valida el certificado contra los CAs que conoce. Si la firma no es reconocida, el browser muestra advertencia.
4. Cliente y servidor acuerdan una clave de sesión (compartida) usando criptografía asimétrica.
5. A partir de ahí toda la comunicación se cifra con esa clave compartida.

## Tipos de SSL Handshake

- **Full Handshake:** ocurre cuando cliente y servidor no tienen una sesión previa. Se hace todo el proceso desde cero.
- **Session Resumption:** se reutiliza una sesión anterior para acelerar el proceso. Ejemplo: mirar Flow fuera del ITBA y, al reconectarse a otra red, el contenido sigue disponible sin renegociar todo. El proxy puede ver el contenido en claro porque es él quien terminó la conexión SSL del cliente y luego abre otra como cliente hacia el servidor real.

## SSL vs. TLS

TLS es la evolución de SSL (TLS 1.0 está basado en SSL 3.0, a veces llamado "SSL 3.1"). El nivel de encriptación es equivalente; la diferencia principal está en cómo se inicia la conexión segura (ver `tls.md`).

## Aparece en parciales

- "TLS se diferencia de SSL principalmente en que la conexión se establece en el puerto estándar y sin seguridad. Una vez establecida se puede negociar el uso del TLS y se comienza a usar sobre la misma conexión, no sobre otro puerto especial como requiere SSL." → **Verdadero** (preguntas-teoricas, ej. 25).

## Fuente

- Apunte oficial, Unidad 4, p. 45-47.
