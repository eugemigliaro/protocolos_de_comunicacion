# Resolución de Nombres

Proceso de traducir un nombre de dominio (`www.ejemplo.com`) a una dirección IP (`192.0.2.1`). Permite usar nombres legibles en lugar de IPs numéricas.

## Conceptos

- **Dominio:** colección de computadoras accesibles bajo un nombre común.
- **Nombre de dominio:** referencia colectiva a múltiples hosts (`itba.edu.ar`, `ibm.com`).
- **Niveles:** los dominios son jerárquicos. El más alto es el TLD (top-level domain) — `.com`, `.org`, `.ar`, etc. Ver RFC 1591 y RFC 3071.
- **FQDN (Fully Qualified Domain Name):** `hostname + dominio + TLD`. Ej: `www.itba.edu.ar`.

## Cómo resuelve un host un FQDN a IP

Para obtener un número IP a partir de un FQDN hay dos posibilidades, en este orden por defecto:

### 1. `/etc/hosts`

Archivo local con una línea por cada IP y los nombres asociados. Si el dominio aparece ahí, el sistema usa esa IP sin consultar nada externo. Útil para configuraciones locales o pruebas.

### 2. DNS Resolver + `/etc/resolv.conf`

Si el dominio no está en `/etc/hosts`, el resolver del kernel consulta servidores DNS externos. `/etc/resolv.conf` tiene las IPs de esos servidores.

Opciones de `/etc/resolv.conf`:

- **`nameserver <IP>`** — servidor de nombres a consultar (hasta 3). Si no hay ninguno, se usa el propio host.
- **`domain <nombre>`** — dominio local por defecto. Se agrega a hostnames sin punto antes de resolverlos.
- **`search <dominios>`** — varios dominios para completar (mutuamente excluyente con `domain`).
- **`sortlist red[/máscara]`** — reordena IPs múltiples según la red preferida.
- **`options`** — `debug`, `ndots:n`, `timeout:n`, `attempts:n`, `rotate`, `no-check-names`, `inet6`.

Ejemplo:

```
nameserver 192.168.2.1   # DNS local de la red interna
nameserver 8.8.8.8       # Google, fallback
nameserver 200.49.159.69 # ISP, último recurso
```

## Orden de consulta: `/etc/host.conf`

Linux por defecto consulta primero `/etc/hosts` y después al DNS. Se controla con `/etc/host.conf`:

```
order hosts, bind   # primero hosts, después DNS (default)
order bind, hosts   # invertido: siempre DNS primero
```

## Caché local

Un host mantiene en caché los nombres ya resueltos. Si la IP del FQDN cambió pero la entrada local todavía no expiró (TTL), seguirá apuntando a la vieja.

## Aparece en parciales

- **Guía 3, ej. 5:** "El IP de un FQDN cambió, mi DNS ya conoce la nueva pero mi PC sigue resolviendo a la vieja." → o está fija en `/etc/hosts` (editar el archivo) o no expiró la caché DNS local (limpiarla).
- **Guía 3, ej. 6:** si conozco la nueva IP puedo agregarla a `/etc/hosts` para no esperar al DNS. Si no la conozco, puedo consultar a otro DNS (ej. el autorizado del dominio).
