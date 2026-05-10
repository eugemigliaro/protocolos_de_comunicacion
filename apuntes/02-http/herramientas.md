# Herramientas

## curl

Cliente de línea de comandos para transferir recursos por URL sobre HTTP, HTTPS, FTP, etc. Útil para probar APIs, ver el detalle de la conexión, descargar archivos.

### Casos correctos

```bash
curl http://google.com/humans.txt
curl https://www.google.com/humans.txt -i        # incluye headers de respuesta
curl -X POST -F 'locale=en' url                  # POST con form-data
curl --head https://www.google.com               # solo headers (HEAD)
```

### Casos incorrectos (típicos errores)

```bash
curl https:asite.com           # falta // después del scheme
curl https:asite.com --head    # mismo error
```

### Flags útiles para parciales

| Flag | Para qué sirve |
|------|----------------|
| `-i` | Incluye headers de respuesta en el output. |
| `-I` o `--head` | Solo HEAD (pide headers sin body). |
| `-H "Header: valor"` | Agrega un header propio. |
| `-X METHOD` | Cambia el método (GET, POST, PUT, DELETE...). |
| `-F 'campo=valor'` | Envía form-data (multipart). |
| `-d 'body'` | Envía un body (POST por default). |
| `-x [proto://]host:puerto` (alias `--proxy`) | Hace la solicitud a través de un proxy. |
| `-L` | Sigue redirects automáticamente. |
| `-v` | Verbose: muestra todo el handshake y headers. |

### Ejemplos de parciales

```bash
# Custom header (Práctico 2024-2C, Ej 1)
curl -i -H "X-Parcial-Protos: pincha" http://foo.example.org

# Vía proxy SOCKS5 con resolución remota (Práctico 2024-2C, Ej 2)
curl -x socks5h://proxy.sebikul.com:1080 \
     -H "Accept: text/plain" \
     -H "Accept-Language: es" \
     http://ejercicio2.sebikul.com:8080/foo/
```

## wget

Descarga archivos por HTTP, HTTPS y FTP. Soporta descargas en background y reanudación de archivos interrumpidos. Bueno para descargas masivas o automáticas.

### Casos típicos

```bash
wget https://wordpress.org/latest.zip            # descarga simple
wget -i files.txt                                # descarga las URLs listadas en files.txt
wget --limit-rate=500k <url>                     # limita el ancho de banda
wget -r -l1 -H -nd -A pdf https://ahira.com.ar/revistas/patoruzu
#  -r recursivo, -l1 profundidad 1, -H entre hosts, -nd sin dirs, -A filtro de extensión
```

## netcat (`nc`)

"La navaja suiza" de las redes: lee y escribe datos por conexiones TCP o UDP. Sirve para depuración, transferencia de archivos, escaneo de puertos, conexiones manuales a servidores.

### Casos típicos

```bash
netcat www.google.com 80          # cliente: abrir TCP a Google puerto 80
netcat -l -p 8080 -v              # servidor: escuchar en 8080, verbose
nc -C mail.sebikul.com smtp       # conexión a SMTP usando \r\n (-C)
```

### Flags útiles

| Flag | Para qué sirve |
|------|----------------|
| `-l` | Modo listen (servidor). |
| `-p <puerto>` | Puerto local. |
| `-v` | Verbose. |
| `-u` | UDP en lugar de TCP. |
| `-C` | Termina líneas con `\r\n` (necesario para SMTP, HTTP). |

### Ejemplo de parcial: SMTP a mano

```bash
nc -C mail.sebikul.com smtp
HELO ipedemonteberthoud
MAIL FROM: <ipedemonteberthoud@itba.edu.ar>
RCPT TO: <64908@mail.sebikul.com>
DATA
...
.
QUIT
```

(Práctico 2024-2C — más detalle en la unidad 3.)

> Referencia oficial: Unidad 2, p. 20-21 (`material/apuntes-pdf/Protos - Apuntes.pdf`).
