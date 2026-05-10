# Conceptos Básicos de Sockets

Un **socket** es un punto final de comunicación entre dos dispositivos en una red, identificado por una IP y un número de puerto. Permite que las aplicaciones envíen y reciban datos usando protocolos como TCP o UDP.

## Modelo cliente/servidor

- El **servidor debe estar en ejecución antes** de que el cliente intente conectarse.
- El servidor debe tener un socket abierto (la "puerta") por el cual recibir y enviar mensajes.
- El cliente necesita su propio socket y conocer **IP + puerto** del socket del servidor.

## Sockets y file descriptors

Un proceso que hace E/S usa el system call `open` que devuelve un **file descriptor** (FD). El SO implementa los FDs como un vector de punteros a estructuras.

La API de sockets reusa esa abstracción: cada socket es un handle almacenado en la tabla de file descriptors.

```
FD table del proceso:
 0 → stdin
 1 → stdout
 2 → stderr
 3 → socket pasivo (servidor escucha)
 4 → socket activo (conexión con cliente A)
 5 → socket activo (conexión con cliente B)
```

> Los servidores suelen hacer `close(0)` (cerrar stdin), porque no esperan input de teclado. Beneficio: queda un FD libre más para conexiones.

## Sockets pasivos vs activos

- **Pasivo:** espera el inicio de una conexión. Lo usa el servidor para escuchar.
- **Activo:** sirve para enviar/recibir datos sobre una conexión ya establecida.
  - Cuando llega una conexión al socket pasivo, el `accept()` crea el socket activo correspondiente.

```
Servidor                    Cliente
┌──────────────┐
│ socket pasivo│ ←─── connect ───── socket activo
│   (listen)   │
└──────┬───────┘
       │ accept()
       ▼
┌──────────────┐
│ socket activo│ ◀──── datos ────▶ socket activo
└──────────────┘
```

## Aparece en parciales

**Preguntas teóricas, ej. 18:** "Un socket pasivo TCP se usa únicamente para atender los pedidos de conexión, no para recibir datos de una conexión establecida." → **Verdadero.**

## Fuente

Apunte oficial Unidad 11, p. 133-134.
