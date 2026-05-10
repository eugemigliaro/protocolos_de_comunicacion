# IPTables

**iptables** es la herramienta estándar de Linux para configurar el firewall y el NAT del kernel (sobre Netfilter). Permite definir reglas que filtran, modifican o redirigen paquetes según IP, puerto, protocolo, interfaz, estado de conexión, etc. Las reglas se organizan en **tablas** (qué tipo de operación) y **cadenas** (en qué momento del recorrido del paquete se evalúan).

## Tablas

| Tabla | Para qué sirve |
|-------|----------------|
| **filter** | Filtrado de paquetes (default). Permite/bloquea. |
| **nat** | Traducción de direcciones (SNAT, DNAT, MASQUERADE). |
| **mangle** | Modificar campos del paquete en tránsito (TTL, ToS, marcas). |
| **raw** | Excepciones al sistema de tracking de conexiones (NOTRACK). |
| **security** | Etiquetado para SELinux y MAC. |

## Cadenas

Una cadena es un punto del recorrido del paquete donde se evalúan reglas.

| Cadena | Cuándo se ejecuta |
|--------|-------------------|
| **INPUT** | Paquete destinado al sistema local |
| **OUTPUT** | Paquete generado por el sistema local |
| **FORWARD** | Paquete que pasa a través del sistema (router) |
| **PREROUTING** | Antes de la decisión de ruteo (típico para DNAT) |
| **POSTROUTING** | Después de la decisión de ruteo (típico para SNAT) |

No todas las cadenas existen en todas las tablas. Combinaciones comunes:

- `filter` → INPUT, OUTPUT, FORWARD
- `nat` → PREROUTING, POSTROUTING, OUTPUT
- `mangle` → todas

## Reglas

Cada regla tiene **criterios** (matching) y una **acción** (target):

```
iptables -t <tabla> -A <cadena> <criterios> -j <acción>
```

Acciones comunes: `ACCEPT`, `DROP`, `REJECT`, `LOG`, `DNAT`, `SNAT`, `MASQUERADE`, `RETURN`.

### Ejemplos

```bash
# Permitir SSH entrante
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Bloquear todo el resto del tráfico entrante
iptables -A INPUT -j DROP

# DNAT: redirigir tráfico que llegue al puerto público 80 hacia un servidor interno
iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 80 \
    -j DNAT --to-destination 192.168.1.20:8080

# SNAT clásico: re-escribir IP origen al salir por la WAN
iptables -t nat -A POSTROUTING -o eth1 -j SNAT --to-source 200.45.12.5

# MASQUERADE: SNAT cuando la IP pública es dinámica (típico router casero)
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
```

## Comandos útiles

```bash
# Listar reglas de la tabla NAT
iptables -t nat -L

# Listar conexiones NAT activas
netstat-nat -n

# Listar reglas con números de regla
iptables -L -n --line-numbers

# Borrar todas las reglas
iptables -F           # filter
iptables -t nat -F    # nat
```

## Tres formas de hacer SNAT

Asumiendo que `eth1` es la interfaz WAN:

1. **SNAT con IP pública fija** — cuando el ISP da una IP pública estática:
   ```
   iptables -t nat -A POSTROUTING -o eth1 -j SNAT --to-source 200.45.12.5
   ```
2. **MASQUERADE** — cuando la IP pública cambia (DHCP del ISP):
   ```
   iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
   ```
3. **SNAT con rango de IPs** — si tengo varias públicas y quiero rotar:
   ```
   iptables -t nat -A POSTROUTING -o eth1 -j SNAT --to-source 200.45.12.5-200.45.12.10
   ```

## Recorrido de un paquete (resumido)

```
                    ┌───────────────┐
PAQUETE ENTRANTE → │ PREROUTING    │ → decisión de ruteo
                    │ (raw, mangle, │       │
                    │  nat-DNAT)    │       ├─→ es para mí: INPUT (mangle, filter) → proceso local
                    └───────────────┘       │                                                │
                                            │                                                ↓
                                            └─→ no es para mí: FORWARD (mangle, filter) → POSTROUTING (mangle, nat-SNAT) → SALE
                                                                                                              ↑
                                                          Proceso local genera paquete → OUTPUT (raw, mangle, filter, nat) ┘
```

## Aparece en parciales

(Sin preguntas dedicadas — `iptables` se trata más como herramienta de implementación. Los conceptos de filtrado/firewall/NAT que sí aparecen en parciales se cubren en `snat.md` y la unidad 5 — `apuntes/05-transporte/firewall.md`.)

> Fuente: Apuntes oficiales, Unidad 6, p. 101-103.
