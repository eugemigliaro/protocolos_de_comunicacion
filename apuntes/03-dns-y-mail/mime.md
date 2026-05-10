# MIME

**MIME (Multipurpose Internet Mail Extensions)** es una capa por encima de SMTP que permite enviar contenido no-ASCII (imágenes, audio, video, HTML, texto internacional) sin romper el protocolo, que solo acepta US-ASCII.

## Idea

SMTP solo entiende US-ASCII de 7 bits. Si quiero mandar una imagen, audio o un mail con `ñ`, no puedo hacerlo directamente. MIME mete una **capa intermedia que estructura el cuerpo del mensaje** de forma transparente para SMTP:

```
[ SMTP ]   ← solo ASCII-7
[ MIME ]   ← agrega estructura al cuerpo
[ Cuerpo: texto, imagen, audio, etc. codificado en ASCII (ej. base64) ]
```

MIME respeta el RFC 822 y agrega estructura al cuerpo del mensaje. Es **transparente para SMTP**.

## Para qué sirve

- Normalizar el intercambio de **distintos tipos de contenido**: texto plano, texto formateado, imágenes, sonido, video, HTML.
- Resolver el problema de enviar **texto internacional** (caracteres no-ASCII) por mail.

## Encabezados MIME

| Header | Qué hace |
|--------|----------|
| `MIME-Version: 1.0` | declara que el mensaje usa MIME |
| `Content-Type` | tipo y subtipo del contenido (ver más abajo) |
| `Content-Transfer-Encoding` | cómo está codificado el contenido (`7bit`, `8bit`, `binary`, `base64`, `quoted-printable`) |
| `Content-Description` | descripción legible (opcional) |
| `Content-ID` | identificador único del cuerpo (opcional) |

## Content-Type

Dos componentes: `tipo/subtipo`. Tipos principales:

| Tipo | Subtipos típicos |
|------|------------------|
| `text` | `plain`, `html`, `richtext` |
| `image` | `jpeg`, `gif`, `png` |
| `audio` | `basic`, `mpeg` |
| `video` | `mpeg`, `quicktime` |
| `application` | `octet-stream`, `pdf`, `zip` |
| `multipart` | `mixed`, `alternative`, `parallel`, `digest` |
| `message` | `rfc822`, `partial`, `external-body` |

`multipart` permite incluir **varias partes** en un mismo mensaje, separadas por un `boundary` definido en el header.

## Content-Transfer-Encoding

| Valor | Uso |
|-------|-----|
| `7bit` | el contenido ya es ASCII-7, sin codificar |
| `8bit` | bytes de 8 bits (no estándar en SMTP clásico) |
| `binary` | binario crudo |
| `base64` | binario codificado en base64 (ver `formato-de-un-mensaje.md`) |
| `quoted-printable` | mayoría ASCII, escapa solo lo no imprimible (ej. `=E1` para `á`) |

## Ejemplo: PDF adjunto en base64

```
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="boundary123"

--boundary123
Content-Type: text/plain; charset="UTF-8"

Mira el adjunto.

--boundary123
Content-Type: application/pdf; name="Adjunto_1_OP.pdf"
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="Adjunto_1_OP.pdf"

JVBERi0xLjQKJeLjz9MK...
(contenido del PDF en base64)

--boundary123--
```

## Multipart/alternative

Permite mandar la **misma información en varios formatos** y que el cliente elija cuál renderizar. Típico: una versión `text/plain` y una `text/html` del mismo mensaje.

```
Content-Type: multipart/alternative; boundary="abc"

--abc
Content-Type: text/plain
...
--abc
Content-Type: text/html
...
--abc--
```

## Aparece en parciales

- **Ejercicios prácticos 2024-2C:** mandar un mail por SMTP a mano con cuerpo MIME `multipart/alternative`, partes en `text/plain` y `text/html`, ambas con `Content-Transfer-Encoding: base64` y contenido codificado.
