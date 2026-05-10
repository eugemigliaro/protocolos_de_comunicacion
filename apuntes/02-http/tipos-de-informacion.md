# Tipos de Información

HTTP transfiere texto, pero también imágenes, video, PDFs, etc. La forma de describir el tipo de contenido es **MIME** (Multipurpose Internet Mail Extensions). El web server etiqueta cada objeto con un tipo MIME, por ejemplo:

- `text/html`
- `text/plain`
- `video/mp4`
- `image/jpeg`
- `application/pdf`

> MIME pertenece a la **capa de presentación** del modelo OSI (Preguntas Teóricas, Ej 5.2).

## GET vs POST

| Aspecto | GET | POST |
|---------|-----|------|
| Cacheable | Sí (f5 reusa caché) | No |
| Historial del browser | Se guarda | No |
| Bookmarkeable | Sí | No |
| Longitud | Acotada (los parámetros van en la URL, que tiene un máximo) | Sin límite práctico |
| Propósito | Solo pedir datos (idempotente) | Enviar datos para procesar |

## OPTIONS

Consulta los métodos permitidos para un recurso sin modificarlo. La respuesta incluye el header `Allow` con la lista, por ejemplo:

```
HTTP/1.1 200 OK
Allow: GET, POST, HEAD, OPTIONS
```

## TRACE

Realiza una prueba de diagnóstico: el servidor devuelve en el body el request exacto que recibió. Esto permite ver el recorrido de la solicitud y cómo la modificaron proxies y otros intermediarios.

> Detalle de seguridad: TRACE puede filtrar headers que un proxy agrega para autenticarse contra el servidor. Ver `mensajes-http.md`.

> Referencia oficial: Unidad 2, p. 13-14 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## Aparece en parciales

- **¿Por qué algunos recursos del mismo server se cachean y otros no, si todos tienen el mismo `Cache-Control`?** Porque algunos se obtuvieron con `GET` (cacheables) y otros con `POST` (no cacheables). (Guía 2, Ej 6 y Preguntas Teóricas, Ej 44)
- **MIME es de la capa de presentación.** Verdadero. (Preguntas Teóricas, Ej 5.2)
