# Apuntes de las clases del TP

Pasaje a markdown del PDF `Apuntes_Final_Protocolos_Comunicacion_TP.pdf` (dos clases que la
cátedra dio sobre este TP). Original en `inbox/`. Foco: diseño de protocolos, Makefile, sockets,
event loop y criterios de entrega.

> **Idea fuerza:** un protocolo no es solo enviar bytes por un socket. Es un conjunto de reglas:
> transporte, formato, framing, comandos, respuestas, errores, estados, cierre de conexión,
> pruebas y límites.

## 1. Lectura general

El final no es solo programar cliente+servidor que intercambian datos. Es **diseñar un protocolo
completo, documentarlo, justificar las decisiones técnicas, implementarlo de forma robusta y
entregar un proyecto que compile, se pruebe y se defienda.** En los videos aparecen archivos como
`DECISIONS.md`, `SPEC.md`, `MAP-TEXTO.md`, `MAP-BINARIO.md` → conviene documentación técnica además
del código.

Conceptos que atraviesan toda la materia:
- El protocolo debe tener especificación escrita.
- Las decisiones técnicas deben estar justificadas.
- El formato de los mensajes debe ser inequívoco.
- TCP no preserva fronteras de mensajes: el parser debe manejar lecturas parciales.
- `write()` puede escribir menos bytes que los solicitados.
- Cada cliente/conexión necesita estado propio.
- El servidor debe manejar errores sin caerse.
- El Makefile forma parte de la entrega.
- Los protocolos se entienden mejor como máquinas de estado.
- La entrega debe poder probarse de manera simple y reproducible.

## 2. Qué debería demostrar el TP final

Tres capacidades: **diseñar, implementar y defender.**

| Dimensión | Qué se espera | Evidencia |
|-----------|---------------|-----------|
| Diseño | Reglas completas del protocolo | `SPEC.md`, diagramas, ejemplos de conversación |
| Justificación | Por qué TCP/UDP, texto/binario, estados y límites | `DECISIONS.md` o equivalente |
| Implementación | Cliente y servidor robustos | `src/client`, `src/server`, `src/shared` |
| Build | Compilación simple y reproducible | Makefile, `make clean`, `make all` |
| Pruebas | Casos normales y de error | README con comandos de prueba |
| Defensa | Explicar el protocolo y sus decisiones | Diagrama de estados y ejemplos |

Entregables recomendados: `README.md`, `SPEC.md`, `DECISIONS.md`, `Makefile` (+ `Makefile.inc`),
`src/{client,server,shared}`, tests/scripts, diagrama de estados (imagen o ASCII), ejemplos de
conversación cliente-servidor.

## 3. Diseño del protocolo

Primero **separar el problema funcional del protocolo**: definir qué operaciones existen y qué
comportamiento tiene el sistema; después decidir cómo viajan esas operaciones por la red.

Preguntas a responder **antes** de programar:
1. Qué entidades participan (cliente, servidor, proxy, destino).
2. Quién habla primero.
3. Qué transporte (TCP/UDP).
4. Textual o binario.
5. Cómo termina un mensaje (newline, length-prefix, header fijo).
6. Qué comandos existen y en qué estados son válidos.
7. Qué respuesta corresponde a cada comando.
8. Cómo se representan los errores.
9. Cómo se cierran las conexiones.
10. Si soporta pipelining y cómo se mantiene el orden.
11. Límites: tamaños máximos, caracteres permitidos, buffers.
12. Cómo se prueba con casos válidos e inválidos.

**TCP vs UDP** — guideline: si el protocolo necesita orden, respuestas asociadas a pedidos,
conexión persistente, autenticación o pipelining → TCP es lo natural. Si se elige UDP hay que
explicar cómo se resuelve pérdida, duplicados, reordenamiento y reintentos.

**Texto vs binario** — texto: fácil de debuggear (ncat/telnet), legible, más bytes y parseo más
caro, requiere definir separadores/escapes. Binario: más eficiente y compacto, más difícil de
debuggear, requiere definir tamaños y códigos. Guideline: texto donde importa claridad/depuración;
binario donde importa performance o estructura compacta.

## 4. Protocolo MAP en texto (ejemplo de clase)

Key-value store simple: GET / SET / DEL por clave. Decisiones: transporte TCP, formato texto, el
cliente habla primero, cada comando ocupa una línea (`\n`), comandos y args separados por espacios.

