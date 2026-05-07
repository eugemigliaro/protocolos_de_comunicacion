# Apuntes — mis notas organizadas por unidad

Esta es mi base de conocimiento curada. Lo que está acá es lo que estudio.

## Estructura
Una carpeta por unidad, con prefijo numérico que matchea el PDF oficial:
`01-introduccion/`, `02-http/`, ..., `11-sockets/` (no hay 09).

Dentro de cada unidad, un archivo `.md` por concepto importante, con nombre en kebab-case en español. Ejemplos:
- `apuntes/03-dns-y-mail/dns-spoofing.md`
- `apuntes/05-transporte/control-de-congestion.md`
- `apuntes/06-red/subredes.md`

## Estilo de nota
- Encabezado con el concepto.
- Una definición corta arriba de todo (1-3 oraciones).
- Después, secciones con detalles, ejemplos, diagramas en ASCII si aplica.
- Si la nota se construyó a partir de varias clases o fuentes, dejar la fecha o referencia al final.

## Cómo se llena
Hay dos caminos:
1. **Manualmente**: escribo directo acá cuando estudio.
2. **Vía workflow desde `inbox/`**: el workflow "organizá mis notas" appendéa contenido del inbox al archivo correspondiente.

Cuando el workflow appendéa, deja una línea separadora (`---`) y un subtítulo con la fecha de la nota original.

## Para preguntas
Si Claude necesita responder una pregunta sobre la materia, debe leer primero los archivos de la unidad correspondiente acá. Si no alcanza, recién ahí ir a `/material`.
