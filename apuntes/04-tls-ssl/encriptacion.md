# Encriptación

Mecanismo para que dos puntas puedan intercambiar información de forma confidencial: el texto plano se transforma en texto cifrado mediante un algoritmo y una clave, y solo quien tenga la clave correcta puede revertirlo.

## Criptografía simétrica (clave compartida)

Roy y Moss usan la **misma clave** para cifrar y descifrar. Roy encripta el texto plano con su clave, lo manda, y Moss aplica el algoritmo inverso con esa misma clave para recuperar el plano.

```
   Roy                                Moss
   ----                               -----
   plano --[E con K]--> cifrado --[D con K]--> plano
```

- Ventaja: rápido, apto para encriptar volúmenes grandes de datos.
- Desventaja: cómo distribuir la clave de forma segura.

## Criptografía asimétrica (clave pública / privada)

Cada parte tiene un **par de claves**: una pública (se puede repartir libremente) y una privada (se guarda en secreto). Lo que se cifra con una solo se descifra con la otra.

### Confidencialidad

Si Roy quiere mandarle algo confidencial a Moss, lo cifra con la **clave pública de Moss**. Solo la **clave privada de Moss** puede descifrarlo.

```
plano --[E con pubMoss]--> cifrado --[D con privMoss]--> plano
```

Esto se usa generalmente para **compartir una clave simétrica** y después seguir la comunicación con un algoritmo simétrico robusto (más eficiente).

### Autenticidad

Lo que se cifra con la **clave privada** solo se descifra con la **clave pública** correspondiente. Sirve para demostrar que el mensaje fue cifrado por el dueño de esa privada (ya que es la única que puede haberlo hecho).

```
plano --[E con privX]--> cifrado --[D con pubX]--> plano
```

Si la desencriptación con la clave pública de X funciona, entonces el mensaje vino de X (o alguien con su privada).

### Verificación de identidad (challenge)

¿Cómo hace Roy para asegurarse de que está hablando con Moss y no con un impostor? Le manda una frase, Moss la cifra con su clave privada y se la devuelve. Roy desencripta con la pública de Moss; si coincide con la frase original, se confirma que del otro lado está Moss.

## Por qué se usan combinadas

Asimétrica es lenta para grandes cantidades de datos pero resuelve el problema de distribución de claves. Simétrica es rápida pero necesita que la clave esté del otro lado. La práctica habitual (y la de SSL/TLS) es:

1. Usar asimétrica para autenticarse y acordar una clave de sesión.
2. Usar simétrica con esa clave para cifrar el tráfico real.

## Fuente

- Apunte oficial, Unidad 4, p. 45-46.