| Comando | Args | Éxito | Error |
|---------|------|-------|-------|
| GET | clave | `+OK valor` | `-ERR` si no existe o inválida |
| SET | clave valor | `+OK` | `-ERR` ante error interno o formato inválido |
| DEL | clave | `+OK` | `-ERR` si no existe o inválida |

Validaciones: clave 1–64 chars, regex `[a-zA-Z\-_0-9]{1,64}`, valor hasta 1024 chars. Definir qué
pasa si faltan/sobran argumentos o hay caracteres inválidos. Respuestas: una por línea, `+OK`/`-ERR`,
dato tras el status separado por espacio, **mismo orden** que los pedidos.

**Pipelining:** el cliente puede mandar varios comandos sin esperar respuesta; el servidor responde
en el mismo orden. Soportarlo obliga a parsear múltiples comandos del buffer de entrada y conservar
el orden de respuestas. Si no se soporta, aclararlo explícitamente en la spec.

## 5. Protocolo MAP en binario

Misma funcionalidad, representación con códigos y campos de longitud.
- **Handshake de versión:** cliente manda `VER` (1 byte); servidor responde su versión, o `0` si
  no es compatible (y se cierra). Incluir versión permite evolucionar sin romper compatibilidad.
- **Request:** `METHOD` (1 byte): GET=`0x01`, SET=`0x02`, DEL=`0x03`.
- **Strings con longitud:** `LEN(1) + STRING` → evita depender de separadores; se leen exactamente
  `LEN` bytes. Ej. `SET "pepe" "pipi"` → `0x02 | 0x04 "pepe" | 0x04 "pipi"`.
- **Response:** `STATUS` (1 byte): 0=OK, 1=ERR. Si era GET y OK, incluye el valor como `LEN+STRING`.

## 6. TCP, EOF, shutdown y pipelining

**TCP es un stream, no una cola de mensajes.** Error clásico: pensar que cada `write()` del cliente
corresponde a un `read()` del servidor. Falso.

| Situación | Qué puede pasar |
|-----------|-----------------|
| Cliente manda un comando | El servidor lo lee completo o **partido** en varios `read()` |
| Cliente manda varios comandos | El servidor puede recibirlos **juntos** en un `read()` |
| Servidor responde mucho | `write()` puede escribir **solo una parte** |
| Cliente cierra escritura | El servidor ve **EOF** al leer, pero puede tener respuestas pendientes |

**`shutdown` de escritura:** cuando el cliente no quiere enviar más, hace shutdown de su lado de
escritura (no implica dejar de leer respuestas). Flujo: cliente envía comandos → shutdown de
escritura → servidor detecta EOF → procesa pendientes → envía respuestas pendientes → se cierra la
conexión. Guideline: distinguir "no escribo más" de "cierro toda la conexión". En TCP, `shutdown`
permite cerrar medio canal y seguir leyendo.

## 7. Pruebas manuales con herramientas de red

Clases muestran pruebas con `ncat`/`nc` y SMTP. Sirven para interactuar con protocolos de texto
reales sin escribir un cliente. Ejemplo SMTP: `ncat aspmx.l.google.com 25`, `EHLO ...`, y el server
responde con códigos y anuncia capacidades. Aplicación al TP: si el protocolo es textual, debería
poder probarse con ncat; el README debería incluir ejemplos para levantar server y probar; incluir
casos de error, no solo el camino feliz; mensajes de error consistentes y predecibles.

```
make clean
make all
./bin/server 1234
ncat localhost 1234
```

## 8. Organización del proyecto y Makefile

**Subir el Makefile: forma parte de la entrega.** Estructura recomendada:

```
src/
  client/client.c
  server/server.c
  shared/shared.c  shared.h
bin/
obj/
Makefile
Makefile.inc
.gitignore
```

Header guard: `#ifndef _SHARED_H_ / #define _SHARED_H_ / ... / #endif`.

Makefile — puntos clave: target `all`; compilar client y server; `clean`; crear `bin/` y `obj/`
si no existen; flags `-Wall -pedantic -g`; compilar el código compartido una vez y linkearlo donde
corresponda; `.PHONY` para targets que no son archivos; reglas para construir `.o` desde `.c`.

