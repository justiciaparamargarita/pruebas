# Instrucciones para la IA: preparar las pruebas de `pruebas/`

Para una IA que corre en este proyecto (`/data/www/iurix/watson/proyectos/margarita`). Respetar `CLAUDE.md` y `/data/www/iurix/sherlock/formato-md-watson.md`. Sherlock lee estos `.md` y no los corrige: si algo no cumple, se arregla acá.

## Qué hay que hacer

Para cada prueba de `pruebas/` (hoy los videos del 25/03/2020; después audios y fotos) dejar un `.md` junto al original con: datos técnicos, transcripción del audio y, en los videos, lectura de las imágenes. Los videos de ese día son `20200325_104613`, `104751`, `105605`, `105831`, `112310`, `122731`, `123255` y `123506` (más `123506_rotado`). Empezar por `123506`, `123255` y `122731`.

## Reglas

- Nunca sobreescribir un `.md` que ya existe: puede estar editado a mano. Si existe, completarlo solo si falta una sección y avisar qué agregaste.
- No hacer commits ni push. El usuario revisa con git y comitea a mano.
- No inventar. Si no se ve o no se oye, `[ilegible]` o `[dudoso: texto]`.
- Nada editorial, opinión ni conclusiones dentro del `.md`. Lo que se ve y se oye, nada más.
- No identificar personas por la cara. No atribuir voces: lo hace el usuario.
- No repetir la transcripción de audio si el `.md` ya la tiene.

## Nombres

- El original conserva su nombre (`20200325_123506.mp4`, `WhatsApp Ptt 2021-06-24 at 14.47.41.ogg`).
- El `.md` va al lado con el mismo nombre base: `20200325_123506.md`. Si el original no cumple (minúscula, ASCII, hasta 64 caracteres), el `.md` usa un nombre limpio y `archivo` lleva el nombre real.
- Para el repo publicado se ubica por fecha, y la fecha ya está en la ruta, así que el nombre lleva solo la hora: `AAAA/mm/dd/video_HHMMSS.md` (audio: `audio_HHMMSS.md`, foto: `foto_HHMMSS.md`). El original no se renombra nunca: su nombre tiene la hora de inicio y el informe lo cita; el encabezado `archivo` guarda el nombre real.
- Fotogramas (opcionales, solo si el usuario los pide guardar): `AAAA/mm/dd/fotogramas_HHMMSS/NNN.jpg`, uno cada 3 segundos, 800 px de ancho, `NNN` = segundo de inicio con tres cifras.
- El mp4 pesado no va al repo: solo el `.md`. En el encabezado queda el `sha256` del original.

## Encabezado del `.md`

Entre `---`, una clave por línea, valores entre comillas.

| Clave | Contenido |
|---|---|
| `fecha` | `AAAA-MM-DD` de la grabación |
| `expediente` | `MPF00462972` |
| `tipo` | `otro` |
| `origen` | `propio` |
| `archivo` | nombre real del original |
| `sha256` | hash del original |
| `inicio` | `AAAA-MM-DD HH:MM:SS` hora local (del nombre del archivo) |
| `fin` | `AAAA-MM-DD HH:MM:SS` (`creation_time` de ffprobe; en estos teléfonos es el fin de la grabación) |
| `duracion_s` | segundos |
| `transcripcion` | herramienta y parámetros (p. ej. `faster-whisper large-v3, VAD, es`) |
| `revision_ia` | modelo y nivel que leyó los fotogramas |
| `nota_transcripcion` | solo si hay dudas generales |

Los datos de los `.md` actuales (`md5`, `informe`, `inspeccion`) pueden quedar; no borrarlos.

## Cuerpo, en este orden

1. `Transcripción del audio:` una línea por tramo, `- 37–38 s: texto`. Si hay una voz en la línea, dejar `[hablante: ?]` al principio del texto: `- 37–38 s: [hablante: ?] Muchachos, ¿qué pasa?`. Solo se escribe una hipótesis (`[hablante: ¿seguridad?]`) si hay una pista visible en la imagen (uniforme, credencial, cartel); si no, `?`. Sin habla: `Transcripción del audio: sin habla reconocible.`
2. `Imagen (lectura de IA):` una línea por fotograma, `- 12 s: …`, con lo que se ve y solo eso: personas presentes (cantidad, ropa, uniforme, si portan credencial legible), carteles y su texto, camas y quién está en cada una, qué hay en el piso y sobre las camas, objetos en las manos, el cuarto o pasillo. Sin interpretar intenciones. Cartel ilegible: `[ilegible]`.

## Pasos

1. `ffprobe` para duración y `creation_time`; calcular `sha256`.
2. Si hay versión rotada (`_rotado`), usarla para los fotogramas.
3. `ffmpeg -i ARCHIVO -vf "fps=1/3,scale=800:-1" …/NNN.jpg` en un directorio temporal fuera del proyecto.
4. Leer cada fotograma y escribir su línea de `Imagen (lectura de IA)`.
5. Armar el `.md` solo si no existe (reglas arriba).
6. Validar con `/data/www/iurix/sherlock/bin/formato` y corregir.
7. Informar al usuario: qué archivos escribiste, cuáles saltaste por existir y qué quedó dudoso. No comitear.

## Después (lo hace el usuario)

Comitea al repo `pruebas`, lo baja en Sherlock (`bajar` o desde la web), y desde la web asigna quién habla en cada línea y vincula la prueba a hallazgos.
