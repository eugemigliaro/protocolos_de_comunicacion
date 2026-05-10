# DNAT (Destination NAT)

Técnica complementaria a NAPT que **cambia la IP destino** de los paquetes que **llegan al router** desde afuera, redirigiéndolos a un host de la red privada. Sirve típicamente para **publicar un servidor interno** sin asignarle una IP pública propia.

## Caso de uso típico — Port forwarding

Queremos exponer al exterior un servidor web que vive en `192.168.1.4:8080`. Opciones:

- **Mala**: pedirle al ISP **dos IPs públicas**, una para el router y otra para el servidor. Es **caro** y, peor, **inseguro** — el servidor queda expuesto directamente a internet.
- **Buena**: configurar el firewall para que **lo que llegue al puerto 80 del router** se reenvíe a `192.168.1.4:8080`. Eso es DNAT.

```
Internet ──► router (IP pública 200.1.1.1, puerto 80)
                │   DNAT: dst 200.1.1.1:80 → 192.168.1.4:8080
                ▼
          192.168.1.4 (servidor real)
```

## Funcionamiento

Cuando llega un paquete entrante con destino `<IP_pública_router : puerto_publicado>`:

1. El router consulta su regla de DNAT.
2. Reescribe el destino: cambia la IP por la del servidor interno y, si aplica, también el puerto.
3. Agrega la traducción a la tabla **por la duración de la conexión** (igual que NAPT).
4. Forwardea el paquete a la red interna.
5. Las respuestas del servidor pasan de vuelta por el router, que reescribe el origen para que el cliente externo siga viendo `<IP_pública : puerto_publicado>` como contraparte.

Notar que para el cliente externo **el servidor parece estar en la IP pública del router** — la red privada queda oculta.

## DNAT vs SNAT

| | SNAT (NAPT) | DNAT |
|---|---|---|
| Reescribe | IP y puerto **de origen** | IP y puerto **de destino** |
| Dirección | Salida (host interno → exterior) | Entrada (exterior → host interno) |
| Uso típico | Compartir IP pública para salir | Publicar un servidor interno |

En la práctica, ambos suelen coexistir en el mismo router/firewall (iptables tiene cadenas separadas POSTROUTING para SNAT y PREROUTING para DNAT).

## Aparece en parciales

- (No aparece preguntado directamente en las preguntas teóricas relevadas, pero es contraparte de SNAT, que sí — ver `napt.md`.)

## Fuente

- Apunte oficial, Unidad 5, p. 61-62.
