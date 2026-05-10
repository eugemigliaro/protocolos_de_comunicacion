# Conexiones HTTP

## Persistente vs no persistente

**No persistente (HTTP/1.0):** una conexión TCP por recurso. Para una página con N recursos se abren y cierran N conexiones — costoso por el handshake repetido.

```
Cliente                Servidor
  | --- SYN -------->    |
  | <-- SYN/ACK -----    |
  | --- ACK -------->    |
  | --- GET /a.html ->   |
  | <-- response ----    |
  | --- FIN -------->    |   ← cierre
  | <-- FIN/ACK -----    |
  | --- SYN -------->    |   ← otra conexión para /b.png
  ...
```

**Persistente (HTTP/1.1, default):** la misma conexión TCP se reutiliza para múltiples recursos. Se controla con el header `Connection`:

- `Connection: keep-alive` — mantener abierta (default en 1.1).
- `Connection: close` — cerrar después de la respuesta.

```
Cliente                Servidor
  | --- SYN -------->    |
  | <-- SYN/ACK -----    |
  | --- ACK -------->    |
  | --- GET /a.html ->   |
  | <-- response ----    |
  | --- GET /b.png  ->   |   ← misma conexión
  | <-- response ----    |
  | --- GET /c.css  ->   |
  | <-- response ----    |
  | --- FIN -------->    |
```

> Referencia oficial: Unidad 2, p. 16-17 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## Negociación de contenido

El cliente indica preferencias (idioma, encoding, tipo) con headers `Accept-*` y el servidor decide cómo responder. Hay dos posibilidades:

**1. El servidor entrega el recurso directamente.** Ej.: Alice pide un recurso en `Accept-Language: es, en`. El servidor lo tiene en inglés y se lo manda sin redireccionar.

```
Cliente                       Servidor
  | --- GET / -------- >       |
  |     Accept-Language: es,en |
  | <-- 200 OK (en) ----       |
```

**2. Redirección (código 3xx).** El servidor no tiene el recurso y le dice al cliente que vaya a otra URL. Es transparente para el usuario: el browser hace la nueva solicitud automáticamente.

```
Cliente                       Servidor
  | --- GET / -------- >       |
  | <-- 301 Location: ... <    |
  | --- GET /es/ ----- >       |
  | <-- 200 OK (es) ----       |
```

## Aparece en parciales

(No hay preguntas específicas de conexiones HTTP en los parciales relevados, pero se relaciona con cookies como mecanismo de "sesión" sobre un protocolo sin conexión — ver `cookies.md`.)
