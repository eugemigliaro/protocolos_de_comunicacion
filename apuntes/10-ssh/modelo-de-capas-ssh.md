# Modelo de Capas SSH

SSH no es una capa única: internamente está estructurado en **tres subprotocolos** que se apilan sobre TCP. Cada uno cumple una función distinta y se ejecuta uno arriba del otro.

```
+--------------------------------------------+
|  SSH Connection Protocol                   |  ← multiplexa canales lógicos
+--------------------------------------------+
|  SSH User Authentication Protocol          |  ← autentica al cliente
+--------------------------------------------+
|  SSH Transport Layer Protocol              |  ← autentica al servidor + privacidad + integridad
+--------------------------------------------+
|  TCP                                       |
+--------------------------------------------+
```

## Transport Layer Protocol

- Provee **autenticación del servidor**, **privacidad** (encriptación) e **integridad**.
- Opcionalmente puede **comprimir** datos.
- Utiliza los servicios de **TCP**.

Es la capa que negocia algoritmos, intercambia claves y arma el canal seguro. Cuando esta capa termina, ya hay un túnel encriptado y autenticado del lado del servidor — pero todavía no se autenticó el cliente.

## User Authentication Protocol

- Provee **autenticación del lado del cliente**.

Es donde corren los métodos vistos en `autenticacion.md` (password, clave pública, host-based). Se ejecuta **sobre** el canal seguro armado por la capa de transporte.

## Connection Protocol

- Multiplexa el túnel seguro en distintos **canales lógicos**.
- Algunos canales lógicos posibles:
  - **Sesiones de shell seguras**.
  - **TCP port forwarding** (los túneles `-L`, `-R`, `-D`).
  - **Conexiones X11**.

Es lo que permite, por ejemplo, abrir varios túneles y una sesión interactiva sobre la misma conexión SSH simultáneamente.

## Resumen

| Capa | Qué autentica | Qué hace |
|------|---------------|----------|
| Transport | Servidor | Encriptación + integridad + (opc.) compresión sobre TCP. |
| User Auth | Cliente | Login por password / clave pública / host-based. |
| Connection | — | Multiplexa el túnel en canales (shell, port forwarding, X11). |

## Fuente

Apunte oficial Unidad 10, p. 130-131.
