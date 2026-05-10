# Conmutación de Paquetes

La **conmutación de paquetes** parte la información en bloques (paquetes) que viajan **independientes** por la red, cada uno con su propio header (IP origen, IP destino, etc.). Cada paquete puede tomar una ruta distinta y llegar fuera de orden; se reensamblan en el destino. Es el modelo que usa Internet (IP).

## Por qué se eligió este modelo

- **Eficiente:** no hay que reservar recursos, los enlaces se aprovechan a demanda.
- **Robusto:** si se cae un router, sólo se pierden los paquetes encolados ahí, no toda la sesión.
- **Flexible:** distintos paquetes del mismo flujo pueden tomar rutas distintas.

## Costo

- Cada paquete carga toda la metadata (IP origen, IP destino, TTL, checksum, etc.) → overhead de header en todos los paquetes.
- Por cada paquete se vuelve a hacer forwarding → pueden llegar **desordenados**, es problema de la capa superior (TCP) reordenar.

## Las dos funcionalidades del router

- **Routing:** determinar la ruta que toman los paquetes según su destino. Hay distintos algoritmos (estático, dinámico — RIP, OSPF, BGP, etc.).
- **Forwarding:** mover el paquete del input correspondiente al output correspondiente, según la tabla de ruteo.

> *La cátedra distingue:* routing es **cómo se construye** la tabla, forwarding es **la decisión que toma el host** en base a esa tabla.

## Tabla de ruteo

Todos los hosts (no sólo routers) tienen tabla de ruteo, porque tienen que decidir por dónde enviar cada paquete que generan. Cada entrada tiene:

- Dirección de red
- Máscara
- Gateway o interfaz de salida

Si la IP destino no matchea con ninguna entrada → puede irse por la ruta por defecto (`0.0.0.0/0`) o ser descartada.

## Comparación rápida

| | Datagrama (IP) | Circuito virtual |
|---|---|---|
| Estado en routers | No | Sí, por CV |
| Header | Pesado | Liviano (ID del CV) |
| Orden | No garantizado | Garantizado |
| Caída de router | Pérdida parcial | Cae el CV completo |

## Aparece en parciales

- *Guía 5, Ej. 2:* diferencia entre routing y forwarding (definiciones de la cátedra arriba).
- *Parcial Teórico 1Q2025 / Preguntas teóricas, Ej. 11:* entradas inválidas en tabla de ruteo. Una entrada es válida si **al aplicar la máscara a la dirección, el resultado coincide con la dirección de la entrada**. Casos inválidos típicos: `127.0.0.1/32` no, `10.0.0.128/24` (al aplicar `/24` queda `10.0.0.0`, distinto), `192.168.0.1/24` (queda `192.168.0.0`, distinto).
- *Preguntas teóricas, Ej. 23:* enlace confiable salto a salto **no implica** servicio de red confiable extremo a extremo.

> Fuente: Apuntes oficiales, Unidad 6, p. 72-73.
