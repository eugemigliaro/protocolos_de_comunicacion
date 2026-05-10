# Cookies

Una cookie es un pequeño texto que el servidor envía al cliente y el browser almacena. Sirven para mantener **estado** entre cliente y servidor sobre un protocolo (HTTP) que no es orientado a conexión.

## Flujo

```
Cliente                              Servidor
  | --- GET /login ----------- >       |
  | <-- 200 + Set-Cookie: id=abc       |
  | --- GET /perfil ---------- >       |
  |     Cookie: id=abc                 |
  | <-- 200 OK ---------------         |
```

- Servidor → cliente: header `Set-Cookie` en la **response**.
- Cliente → servidor: header `Cookie` en cada **request** posterior al mismo dominio.

## Persistencia

| Tipo | Vida útil |
|------|-----------|
| **Cookies de sesión** (session cookies) | Temporales, se eliminan al cerrar el browser. No tienen fecha de expiración. |
| **Cookies persistentes** (persistent cookies) | Tienen fecha de expiración explícita (horas, días, meses, años). Sobreviven al cierre del browser. Se usan para recordar preferencias o mantener al usuario autenticado entre sesiones. |

## Otras categorías

- **Third-party cookie:** creada por un dominio **distinto** al sitio que el usuario está visitando. Típico de tracking publicitario (un mismo proveedor de ads embebido en muchos sitios sigue al usuario por todos ellos).

  > **Aclaración importante:** las cookies **siempre** quedan asociadas al servidor que las dejó. "Third-party" no significa que un servidor deja una cookie para que la lea otro servidor — eso no existe. Significa que la cookie es del servidor de un recurso embebido (ej. un script o imagen externa), no del sitio principal que el usuario abrió. (Guía 2, Ej 5.3)

- **Secure cookie:** solo se transmite por HTTPS, no por HTTP plano. Protege la cookie de ser interceptada en tránsito.

> Referencia oficial: Unidad 2, p. 19-20 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## Aparece en parciales

- **TCP/IP no tiene capa de sesión y HTTP no es orientado a conexión, ¿cómo se mantiene una sesión web?** Mediante **cookies**, que son texto que el servidor manda al cliente para identificarlo en requests posteriores. (Guía 2, Ej 2 y Preguntas Teóricas, Ej 42)
- **"Third-party cookie" es una cookie que un server HTTP deja para enviar a otro server HTTP.** Falso: las cookies siempre están asociadas al server que las dejó. (Guía 2, Ej 5.3)
