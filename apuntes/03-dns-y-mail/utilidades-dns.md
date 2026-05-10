# Utilidades DNS

Herramientas de línea de comandos para consultar y debuggear DNS.

## `host`

Lookup simple. Dado un nombre devuelve la IP (o viceversa con reverse DNS).

```
host www.itba.edu.ar
```

## `dig` (Domain Information Groper)

La más completa. Permite ver registros específicos de un dominio.

| Comando | Qué hace |
|---------|----------|
| `dig host MX +noall +answer` | muestra registros MX (servidores de correo del dominio) |
| `dig -t NS host +noall +answer` | consulta los NS (servidores de nombre autoritativos) |
| `dig host ANY +noall +answer` | todos los registros DNS del host (algunos servers no responden a `ANY`) |
| `dig host ns +short` | lista simplificada de NS |
| `dig -x 13.227.69.6` | consulta inversa (IP → nombre) |
| `dig host +trace` | recorrido completo desde los root servers |
| `dig .` | consulta a la raíz, lista de root servers |
| `dig . NS` | obtiene los nameservers de la raíz |
| `dig ar` | lo mismo pero para `.ar` |
| `dig ar NS` | servidores DNS responsables de `.ar` |
| `dig ar MX` | respuesta vacía (los TLDs no tienen MX) |

## `nslookup`

Alternativa interactiva para consultar registros DNS. Hoy `dig` es preferido.

## `whois`

Información sobre el responsable administrativo y técnico de un dominio (no es DNS, va por puerto 43 a registries específicos).

```
whois itba.edu.ar
```

## Aparece en parciales

- **Guía 3, ej. 7:** semántica de cada comando `dig` listado arriba.
