# Formato de un Mensaje

El formato de un mail está definido por el **RFC 822** (con extensiones posteriores en RFC 2822 y RFC 5322).

## Estructura

Un mensaje consta de:

1. **Envoltura primitiva** (RFC 821, definida por SMTP — `MAIL FROM`, `RCPT TO`, etc.).
2. **Campos de cabecera** (`From:`, `To:`, `Subject:`, `Date:`, etc.). Los usuarios pueden definir cabeceras propias prefijándolas con `X-`.
3. **Una línea en blanco** que separa cabeceras del cuerpo.
4. **Cuerpo del mensaje**.

Ejemplo:

```
From: Juan <juan@ejemplo.com>
To: Ana <ana@otro.com>
Subject: Hola
Date: Wed, 4 Jun 2025 14:24:17 -0300
X-Mi-Header: lo-que-quiera

Hola Ana, ¿cómo va?
```

## Limitaciones de SMTP + RFC 822

Aunque el e-mail es probablemente la aplicación TCP/IP más usada, SMTP y RFC 822 limitan a:

- **ASCII de 7 bits**.
- **100 caracteres máximo por línea**.

Esto impide enviar:
- Caracteres con acentos, `ñ`, alfabetos no latinos.
- Imágenes, audio, video, documentos binarios.

Para resolver esto se usa **Base64** como codificación, y **MIME** como capa de estructura sobre el cuerpo del mensaje (ver `mime.md`).

## Base64

Codificación simple para representar datos binarios como ASCII de 7 bits.

### Cómo funciona

- Cada grupo de **3 bytes (24 bits)** se codifica como **4 caracteres** de 6 bits cada uno.
- 6 bits → rango `[0, 63]`. Cada número se mapea a un carácter del alfabeto Base64.

Alfabeto Base64:

```
0–25:  A–Z
26–51: a–z
52–61: 0–9
62:    +
63:    /
```

### Padding

Si la cantidad de bits no es múltiplo de 6:
- Se agregan ceros al final hasta llegar a múltiplo de 6.
- Se agregan **uno o dos caracteres `=`** de relleno al final del bloque codificado para indicar cuántos bits eran de padding.
- En general se agrega un solo `=`.

### Ejemplo

`Hola` (4 bytes, 32 bits) → no es múltiplo de 3 bytes, falta 1 byte → padding de un `=`.
```
Hola (texto)
0x48 0x6F 0x6C 0x61
01001000 01101111 01101100 01100001
agrupando de a 6 bits y agregando ceros:
010010 000110 111101 101100 011000 010000
   18      6     61     44     24     16
    S      G      9      s      Y      Q
→ "SG9sYQ=="
```

## Aparece en parciales

- **Ejercicios prácticos 2024-2C:** ejercicio de SMTP donde hay que conectarse a `mail.sebikul.com` con `nc -C ... smtp` y mandar el mail manualmente. Las partes con caracteres no-ASCII (ej. `¡Hola mundo!`) se codifican en Base64 antes de poner en `DATA`. Headers MIME (`Content-Transfer-Encoding: base64`) indican al receptor cómo decodificar.
