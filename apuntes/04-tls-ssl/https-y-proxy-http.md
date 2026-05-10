# HTTPS y Proxy HTTP

Cuando el tráfico es HTTP en claro, un proxy puede inspeccionar, modificar y cachear. Cuando el tráfico es HTTPS, el contenido viaja cifrado entre cliente y servidor, así que un proxy "normal" no puede ver nada. Para aún así inspeccionar / filtrar / cachear HTTPS, los proxies recurren a técnicas como **MITM (Man-in-the-Middle)** o a inspección de **metadata** que sigue viajando en claro.

## El problema

- Proxy HTTP: ve URL, headers y body → puede cachear, filtrar, modificar.
- Proxy HTTPS: ve solo el handshake TLS y bytes cifrados → no puede ver el contenido.

## MITM con certificado falso

Para inspeccionar HTTPS, un proxy puede actuar como hombre en el medio:

1. El cliente inicia una conexión HTTPS hacia el sitio (ej. `bank.com`).
2. El proxy **intercepta** la conexión y le presenta al cliente un **certificado falso** generado al vuelo (firmado por una CA del proxy).
3. Si el cliente **confía en el CA del proxy** (porque el administrador de red lo instaló previamente en el equipo), acepta el certificado y la conexión TLS se establece **contra el proxy**.
4. El proxy abre **otra conexión TLS** real contra el servidor verdadero y retransmite los datos en el medio.

```
   Cliente <===TLS===> Proxy(MITM) <===TLS===> Servidor real
                       (ve todo en claro)
```

Esto le permite al proxy **leer y modificar** el contenido antes de reenviarlo. Es común en redes corporativas, escuelas y gobiernos para filtrar contenido o aplicar políticas de seguridad.

### Cómo detectar un MITM

- Verificar el certificado SSL en el navegador.
- Si el emisor del certificado **no es la CA oficial** que esperaríamos (ej. en lugar de Let's Encrypt o DigiCert aparece "Acme Corp Internal CA"), puede ser un MITM.
- Usar herramientas como **Wireshark** o extensiones de browser para inspeccionar la conexión.

### Limitaciones del MITM

- **No funciona** si el cliente no confía en el certificado del proxy (falla la validación).
- Sitios con **HSTS** (HTTP Strict Transport Security) pueden rechazar conexiones con certificados no confiables, dificultando el MITM.
- Algunas aplicaciones y browsers alertan sobre certificados sospechosos (certificate pinning, etc.).

## Bloqueo sin romper el cifrado

Aún sin hacer MITM, los proxies pueden filtrar HTTPS mirando lo que **sí viaja en claro**:

- **SNI (Server Name Indication)** en el handshake TLS: indica el dominio al que el cliente quiere conectarse y se envía sin cifrar al inicio. Permite al proxy bloquear por dominio.
- **Filtrado a nivel DNS**: evitar que el cliente resuelva ciertos dominios (devolver NXDOMAIN o IP nula).
- **Listas de IP bloqueadas** de servidores sospechosos.

Esto restringe accesos sin necesidad de ver el contenido real.

## Conclusión

- Los proxies **pueden inspeccionar HTTPS mediante MITM**, pero requieren que el cliente confíe en su CA.
- Sin romper el cifrado, pueden **bloquear sitios** mediante SNI, DNS o IPs.
- Técnica común en empresas, gobiernos y redes educativas.
- Los usuarios pueden detectar y mitigar MITM revisando certificados y usando herramientas de seguridad.

## Notas adicionales

- Cuando una app web está detrás de un **proxy reverso** (ej. nginx terminando HTTPS y reenviando a tomcat en `127.0.0.1`), la app ve siempre la IP del proxy. Para que conozca la IP real del cliente, el proxy debe agregar el header **`X-Forwarded-For`** (preguntas-teoricas ej. 15).
- Si en una URL se pone la **IP en lugar del FQDN**, el header `Host` viaja con la IP. Esto puede ser rechazado por servidores con **multihoming**, y en HTTPS también porque el certificado normalmente está emitido para el nombre, no para la IP (preguntas-teoricas ej. 26).

## Aparece en parciales

- **X-Forwarded-For** para que la app conozca la IP real detrás de un proxy reverso (preguntas-teoricas, ej. 15).
- Acceder por IP en vez de FQDN: a veces falla, especialmente en HTTPS por el certificado y/o el header `Host` (preguntas-teoricas, ej. 26).

## Fuente

- Apunte oficial, Unidad 4, p. 49-50.
