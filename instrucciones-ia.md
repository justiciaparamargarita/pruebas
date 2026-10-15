# Instrucciones para la IA: preparar las pruebas de `pruebas/`

Para una IA que corre en este proyecto (`/data/www/iurix/watson/proyectos/margarita`). Respetar `CLAUDE.md` y `/data/www/iurix/sherlock/formato-md-watson.md`. Sherlock lee estos `.md` y no los corrige: si algo no cumple, se arregla acá.

## Qué hay que hacer

Para cada prueba de `pruebas/` (hoy los videos del 25/03/2020; después audios y fotos) dejar un `.md` junto al original con: datos técnicos, transcripción del audio y, en los videos, lectura de las imágenes. Los videos de ese día son `20200325_104613`, `104751`, `105605`, `105831`, `112310`, `122731`, `123255` y `123506`. Empezar por `123506`, `123255` y `122731`.

## Reglas

- Nunca sobreescribir un `.md` que ya existe: puede estar editado a mano. Si existe, completarlo solo si falta una sección y avisar qué agregaste.
- No hacer commits ni push. El usuario revisa con git y comitea a mano.
- No inventar. Si no se ve o no se oye, `[ilegible]` o `[dudoso: texto]`.
- Nada editorial, opinión ni conclusiones dentro del `.md`. Lo que se ve y se oye, nada más.
- No identificar personas por la cara. No atribuir voces: lo hace el usuario.
- No repetir la transcripción de audio si el `.md` ya la tiene.

## Nombres y ubicación

`pruebas/` es un repo git propio: las rutas de abajo son relativas a su raíz. Sherlock lo lee como `expedientes/pruebas/`.

- Cada prueba va en `videos/AAAA/mm/dd/`, `audios/AAAA/mm/dd/` o `imagenes/AAAA/mm/dd/` según su fecha de grabación. La fecha va en la ruta y no se repite en un prefijo.
- El original y su `.md` van juntos y con el mismo nombre base, el original sin renombrar: `videos/2020/03/25/20200325_123506.mp4` y `videos/2020/03/25/20200325_123506.md`. Para audios, el nombre del original (`WhatsApp Ptt 2021-06-24 at 14.47.41.ogg` + `.md`). El nombre del original lleva la hora de inicio y los informes lo citan.
- Fotogramas (solo si el usuario los pidió guardar): `videos/AAAA/mm/dd/fotogramas_HHMMSS/NNN.jpg`, uno cada 3 segundos, 800 px de ancho, `NNN` = segundo de inicio con tres cifras. `HHMMSS` es la hora de inicio del video.
- El `sha256` del original va siempre en el encabezado. Los mp4 pesan cientos de MB: que se suban o no al repo lo decide el usuario (un mp4 de más de 100 MB no entra a GitHub sin LFS). Si no se suben, el `.md` los describe igual por su hash.

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
| `revision_ia` | modelo y nivel de cada lectura de imagen, separados por `;` (p. ej. `claude-opus-5-5-high (fotogramas); gemini-… (agy, video)`) |
| `nota_transcripcion` | solo si hay dudas generales |

Los datos de los `.md` actuales (`md5`, `informe`, `inspeccion`) pueden quedar; no borrarlos.

## Cuerpo, en este orden

1. `Transcripción del audio:` una línea por tramo, `- 37–38 s: texto`. Si hay una voz en la línea, dejar `[hablante: ?]` al principio del texto: `- 37–38 s: [hablante: ?] Muchachos, ¿qué pasa?`. Solo se escribe una hipótesis (`[hablante: ¿seguridad?]`) si hay una pista visible en la imagen (uniforme, credencial, cartel); si no, `?`. Sin habla: `Transcripción del audio: sin habla reconocible.`
2. `Imagen (lectura de IA):` una línea por fotograma, `- 12 s: …`, con lo que se ve y solo eso: personas presentes (cantidad, ropa, uniforme, si portan credencial legible), carteles y su texto, camas y quién está en cada una, qué hay en el piso y sobre las camas, objetos en las manos, el cuarto o pasillo. Sin interpretar intenciones. Cartel ilegible: `[ilegible]`.
   - Para cada cama, camilla o colchón visible decir siempre si hay una persona encima o no, y qué lo cubre (sábanas, frazada, almohada, nada). Describir la superficie por lo que se ve («colchón marrón sin sábanas ni almohada, sin nadie») y no por su función («camilla»), salvo que se vea claro. Si no se ve cuál es la cama de la paciente, no suponerlo.
   - Anotar también lo que no hay cuando es relevante: personas ausentes, cartel ausente, nada en el piso. Solo si el encuadre permite verlo.

## Segunda lectura con agy (video completo)

`agy` (Antigravity, modelos Gemini) puede mirar el video entero y no solo fotogramas. Usarlo como segunda lectura independiente, sin ver la primera:

```bash
agy --mode plan --model MODELO --output-format text --print-timeout 900s \
  --print="Describí lo que se VE y se OYE en el video RUTA_ABSOLUTA, con marcas de tiempo (segundos desde el inicio). Solo hechos observables; sin interpretar intenciones ni identificar personas por la cara. Si algo no se ve o no se entiende: [ilegible] o [dudoso: …]."
```

- `MODELO`: el que el usuario indique; si no indica, preguntarlo. Anotarlo en `revision_ia`.
- Primero probar con el video más corto del día (`105831`, 10 s). Si `agy` no logra leer el archivo de video (error o respuesta que describe otra cosa), no seguir con los demás: decirlo al usuario y dejar solo la lectura por fotogramas.
- El resultado va en una sección aparte, `Imagen (lectura de IA, agy):`, con el mismo formato (`- 12 s: …`). No se mezcla con la lectura por fotogramas ni se reconcilia: si difieren, el `.md` muestra las dos y se agrega bajo `Diferencias entre lecturas:` una línea por tramo donde no coinciden (`- 40–45 s: fotogramas no muestran X; agy sí`).
- El video se manda a un servicio externo: hacerlo solo con los videos que el usuario autorizó. Si no está claro cuáles, preguntar antes.

## Pasos

1. `ffprobe` para duración y `creation_time`; calcular `sha256`.
2. Si el original trae etiqueta de rotación (`ffprobe` muestra `rotation`) y los fotogramas salen de costado, extraerlos con `-noautorotate`. No crear ni guardar copias rotadas del video.
3. `ffmpeg -i ARCHIVO -vf "fps=1/3,scale=800:-1" …/NNN.jpg` en un directorio temporal fuera del proyecto.
4. Leer cada fotograma y escribir su línea de `Imagen (lectura de IA)`.
4b. Si el usuario autorizó `agy`, hacer la segunda lectura (sección de arriba) y completar `Imagen (lectura de IA, agy)` y `Diferencias entre lecturas`.
5. Armar el `.md` solo si no existe (reglas arriba).
6. Validar con `/data/www/iurix/sherlock/bin/formato` y corregir.
7. Informar al usuario: qué archivos escribiste, cuáles saltaste por existir y qué quedó dudoso. No comitear.

## Después (lo hace el usuario)

Comitea al repo `pruebas`, lo baja en Sherlock (`bajar` o desde la web), y desde la web asigna quién habla en cada línea y vincula la prueba a hallazgos.
