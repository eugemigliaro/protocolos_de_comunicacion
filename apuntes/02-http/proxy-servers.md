# Proxy Servers

Un **proxy** es un intermediario entre el cliente y el web server. Cuando el cliente pide un recurso, lo pide al proxy, que actúa como cliente del server real y como servidor del cliente original.

```
Cliente  ↔  Proxy  ↔  Web server
```

## Tipos según visibilidad

- **Explícito:** el cliente está configurado para usar el proxy (ej. setting de browser, `curl -x`).
- **Transparente:** el cliente no sabe que existe; típicamente el router redirige el tráfico HTTP al proxy sin que el cliente lo configure.

## Funciones

1. **Filtro:** todo pasa por él, así que puede bloquear sitios o controlar acceso (típico en redes corporativas).
2. **Caché:** guarda recursos recientes para responder más rápido y bajar tráfico al server real.

## Caché en el proxy

El servidor que genera el recurso decide si tiene sentido cachearlo (por ejemplo, recursos muy pequeños no conviene; recursos dinámicos personalizados tampoco).

Si el recurso **no está cacheado**, el proxy lo busca en el server. Si el cliente vuelve a pedir un recurso **no cacheable**, el proxy lo vuelve a buscar.

### `max-age`

El servidor le dice al proxy (vía `Cache-Control: max-age=N`) por cuántos segundos puede considerar válida la copia. Mientras dure ese tiempo:
- El browser que vuelve a pedirlo lo tiene local, no va al proxy.
- Otro browser que pida el mismo recurso lo recibe del proxy sin ir al server.

### `Last-Modified` + `If-Modified-Since`

Para recursos pesados, el servidor manda `Last-Modified: <fecha>`. Cuando el proxy quiere revalidar, manda un request con `If-Modified-Since: <esa fecha>`:
- `304 Not Modified` → el proxy reusa la copia local, sin re-descargar.
- `200 OK` con el recurso → hubo cambio, lo trae nuevo.

Combinando `max-age` con `Last-Modified` se hace eficiente el funcionamiento del proxy.

### `public` vs `private`

- `Cache-Control: public` (default): el recurso puede ser cacheado por proxies intermedios.
- `Cache-Control: private`: solo el cliente final puede cachearlo. Los proxies no deben guardarlo, porque el recurso es específico de ese usuario y no le sirve a otros.

> Referencia oficial: Unidad 2, p. 17-19 (`material/apuntes-pdf/Protos - Apuntes.pdf`).

## Proxy reverso

Concepto opuesto al proxy "directo": el proxy reverso vive del lado del servidor. El cliente cree que habla con el server final, pero en realidad habla con el proxy reverso, que internamente despacha a uno o varios backends. Caso típico: nginx delante de tomcat.

**Problema:** el backend ve siempre `127.0.0.1` como IP del cliente, porque para él el cliente es el propio nginx.

**Solución:** nginx agrega el header `X-Forwarded-For: <IP real>`. Ver `headers-http.md`.

## Aparece en parciales

- **¿Cómo sabe el proxy si la copia en caché todavía es válida?** Por `max-age` (cuánto tiempo es válida) o por `Last-Modified` revalidando contra el servidor. (Guía 2, Ej 1)
- **"La única forma de obtener un recurso sin acceder al server HTTP es porque el proxy lo tiene cacheado."** Falso: el browser también puede tenerlo en su propia caché. (Guía 2, Ej 3 y Preguntas Teóricas, Ej 46)
- **Si el recurso se cachea en mi browser, ¿también lo hace el proxy?** No necesariamente: si `Cache-Control: private`, los proxies no deben guardarlo. (Guía 2, Ej 7)
- **TRACE filtra headers que el proxy agregó para autenticarse.** No habilitar TRACE en servers que confían en headers custom del proxy. (Guía 2, Ej 4)
- **`X-Forwarded-For` para informar la IP real al backend detrás de nginx.** (Preguntas Teóricas, Ej 15)
- **Saltarse el filtro del proxy:** un alumno cuya empresa solo permite HTTP vía proxy y filtra ciertos sitios puede usar un **túnel SSH dinámico** para que el tráfico salga desde otro servidor. (Preguntas Teóricas, Ej 10)
- **Práctico 2024-2C:** `curl -x socks5h://proxy.sebikul.com:1080 -H "Accept: text/plain" -H "Accept-Language: es" http://...` — la opción `-x` (alias `--proxy`) usa el proxy indicado.
