# Registros DNS

DNS es una base de datos distribuida de **resource records** (RR). Cada registro tiene la forma `(name, value, type, ttl)`.

## Tipos de registros

### A (Address)
Asocia un nombre a una IPv4.
```
ejemplo.com.   IN  A     192.0.2.1
```

### AAAA
Asocia un nombre a una IPv6.
```
ejemplo.com.   IN  AAAA  2001:0db8::1
```

### CNAME (Canonical Name)
Alias hacia otro nombre.
```
www.ejemplo.com.  IN  CNAME  ejemplo.com.
```

### MX (Mail Exchange)
Especifica el servidor de correo del dominio. El número es la prioridad (más bajo = más preferido).
```
ejemplo.com.   IN  MX  10  mail.ejemplo.com.
ejemplo.com.   IN  MX  20  backupmail.ejemplo.com.
```
Cuando un cliente de mail quiere enviar a `usuario@ejemplo.com`, hace una query MX para saber a qué servidor SMTP conectarse.

### TXT (Text)
Texto arbitrario. Usos típicos: verificación de propiedad de dominio, políticas de mail (SPF, DKIM, DMARC).
```
ejemplo.com.   IN  TXT  "v=spf1 include:_spf.ejemplo.com ~all"
```

### NS (Name Server)
Indica los servidores DNS autoritativos para el dominio.
```
ejemplo.com.   IN  NS  ns1.ejemplo.com.
ejemplo.com.   IN  NS  ns2.ejemplo.com.
```

### SRV (Service)
Define host y puerto de un servicio. Sintaxis: `_service._proto.name. TTL class SRV priority weight port target`.
```
_sip._tcp.ejemplo.com.  3600  IN  SRV  10 60 5060  sipserver.ejemplo.com.
```

### PTR (Pointer)
Resolución inversa: IP → nombre. Usa el árbol especial `in-addr.arpa`.
```
1.2.0.192.in-addr.arpa.  IN  PTR  www.ejemplo.com.
```

## El `@` en los archivos de zona

El símbolo `@` en archivos de zona representa el dominio actual de la zona. (Su uso en mails surgió para separar usuario de dominio cuando se empezaron a mandar mails entre dominios distintos.)

## Reverse zones

Para resolver IP → nombre se usa el árbol `in-addr.arpa` con la IP invertida. Ej: la IP `192.0.2.1` se busca como `1.2.0.192.in-addr.arpa`.

## Aparece en parciales

- **Guía 3, ej. 4:** un cliente de mail solo pide cuenta y contraseña. Sabe a qué servidor conectarse consultando el **registro MX** del dominio que sigue al `@`. Si el MX existe pero el server no escucha en el puerto estándar, falla la conexión.
