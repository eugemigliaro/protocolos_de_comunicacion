# Programación — material de cátedra para el TPE

Originales descargados del campus (carpeta "Programación"). **Solo lectura / referencia.** Sirven
para entender y para **atribuir** el código de cátedra que reutilizamos. El código que efectivamente
se compila vive **integrado** en el repo entregable (`practica/tpe_protos_g14/src/`), no acá.

> Nota: los `.zip` están gitignoreados en el repo de estudio. Cada integrante los baja del campus.
> Lo que importa para el equipo es la versión **integrada y atribuida** en el repo del grupo.

## Qué es cada archivo (según el campus)

| Archivo | Qué es | Destino en el entregable |
|---------|--------|--------------------------|
| `args.c`, `args.zip` | Parser de argumentos oficial de la cátedra (RNF4/RNF7). | `src/shared/args.{c,h}` |
| `socks5nio.zip` | Código no bloqueante de cátedra: `selector`, `buffer`, `stm` (+ `socks5nio` de referencia). | `selector/buffer/stm` → `src/server/utils/`; `socks5nio` = referencia |
| `Makefile`, `Makefile.inc` | Makefile de referencia de la cátedra. | Reconciliar con el nuestro |
| `Parches de git` (`*.patch`) | Serie `git format-patch` para aplicar con `git am`: agrega `test.h`, `buffer.c`, `netutils.c`, `parser.c`, `parser_utils.c`, `selector.c`, `stm.c` como commits **atribuidos a la cátedra**. | `git am` en el repo del grupo (ver abajo) |
| `parches.zip` | Probablemente la versión zipeada de la carpeta de parches (verificar). | idem |

## Cómo se aplican los parches (en el repo del grupo)

```sh
cd practica/tpe_protos_g14
git am /ruta/a/parches/*.patch     # en orden numérico 0001..0008
```
Esto crea un commit por archivo, con la cátedra como autora → atribución automática en la historia
git (que se entrega y se evalúa). Si los parches dejan los archivos en la raíz, después se reubican
con `git mv` a `src/server/utils/` y se ajustan los includes en un commit propio.
> Falta el `0002` en la captura del campus: bajar **toda la serie** antes de aplicar.

## Atribución

Todo lo que reutilicemos de acá se atribuye explícitamente en `doc/DECISIONS.md` del repo del grupo
y en los headers de los archivos integrados. La consigna lo permite: *"Está permitido utilizar código
publicado por los docentes durante la cursada actual, siempre que se atribuya correctamente."*
