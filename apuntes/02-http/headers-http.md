# Headers HTTP

Los headers son metadatos del mensaje, con la forma:

```
<nombre>: <valor asociado>
```

Finalizan con una línea en blanco (que separa headers del body). Hay cuatro categorías y suelen enviarse en este orden:

1. Generales
2. De solicitud
3. De respuesta
4. De contenido

## Headers generales

| Header | Significado |
|--------|-------------|
| `Cache-Control` | Directivas para el cache (`no-cache`, `max-age=...`, `public`, `private`). |
| `Connection` | Opciones de conexión (`close`, `keep-alive`). |
| `Date` | Fecha de creación del mensaje. |
| `Transfer-Encoding` | Encoding de transferencia (ej. `chunked`). |
| `Via` | Lista de intermediarios por los que pasó el mensaje. |

## Headers de solicitud

| Header | Significado |
|--------|-------------|
| `Accept` | Tipo de contenido aceptado por el cliente (ej. `text/html`). |
| `Accept-Charset` | Charset aceptado (`ISO-8859-1`, `UTF-8`...). |
| `Accept-Encoding` | Encoding aceptado (`gzip`, `compress`). |
| `Accept-Language` | Idioma aceptado. |
| `Expect` | Comportamiento esperado del server frente al request. |
| `From` | Email del usuario que generó el request. |
| `Host` | Servidor y puerto destino. **Obligatorio en 1.1** por multihoming. |
| `If-Modified-Since` | Condiciona el request a una fecha (caché). |
| `Referer` | URL del documento que generó el request. |
| `User-Agent` | Aplicación que generó el request (browser, curl, etc.). |
| `Upgrade` | Solicita usar otro protocolo. |
| `Range` | Solicita un rango (en bytes) del recurso. |

## Headers de respuesta

| Header | Significado |
|--------|-------------|
| `Age` | Estimación en segundos del tiempo desde que se generó la respuesta en el server. |
| `Connection` | `close`, `keep-alive`. |
| `Location` | URI a redireccionar. |
| `Retry-After` | Tiempo de delay para reintentar. |
| `Server` | Descripción del software del server. |
| `Authorization` | Indica que el recurso necesita autorización. |

## Headers de contenido

| Header | Significado |
|--------|-------------|
| `Allow` | Métodos aplicables al recurso (respuesta a `OPTIONS`). |
| `Content-Encoding` | Encoding del body. |
| `Content-Length` | Tamaño del body en bytes. |
| `Content-Location` | Ubicación alternativa del recurso. |
| `Content-MD5` | Hash MD5 del body (integridad). |
| `Content-Type` | Tipo MIME del body. |
| `Expires` | Fecha de expiración del recurso. |
| `Last-Modified` | Fecha de última modificación. |

## Headers extendidos (custom)

HTTP/1.1 permite definir headers propios. Convención común: prefijo `X-`.

Ejemplos típicos:
- `X-Forwarded-For`: lo agrega un proxy reverso (ej. nginx) para informar al backend la IP real del cliente, ya que sin él el backend ve siempre `127.0.0.1`.
- `X-PHRASE`: header secreto entre proxy y backend para autenticar al proxy (cuidado con TRACE, ver `mensajes-http.md`).

> Referencia oficial: Unidad 2, p. 15-16 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## Aparece en parciales

- **`X-Forwarded-For` con nginx como proxy reverso:** si la app web "levanta" la IP del cliente y nginx hace de proxy reverso a tomcat (o equivalente), todas las IPs aparecen como `127.0.0.1`. La solución es que nginx incluya `X-Forwarded-For` con la IP real del cliente. (Preguntas Teóricas, Ej 15)
- **Custom header en parcial práctico (2024-2C):** ejecutar `curl -i -H "X-Parcial-Protos: pincha" http://foo.example.org` para enviar un header propio. (Ejercicios Prácticos)
