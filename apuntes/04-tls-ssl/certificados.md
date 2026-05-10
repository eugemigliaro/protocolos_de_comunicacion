# Certificados

Documento que asocia una clave pública a una identidad (un servidor, un dominio) y está firmado por una entidad certificante (CA, Certificate Authority) reconocida. Sirve para que el cliente pueda verificar que el servidor con el que habla es realmente quien dice ser.

## Cómo se arma un certificado

1. El servidor genera un par de claves (pública y privada).
2. Crea un certificado que contiene la clave pública y datos de identidad (FQDN, organización, etc.).
3. Firma el certificado, ya sea:
   - con un **CA reconocido** (DigiCert, Let's Encrypt, etc.), que es lo habitual en producción;
   - **autofirmado** (uno mismo), útil en entornos internos pero genera advertencias en el browser.

## Qué tiene un certificado

- Clave pública del servidor.
- Datos de la entidad certificante (CA emisora) y su clave pública.
- Datos del titular del certificado (CN, emisor, etc.).
- Período de vigencia (fecha desde / hasta).
- Firma digital del CA.

## Cómo lo usa el cliente

- Cuando me conecto al puerto 443 (HTTPS) el servidor me **descarga el certificado** si no lo tengo.
- Confío en el certificado por la **firma del CA**: si reconozco al CA y la firma es válida, confío en el contenido.
- Los browsers traen una lista de CAs de confianza por default.

## Cuando el browser no reconoce la firma

Si el browser o programa **no puede reconocer la firma** del certificado (autofirmado, CA desconocido, certificado vencido, dominio que no coincide), avisa al usuario con una advertencia. Esto es típico en:

- Servidores con certificado autofirmado.
- Proxies MITM corporativos sin instalar la CA en el cliente (ver `https-y-proxy-http.md`).
- Certificados expirados.

## Fuente

- Apunte oficial, Unidad 4, p. 46-47.