```make
include ./Makefile.inc
SERVER_SOURCE=$(wildcard src/server/*.c)
CLIENT_SOURCE=$(wildcard src/client/*.c)
SHARED_SOURCE=$(wildcard src/shared/*.c)
OUTPUT_FOLDER=./bin
OBJECTS_FOLDER=./obj
all: client server
server: $(SERVER_OUTPUT_FILE)
client: $(CLIENT_OUTPUT_FILE)
clean:
	rm -rf $(OUTPUT_FOLDER)
	rm -rf $(OBJECTS_FOLDER)
.PHONY: all server client clean

# Regla patrón para objetos
obj/%.o: src/%.c
	mkdir -p $(dir $@)
	$(COMPILER) $(COMPILER_FLAGS) -c $< -o $@
```
`$<` = primera dependencia, `$@` = target. Error común: `No rule to make target obj/.../x.o` →
falta la regla patrón para generarlo desde `.c`.

## 9. Servidores event-driven y multiplexing de I/O

Un servidor que atiende muchos clientes no puede bloquearse esperando a uno solo. Necesita saber
qué file descriptors están listos para leer o escribir. Mecanismos: `pselect`, `poll`, `epoll`,
`kqueue`, y abstracciones tipo *pollinator/selector*.

| Interés | Significado | Cuándo activarlo |
|---------|-------------|------------------|
| Lectura | Avisame cuando haya bytes para leer | Cuando el buffer de entrada tiene espacio y la conexión sigue activa |
| Escritura | Avisame cuando pueda escribir sin bloquear | Cuando hay datos pendientes en el buffer de salida |
| Freeing/cierre | Liberar recursos de la conexión | Cuando la conexión termina o hay error fatal |

Handlers por evento:
```c
PollerEntryHandlers handlers = {
    .on_readable = &my_read_handler,
    .on_writeable = &my_write_handler,
    .on_freeing  = &my_free_handler
};
```
`epoll` (Linux): se registra una lista de fds de interés y el kernel informa cuáles están listos.
Permite manejar muchos sockets sin un hilo bloqueante por conexión.

## 10. Buffers, lecturas y escrituras parciales

Cada conexión necesita su **propio estado** (no alcanzan variables globales):

| Dato de estado | Para qué |
|----------------|----------|
| Buffer de entrada | Acumular bytes hasta completar un comando/frame |
| Buffer de salida | Guardar respuestas pendientes de envío |
| Estado del parser | Saber qué parte del mensaje se está leyendo |
| Estado del protocolo | Saber si está en handshake, transacción, cierre, etc. |
| Bytes escritos | Manejar escrituras parciales |
| Datos de sesión | Usuario, destino, clave actual, etc. |

**Regla obligatoria (lecturas):** nunca asumir que un `read()` devuelve un request completo. Puede
devolver medio mensaje, uno exacto, varios juntos, 0 (EOF) o -1 (error).

**Regla obligatoria (escrituras):** nunca asumir que `write()` envía todo el buffer. Puede escribir
solo una parte; el resto queda pendiente para un futuro evento de escritura.
```c
amount_written = write(fd, state->buf, state->buf_size);
new_buf_size = state->buf_size - amount_written;
state->buf_size = new_buf_size; /* lo pendiente se conserva para el próximo write */
```
Después de leer/escribir, **recalcular intereses**: si ya no hay datos pendientes, no hace falta
interés en escritura. `poller_set_entry_interests(entry, calc_interests_for(state));`

## 11. Máquinas de estado

Representar protocolos como máquinas de estado evita código caótico y deja claro qué comandos son
válidos en cada momento. Ejemplo POP3: `AUTH -> TRANSACTION -> UPDATE` (no tiene sentido `RETR`
antes de autenticarse).

Aplicación a un TP:
```
START -> HANDSHAKE -> TRANSACTION -> CLOSING -> CLOSED
```
| Estado | Comandos válidos | Transiciones |
|--------|------------------|--------------|
| HANDSHAKE | Versión / saludo inicial | Si compatible → TRANSACTION; si no, cierre |
| TRANSACTION | Comandos principales | Sigue, o → CLOSING con QUIT/EOF |
| CLOSING | No acepta nuevos comandos | Envía pendientes y cierra |
| CLOSED | Ninguno | Recursos liberados |

El **parser** también puede ser una máquina de estados (reconoce comando, espacios, args, fin de
línea en texto; método, longitud, bytes de string, status en binario).

## 12. SOCKS5, proxy y relaying

Diagrama: `CLIENTE <-> SOCKS5 <-> DESTINO`. Un proxy no solo recibe y responde: **conecta contra
otro destino y reenvía datos en ambas direcciones.**

