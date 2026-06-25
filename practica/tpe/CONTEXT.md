# TPE — SOCKS5 (contexto curado)

Carpeta de **contexto para el Trabajo Práctico Especial**. No es la entrega: la entrega vive en
`practica/tpe_protos_g14/` (repo git aparte del grupo, gitignoreado desde este repo de estudio).

Acá está todo lo que una sesión de coding necesita tener a mano para trabajar el TP.

## Archivos

| Archivo | Qué es |
|---------|--------|
| `consigna.md` | La consigna oficial 2026/1, textual. Fuente de verdad #1. |
| `requisitos.md` | Checklist trazable de RF/RNF extraídos de la consigna, con nivel RFC2119. |
| `apuntes-clases-tp.md` | Las dos clases que la cátedra dio sobre este TP, pasadas a markdown. |
| `analisis-nash.md` | Qué tomar y qué evitar de la resolución del grupo anterior + nota de integridad. |
| `plan.md` | Plan por fases para resolver el TP. |
| `progreso.md` | Estado vivo: qué está hecho, qué falta, decisiones abiertas. |

## Setup del equipo (reproducir la estructura)

Todos trabajan sobre la misma estructura: se clona el repo de estudio y, **adentro de `practica/`**,
se clona el repo entregable con su nombre por defecto.

```sh
git clone <url-repo-estudio> protocolos_de_comunicacion
cd protocolos_de_comunicacion/practica
git clone https://github.com/eugemigliaro/tpe_protos_g14.git   # queda en practica/tpe_protos_g14/
```

- El nombre `tpe_protos_g14` (default del clone) es importante: de él dependen las rutas relativas
  `../tpe/` del `CLAUDE.md` del repo entregable. No renombrar la carpeta.
- El repo de estudio gitignorea `practica/tpe_protos_g14/`, así que el clon anidado no se trackea
  desde acá. Son **dos repos git independientes**.
- El **código y los entregables** se commitean en `tpe_protos_g14` (su historia se entrega).
  El **contexto/reference** (esta carpeta) se versiona en el repo de estudio.

## Regla de oro

- La **consigna** manda. Si algo de los apuntes o de nash contradice la consigna, gana la consigna.
- `practica/resolucion-tpe-nash/` es de **otro grupo**: se usa solo como referencia conceptual,
  no se copia código. Ver `analisis-nash.md`.
- Las utilidades de cátedra (`selector`/`stm`/`buffer`/parser de args) se pueden usar **con atribución**.
