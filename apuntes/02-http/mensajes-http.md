# Mensajes HTTP

Cliente y servidor intercambian dos tipos de mensajes:
- **Request** (cliente → servidor)
- **Response** (servidor → cliente)

Ambos comparten estructura: una **start line**, headers, una línea en blanco y opcionalmente un body.

## Estructura del request

```
GET /index.html HTTP/1.1
Host: clarin.com
User-Agent: Mozilla/5.0
Accept: text/html

<body opcional>
```

La línea en blanco separa headers del body — solución mínima para indicar dónde arranca el contenido.

> Aunque la conexión sea con un único host, el header `Host` es obligatorio porque un mismo servidor físico puede alojar muchos sitios (multihoming, virtual hosting).

## Estructura de la response

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<html>...
```

La start line de la response tiene **versión, código de respuesta y mensaje**.

## Métodos (verbs)

| Método | Qué hace |
|--------|----------|
| `GET` | Solicita un recurso al servidor. |
| `HEAD` | Solicita solo los headers del recurso (sin body). |
| `POST` | Envía información al servidor para ser procesada. |
| `PUT` | Crea o reemplaza un recurso (1.1). |
| `DELETE` | Elimina un recurso (1.1). |
| `OPTIONS` | Consulta los métodos permitidos y capacidades del servidor para un recurso, sin modificarlo. La respuesta suele incluir el header `Allow` con la lista de métodos disponibles. |
| `TRACE` | Diagnóstico: el servidor devuelve en el body el contenido exacto del request que recibió. Permite ver qué modificaron los proxies en el camino. |

> Referencia oficial: Unidad 2, p. 12-14 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## Visibilidad del tráfico

Como HTTP/0.9–1.1 viaja en texto plano, cualquiera que intercepte el segmento puede leer el contenido completo (URL, headers, body). Esto motiva la existencia de HTTPS.

## Aparece en parciales

- **TRACE y custom headers de proxy:** si un servidor confía en un header secreto agregado por el proxy (ej. `X-PHRASE: I AM THE PROXY`), **no debe habilitar TRACE**. Como TRACE devuelve al cliente el request *tal como lo recibió el servidor* (incluyendo el header agregado por el proxy), el atacante se enteraría de la frase secreta. (Guía 2, Ej 4)