| Conexión | Rol |
|----------|-----|
| Cliente–Proxy | El cliente habla con el servidor SOCKS5 |
| Proxy–Destino | El proxy abre una conexión hacia el servidor final |
| Relay cliente→destino | Bytes del cliente se envían al destino |
| Relay destino→cliente | Bytes del destino se envían al cliente |

Guideline: un proxy necesita **buffers y estados separados para ambos sentidos**, y manejar cierres
parciales (puede cerrarse la escritura de un lado mientras todavía quedan bytes pendientes del otro).

## 13. getaddrinfo y resolución de direcciones

```c
int getaddrinfo(const char *restrict node,
                const char *restrict service,
                const struct addrinfo *restrict hints,
                struct addrinfo **restrict res);
```
Permite resolver hostnames y servicios/puertos, devuelve estructuras listas para `connect()`/`bind()`,
facilita soportar IPv4 e IPv6, evita hardcodear IPs o asumir `AF_INET`.

## 14. Calidad de código

| Mala práctica | Mejor práctica |
|---------------|----------------|
| Nombres con typos (`neim`, `produkt`) | Nombres claros (`name`, `product`) |
| Mezclar idiomas sin criterio | Un idioma consistente |
| Funciones gigantes | Separar parseo, validación, ejecución y respuesta |
| Estados implícitos en flags sueltos | Definir enums o estructuras de estado |
| Errores silenciosos | Retornar códigos o mensajes de error claros |
| Código duplicado entre cliente y servidor | Mover estructuras comunes a `shared` |

Responsabilidades recomendadas: **Parser** (bytes → comandos/frames), **Validador** (formato, args,
límites), **Motor de comandos** (ejecuta la operación), **Encoder** (respuestas internas → bytes),
**I/O** (leer/escribir manejando parcialidades), **Estado de conexión** (en qué punto está cada cliente).

## 15. Plantillas reutilizables

`DECISIONS.md`: Transporte / Formato / Framing / Pipelining / Cierre de conexión / Límites.
`SPEC.md`: Objetivo / Estados / Formato general de mensaje / Comandos (tabla) / Errores / Ejemplos /
Cierre / Casos borde. `README.md`: Compilación / Ejecución / Pruebas manuales / Casos de prueba.

## 16. Checklist integral de entrega

| Área | Checklist |
|------|-----------|
| Documentación | README, SPEC, DECISIONS, ejemplos, diagrama de estados |
| Protocolo | Transporte, formato, framing, comandos, errores, límites, cierre |
| Implementación | Cliente, servidor, shared, parser incremental, estado por conexión |
| Robustez | read parcial, write parcial, EOF, shutdown, errores sin crash |
| Makefile | all, client, server, clean, obj, bin, flags, .PHONY |
| Pruebas | casos válidos, inválidos, pipelining si aplica, cierre, mensajes grandes |
| Defensa | justificar cada decisión y dibujar la máquina de estados |

Detallada: el protocolo indica quién habla primero, define formato exacto de requests/responses,
errores/códigos, máximos (clave, valor, buffers/frames), si soporta pipelining, qué pasa al recibir
EOF; el servidor no asume que un read == un mensaje ni que write escribe todo; cada conexión tiene
estado propio; el parser maneja mensajes incompletos; el build funciona con `make clean && make all`;
el README permite ejecutar sin adivinar; hay ejemplos reproducibles; los errores de entrada no tiran
abajo el servidor; el código está separado por responsabilidades.

## 17. Preguntas probables de defensa

- ¿Por qué TCP y no UDP? ¿Por qué texto/binario?
- ¿Cómo sabe el receptor dónde termina un mensaje?
- ¿Qué pasa si llega medio mensaje? ¿Y si llegan varios juntos? ¿Y si `write` escribe solo una parte?
- ¿Cómo manejan EOF del cliente? ¿Soporta pipelining y cómo conservan el orden?
- ¿Qué estados tiene el protocolo? ¿Qué comandos son válidos en cada estado?
- ¿Cómo pruebo el servidor manualmente? ¿Cómo compilo desde cero?
- ¿Qué archivos debo tocar para agregar un nuevo comando?
- ¿Qué límites impiden entradas gigantes o inválidas? ¿Cómo se liberan los recursos de cada conexión?

> **Cierre:** un buen TP de protocolos no solo funciona: deja claro qué reglas sigue, por qué toma
> esas decisiones, cómo maneja los casos borde y cómo puede probarse. La documentación, el Makefile
> y la robustez del parser son tan importantes como el camino feliz.
