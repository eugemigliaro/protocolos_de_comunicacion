# OpenSSH

OpenSSH es la implementación libre de SSH que viene por defecto en las distribuciones Linux. SSH (Secure Shell) es un reemplazo seguro de Telnet: tanto comandos como datos viajan encriptados, pero además permite hacer túneles, transferir archivos y mucho más.

## Qué ofrece SSH

- **Autenticación:** SSH se apoya en SSL — en la primera conexión se piden los certificados.
- **Encriptación:** evita el spoofing por man-in-the-middle.
- **Integridad:** garantiza que los datos no fueron alterados en tránsito.

SSH es el reemplazo seguro de varias aplicaciones legacy (Telnet, rlogin, rsh, rcp, FTP).

## Servidores SSH disponibles

- **OpenSSH** — Windows, Unix, Linux. Es el que se usa en la materia.
- **SSH Communications** — Windows, Unix, Linux.
- **VanDyke's VShell, Bitvise** — alternativas comerciales.

## Componentes de OpenSSH

| Comando | Función |
|---------|---------|
| `sshd` | Demonio que maneja conexiones entrantes. **No** debe iniciarse desde `inetd`/`xinetd`. |
| `ssh` | Cliente. Reemplaza a `rsh`/`rlogin` para login remoto o ejecución remota de comandos. |
| `scp` | Copia segura de archivos (reemplaza `rcp`). |
| `sftp` | FTP seguro. |
| `ssh-keygen` | Generador de pares de claves pública/privada. |

## Configuración: `/etc/sshd_config`

Es el archivo principal de configuración del demonio. Algunas opciones generales:

- **Puerto:** por defecto 22. Conviene cambiarlo en uso personal — cualquier atacante prueba ese puerto primero.
- **Versiones del protocolo:** existen v1 y v2; **no usar v1** porque tiene agujeros de seguridad conocidos.
- **IPs en las que escucha:** `0.0.0.0` para todas las IPv4, `::` para todas las IPv6.
- **Login Grace Time:** tiempo otorgado para completar el login. Si se permiten muchas conexiones simultáneas y se llega al límite, no se aceptan nuevas → ataque DoS posible.
- **Permitir root:** opción para habilitar/deshabilitar login directo como root.
- **Carpetas ocultas:** opción para tratar como inválidas las carpetas `.ssh` invisibles.

## Uso básico del cliente

Login interactivo:

```bash
ssh usuario@host
```

Ejecución remota de un comando (sin abrir shell):

```bash
ssh usuario@host "comando"
```

## Flags útiles

- `-p <puerto>` — usar un puerto distinto al 22.
- `-i <keyfile>` — usar una clave privada específica.
- `-N` — **no abre prompt** para ejecutar comandos. Útil cuando solo se quiere armar un túnel (ver `local-port-forwarding.md`).
- `-L`, `-R`, `-D` — port forwarding (local, remoto, dinámico).
- `-g` — permite que el socket de forwarding escuche en `0.0.0.0` (gateway mode).

## Fuente

Apunte oficial Unidad 10, p. 123-125.
