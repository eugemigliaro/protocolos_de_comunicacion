# Firewall

Componente que **regula el tráfico** que entra y sale de una red, basándose en **reglas**. Evita accesos no autorizados a la intranet. Opera en el nivel **más bajo** de la red (por eso suele ir incrustado en el router).

## Generaciones de firewalls

Hay cuatro tipos, en orden histórico de aparición:

### 1. Packet filter (filtrado de paquetes individuales)

El más antiguo. Decide sobre **cada paquete por separado**, mirando IP origen/destino, puertos, protocolo. **No relaciona** un paquete con los anteriores.

Algoritmo (simplificado):

```
para cada paquete:
   si matchea regla "deny":  drop
   si matchea regla "allow": forward
   si no:                    aplicar política por defecto
```

**Limitación**: no entiende **estado** → no distingue una respuesta legítima de un paquete spoofeado, y debe permitir explícitamente todo el tráfico de respuesta.

### 2. Circuit level (filtro con estado)

Verifica el **estado de la conexión** para superar la limitación anterior. Una vez establecida una sesión, se almacena:

- Identificador de la conexión.
- **Estado**: handshake, established, closing.
- Números de secuencia.
- IP origen y destino.
- Puertos origen y destino.
- Interface de red por la que **entran** los paquetes.
- Interface de red por la que **salen** los paquetes.

Permite escribir reglas como "permitir tráfico que sea respuesta a una conexión iniciada desde adentro" sin enumerar puertos. **No funciona para UDP** (UDP no tiene estado).

### 3. Application layer (proxy / firewall de aplicación)

Entiende el **protocolo de aplicación** (HTTP, FTP, SMTP, ...) y puede filtrar contenido. Un ejemplo ya visto son los **proxies HTTP** — pueden bloquear sitios, cachear, modificar headers, hacer logging detallado.

### 4. Dynamic packet filter

Última generación. Permite **modificar reglas "on the fly"** según el comportamiento observado. Entiende **UDP** (mantiene seudo-estado por flujo) y hace análisis más detallado.

## Resumen comparativo

| Generación | Estado | UDP | Inspección |
|---|---|---|---|
| Packet filter | ❌ | ✓ | Por paquete (header L3/L4) |
| Circuit level | ✓ | ❌ | Por conexión TCP |
| Application layer | ✓ | ✓ | Hasta capa 7 |
| Dynamic packet filter | ✓ | ✓ | Reglas dinámicas, UDP incluido |

## Aparece en parciales

- "Un alumno trabaja en una empresa que filtra el tráfico de salida: HTTP debe pasar por proxy, ciertos sitios están bloqueados. ¿Puede acceder a los sitios prohibidos? ¿Cómo?" → **Sí**, con un **túnel SSH dinámico** para que el tráfico salga desde el servidor remoto, salteando el proxy (preguntas-teoricas ej. 10).
- "En una red privada hay un servidor SSH y un servidor SMTP que solo acepta conexiones locales. Si desde afuera tengo acceso al SSH, ¿también podré acceder al SMTP?" → **Verdadero**: con un **túnel local SSH** al servidor SMTP (preguntas-teoricas ej. 29).

## Fuente

- Apunte oficial, Unidad 5, p. 62-64.
- Preguntas teóricas 10, 29.
