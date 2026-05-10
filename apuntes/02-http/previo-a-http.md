# Previo a HTTP

Antes de HTTP, los documentos se obtenían vía FTP: cada referencia obligaba a abrir un nuevo sitio FTP y bajar el archivo. La necesidad de aplicaciones basadas en **hipertexto** (links a otros recursos sin saltar de sistema) llevó al diseño de HTTP.

## Evolución

| Año | Versión | Características |
|-----|---------|-----------------|
| 1991 | HTTP/0.9 | Protocolo de texto, request-response, contenido estático, sin estado, orientado a línea de comando. |
| 1996 | HTTP/1.0 | Browser-friendly (no solo hipertextos). Códigos de estado. Métodos `GET`, `POST`, `HEAD`. |
| 1999 | HTTP/1.1 | Conexiones persistentes, headers en peticiones, métodos `PUT`, `DELETE`, `TRACE`, `OPTIONS`. URI. Negociación de contenido y headers extendidos. |
| — | HTTP/2 | Protocolo **binario**, compresión de headers, multiplexación de recursos, server push. |

**Conexión persistente (1.1):** un HTML con 6 imágenes en HTTP/1.0 obliga a abrir 6 conexiones (una por recurso). 1.1 mantiene la conexión abierta para reutilizarla.

**Server push (2):** mientras el server parsea el HTML, ya empieza a mandar los recursos referenciados sin esperar el pedido del cliente.

> Referencia oficial: Unidad 2, p. 10-11 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## URI, URL, URN

Un **recurso** es un bloque de información identificado por su URI (Uniform Resource Identifier). Puede ser un archivo físico o algo generado por un programa. Forma general:

```
<scheme>://<authority><path>?<query>
```

- El `path` termina con el primer `?`, `#` o el fin de la cadena. Puede ser absoluto o relativo.
- Si el recurso es una aplicación, puede recibir parámetros en `<query>`.

**URL** (Uniform Resource Locator): identifica el recurso por su **ubicación**. Puede incluir un fragmento al final:

```
http://www.unsitio.com/intro.html#chapter1
```

**URN** (Uniform Resource Name): identifica un recurso por su **nombre**. No implica que el recurso exista ni cómo acceder a él.

Las URIs se usan para acceder a recursos por URL o por URN (por ejemplo, dentro de HTML).

## Aparece en parciales

- **HTTP es un protocolo de texto.** Falso: depende la versión. HTTP/2 es binario, las anteriores son de texto. (Preguntas Teóricas, Ej 5.1)
