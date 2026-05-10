# Autenticación SSH

SSH ofrece tres mecanismos para autenticar a un cliente: por **claves** (usuario/contraseña), por **claves públicas** y **basada en el host**. La forma más común y recomendada es la de claves públicas.

## 1. Por claves (usuario y contraseña)

Es el método por defecto. Controla que el nombre de usuario y la contraseña sean válidos contra la base de usuarios del sistema, generalmente `/etc/passwd` y `/etc/shadow`.

Se puede deshabilitar editando `/etc/sshd_config`:

```
PasswordAuthentication no
```

## 2. Por claves públicas

Cada usuario genera un par de claves: una **privada** que queda en el host cliente (origen) y una **pública** que se copia al o los hosts servidor (destino). El servidor autentica al cliente verificando que pueda firmar un desafío con la clave privada correspondiente a la pública que tiene almacenada.

### Configuración del servidor

En `/etc/sshd_config` debe figurar:

```
PubkeyAuthentication yes
```

### Pasos en el host cliente (Linux)

Generar el par de claves:

```bash
ssh-keygen
```

Esto produce dos archivos en `~/.ssh/`:

- Clave **privada** (típicamente `id_rsa` o `id_ed25519`) — **no compartir, queda en el cliente**.
- Clave **pública** (`.pub`) — se copia al servidor.

### Pasos en el host servidor

Copiar el archivo `.pub` del cliente al servidor, dentro de `~/.ssh/authorized_keys` del usuario destino. Lo más práctico es usar:

```bash
ssh-copy-id usuario@servidor
```

Que se encarga de appendear la clave pública al `authorized_keys` correcto y dejar los permisos bien.

### Quién guarda qué

| Archivo | Dónde vive |
|---------|------------|
| Clave **privada** | En el o los hosts **origen** (clientes). |
| Clave **pública** | En el o los hosts **destino** (servidores), dentro de `~/.ssh/authorized_keys`. |

## 3. Basada en el host

Autentica al **host cliente** completo en vez de al usuario individual. Se usa muy poco en la práctica.

## Fuente

Apunte oficial Unidad 10, p. 129-130.
