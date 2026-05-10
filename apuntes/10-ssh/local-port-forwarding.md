# Local Port Forwarding (y túneles SSH en general)

SSH permite armar **túneles TCP encriptados** entre cliente y servidor. Esto permite que dos aplicaciones (que no necesariamente saben de SSH) se comuniquen como si estuvieran conectadas directamente, pero el flujo de datos viaja encriptado por el canal SSH. Solo funciona sobre **TCP** — no sirve para protocolos basados en UDP (DNS, DHCP, NFS, NetBIOS).

## Idea general

Como SSH establece una conexión TCP autenticada y encriptada, podemos aprovecharla para "transportar" otra aplicación a través de ella. Por ejemplo, si el servidor SSH corre en el mismo host que un Postgres, podemos conectarnos al Postgres a través del túnel SSH y obtener encriptación gratis. Es mejor que tener que abrir muchos puertos.

En este caso SSH (que es un protocolo de aplicación) **transporta datos de otro protocolo de aplicación**.

## Túnel local (`-L`)

El **socket pasivo está en el cliente SSH**. El cliente abre un puerto local y todo lo que entra ahí se reenvía por el túnel hasta el servidor SSH, que lo entrega al destino real.

```
ssh -L 8080:webserver:80 SSHserver
```

- `-L8080:webserver:80` — túnel local.
- `8080` — puerto que abre el cliente local (equivale a `localhost:8080`).
- `webserver` — nombre o IP del destino, **resuelto desde el servidor SSH**.
- `80` — puerto del destino.
- `SSHserver` — el servidor SSH al que conectarse para armar el túnel.

Otro ejemplo (caso real con pampero):

```
ssh ipedemonteberthoud@pampero.itba.edu.ar -L 127.0.0.1:1234:localhost:1234
```

- Los **dos primeros parámetros** son del socket pasivo (en el cliente): `127.0.0.1:1234`.
- Los **dos siguientes** son del socket activo (lo que abre el servidor SSH para conectarse al destino): `localhost:1234`.
- `127.0.0.1` se puede omitir porque es el default.

### Flujo paso a paso

Asumiendo `ssh -L 2001:webserver:80 sshserver` y luego `curl localhost:2001/recurso`:

1. `curl` se conecta por TCP a `localhost:2001`.
2. Manda el request HTTP.
3. El cliente SSH local lee del puerto 2001, **encripta** los datos y los manda al servidor SSH.
4. El servidor SSH **desencripta** y reenvía al destino real (`10.1.0.10:80`).
5. La respuesta vuelve por el mismo túnel.

### ¿Quién hizo el request, según el web server?

Para el web server, el request **proviene del mismo host del servidor SSH** (es decir, de `localhost`/`127.0.0.1` si webserver y sshserver son el mismo host, o de la IP del sshserver dentro de la red interna). No ve al cliente original.

### Caso webserver = sshserver

Si el web server corre en el **mismo host** que el servidor SSH, no hay que redirigir a otro equipo: alcanza con que el SSH reenvíe al puerto 80 local.

## Gateway mode (`-g`)

Por defecto, el socket pasivo del túnel local escucha solo en `127.0.0.1` — únicamente quien armó el túnel puede usarlo. Con el flag `-g`, escucha en `0.0.0.0` y otros hosts de la red local pueden usarlo. Esto se conoce como **gateway**.

## Túnel remoto (`-R`)

El **socket pasivo está en el servidor SSH**. Es el caso inverso al local: la aplicación servidor está del lado del cliente SSH y queremos exponerla en el servidor.

```
ssh -R 9999:localhost:5432 user@pampero
```

Esto crea un socket pasivo en pampero:9999 que reenvía al cliente SSH:5432.

### Cuándo conviene usar túnel remoto

Cuando **uno está detrás de NAT o sin puertos abiertos** y necesita exponer un servicio. Como la PC es la que inicia la conexión SSH, no hace falta que sea alcanzable desde afuera. Ejemplo típico: tenés la base de datos en tu PC y querés que un compañero se conecte, pero tu PC tiene IP privada. Abrís un túnel remoto en un servidor común (pampero) y el compañero se conecta a pampero.

### Contra del túnel remoto

**No se puede regular quién accede** como con `-g` en local. Cualquier usuario en pampero puede conectarse al puerto 9999, tenga acceso a la BD o no.

## Túnel dinámico (`-D`)

Túnel local genérico que usa un **socket dinámico tipo SOCKS**. No hace falta fijar un destino: el cliente decide a qué host conectarse para cada request.

Casos de uso:
- Bypassear un proxy que filtra por IP origen (clásico: laboratorios del ITBA donde el proxy aceptaba pampero pero no la subred de los labs → los estudiantes armaban túnel dinámico por pampero).
- Anonimato frente a un proxy: para el proxy todos los pedidos parecen venir de pampero.

**Limitación:** no sirve para cualquier cosa porque depende de que la app cliente hable SOCKS.

## Resumen: local vs remoto vs dinámico

| Tipo | Flag | Socket pasivo en | Para qué sirve |
|------|------|------------------|----------------|
| Local | `-L` | Cliente SSH | Acceder desde mi PC a un servicio del lado del servidor SSH (o de su red interna). |
| Remoto | `-R` | Servidor SSH | Exponer un servicio de mi PC al servidor SSH (útil detrás de NAT). |
| Dinámico | `-D` | Cliente SSH (SOCKS) | Proxy SOCKS para múltiples destinos arbitrarios. |

## Aparece en parciales

- **Pregunta T29:** "En una red privada hay un host con un servidor SSH y otro con SMTP que solo acepta conexiones locales. ¿Si tengo acceso al SSH desde afuera, puedo acceder al SMTP?" → **Verdadero**, simplemente armando un túnel al servidor SSH (`ssh -L 25:smtpserver:25 sshserver`).
- **Pregunta T31:** Pide un ejemplo de túnel remoto. Respuesta canónica: tengo una BD en mi PC y un amigo no puede conectarse directamente (ej. estoy detrás de NAT). Abro `ssh -R 1234:localhost:1234 user@server.com`, lo que crea un socket pasivo en el servidor que escucha conexiones, y el activo del lado mío para responder.
- **Pregunta T39:** "Si puedo acceder con túnel remoto entonces también con túnel local." → **Falso**. Sirven para propósitos opuestos: el remoto funciona aunque esté detrás de NAT (porque la PC inicia la conexión); el local requiere que pueda alcanzar el servicio destino desde mi máquina (puede haber firewall/NAT/permisos que lo impidan).
- **Ejercicio práctico (2022 - 2C):** transferir el output de `tail -f /tmp/pdc` desde pampero hacia mi PC vía túnel local.
  ```
  # En mi PC:
  ssh -L 12345:localhost:12345 user@pampero.itba.edu.ar
  # En pampero (otra terminal):
  tail -f /tmp/pdc | nc -l localhost 12345
  # En mi PC (otra terminal):
  nc localhost 12345 > archivo.log
  ```

## Fuente

Apunte oficial Unidad 10, p. 125-129.
