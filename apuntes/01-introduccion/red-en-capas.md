# Red en Capas

El modelo en capas estandariza la comunicación entre computadoras: cada capa tiene tareas específicas y le brinda un servicio a la capa inmediatamente superior. El intercambio de información es **entre las dos puntas de una misma capa** (lógicamente), pero físicamente la información viaja de arriba hacia abajo en el host emisor y de abajo hacia arriba en el receptor.

## Por qué surgió

En los sistemas de propietario (principios de los 80'), para conectar dos computadoras tenían que ser del mismo fabricante:

- Misma placa de red.
- Programa cliente compatible con programa servidor.
- Misma codificación.
- Etc.

Con Internet apareció la necesidad de **interconectar redes heterogéneas**, lo que rompió ese esquema. Hizo falta definir estándares de comunicación independientes del fabricante → modelo de capas.

## Modelo OSI (1984)

Tiene **7 capas**:

```
+-------------------+
| 7. Aplicación     |
+-------------------+
| 6. Presentación   |
+-------------------+
| 5. Sesión         |
+-------------------+
| 4. Transporte     |
+-------------------+
| 3. Red            |
+-------------------+
| 2. Enlace         |
+-------------------+
| 1. Física         |
+-------------------+
```

Cada capa toma los servicios de la capa inferior para enviar el paquete y le ofrece un servicio definido a la superior.

## Independencia entre capas

Una capa no hereda automáticamente las propiedades de las inferiores. Esto trae varias consecuencias:

- Si una capa inferior es **con conexión**, la superior **no necesariamente** ofrece servicio con conexión.
  Ej: HTTP (sin conexión, en su semántica clásica) corre sobre TCP (con conexión).
- Si una capa inferior es **sin conexión y no confiable**, la superior **sí puede** garantizar la recepción implementando confiabilidad por su cuenta.
  Ej: TCP da confiabilidad sobre IP.
- La confiabilidad de capa de **enlace** sólo aplica entre **hosts adyacentes** (mismo segmento). Que A↔M y M↔N sean confiables a nivel enlace **no implica** que A↔N a nivel red lo sea: si M se cae después de recibir, los datos se pierden.

## Aparece en parciales

- **Ej 2 (Guía 1) / Ej 36 (preguntas teóricas)**: "Si el protocolo de una capa del modelo OSI es con conexión, entonces la capa superior también ofrecerá un servicio con conexión." → **FALSO**. Las capas son independientes por diseño.
- **Ej 2 (Guía 1) / Ej 23 (preguntas teóricas)**: "Si los protocolos de enlace entre A-M, M-N y N-Z son confiables, entonces el protocolo de red entre A y Z es confiable." → **FALSO**. La confiabilidad de enlace es punto a punto entre adyacentes; no se compone.
- **Ej 2 (Guía 1)**: "Si la capa inferior es sin conexión y no confiable, la superior no puede garantizar la recepción." → **FALSO**. La capa superior puede implementar la confiabilidad (como TCP sobre IP).

## Referencias

- PDF, Unidad 1, p. 6-7.
- Guía 1, Ej 2.
