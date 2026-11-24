# Instrucciones para la IA: preparar las pruebas de `pruebas/`

Para una IA que corre en este proyecto (`/data/www/iurix/watson/proyectos/margarita`). Respetar `CLAUDE.md` y `/data/www/iurix/sherlock/formato-md-watson.md`. Sherlock lee estos `.md` y no los corrige: si algo no cumple, se arregla acá.

## Qué hay que hacer

Para cada prueba de `pruebas/` (video, audio, imagen, documento o testimonio) dejar un `.md` junto al original con: datos técnicos y el contenido según su tipo (ver «Según el tipo de prueba»). Las carpetas de año (`2018/`, `2020/`, `2021/`, `2022/`) ya están organizadas: no tocarlas salvo para completar un `.md` que falte. Todo lo que está fuera de una carpeta de año (`videos/`, `audios/`, `imagenes/`, `escritos/`, `siquiatria/`, `testimonios/`, `otros-casos-de-mala-praxis/`, etc.) hay que moverlo a su `AAAA/mm/dd/`. Los videos del 25/03/2020 son `20200325_104613`, `104751`, `105605`, `105831`, `112310`, `122731`, `123255` y `123506`. Empezar por `123506`, `123255` y `122731`.

## Reglas

- Nunca sobreescribir un `.md` que ya existe: puede estar editado a mano. Si existe, completarlo solo si falta una sección y avisar qué agregaste.
- No hacer commits ni push. El usuario revisa con git y comitea a mano.
- No inventar. Si no se ve o no se oye, `[ilegible]` o `[dudoso: texto]`.
- Nada editorial, opinión ni conclusiones dentro del `.md`. Lo que se ve y se oye, nada más.
- No identificar personas por la cara. No atribuir voces: lo hace el usuario.
- No repetir la transcripción de audio si el `.md` ya la tiene.

## Nombres y ubicación

`pruebas/` es un repo git propio: las rutas de abajo son relativas a su raíz. Sherlock lo lee como `expedientes/pruebas/`.

- Cada prueba va en `AAAA/mm/dd/` según su fecha de grabación, sin importar el tipo (video, audio, imagen u otro): no hay carpetas por tipo. La fecha va en la ruta y no se repite en un prefijo. Si un archivo todavía está en una carpeta que no es de año, moverlo a la carpeta de su día (con `git mv` si ya está versionado), junto con su `.md`, sus fotogramas y cualquier archivo derivado. Los `informe-*.md` y otros índices que citen rutas viejas se actualizan con la ruta nueva.
- Fecha para ubicar: grabación (video, audio, foto); si no hay, la del documento (emisión, firma, redacción o testimonio); si no, la del nombre del archivo o los metadatos. Si no se puede determinar con certeza, no mover el archivo y listarlo al usuario como «sin fecha». Nunca inventar una fecha.
- No mover: scripts y material de trabajo que no es prueba (p. ej. `.sh`, salidas de OCR como `siquiatria_ocr/`); listarlos al usuario.
- Si el destino ya tiene un archivo con ese nombre, no sobreescribir: avisar.
- El original y su `.md` van juntos y con el mismo nombre base, el original sin renombrar: `2020/03/25/20200325_123506.mp4` y `2020/03/25/20200325_123506.md`. Para audios, el nombre del original (`WhatsApp Ptt 2021-06-24 at 14.47.41.ogg` + `.md`). El nombre del original lleva la hora de inicio y los informes lo citan.
- Fotogramas (solo si el usuario los pidió guardar): `AAAA/mm/dd/fotogramas_HHMMSS/NNN.jpg`, uno cada 3 segundos, 800 px de ancho, `NNN` = segundo de inicio con tres cifras. `HHMMSS` es la hora de inicio del video.
- El `sha256` del original va siempre en el encabezado. Los mp4 pesan cientos de MB: que se suban o no al repo lo decide el usuario (un mp4 de más de 100 MB no entra a GitHub sin LFS). Si no se suben, el `.md` los describe igual por su hash.

## Encabezado del `.md`

Entre `---`, una clave por línea, valores entre comillas.

| Clave | Contenido |
|---|---|
| `fecha` | `AAAA-MM-DD` de la grabación o, en documentos, de emisión/redacción |
| `expediente` | `MPF00462972` |
| `tipo` | `otro` (o `documento`, `testimonio`, `imagen` si el usuario ya usa esos valores en otros `.md`) |
| `origen` | `propio` (o el que corresponda: tercero, institución; si no se sabe, preguntar) |
| `archivo` | nombre real del original |
| `sha256` | hash del original |
| `inicio` | solo audio y video: `AAAA-MM-DD HH:MM:SS` hora local (del nombre del archivo) |
| `fin` | solo audio y video: `AAAA-MM-DD HH:MM:SS` (`creation_time` de ffprobe; en estos teléfonos es el fin de la grabación) |
| `duracion_s` | solo audio y video: segundos |
| `transcripcion` | solo audio, video y documentos transcriptos: herramienta y parámetros (p. ej. `faster-whisper large-v3, VAD, es`) |
| `revision_ia` | modelo y nivel de cada lectura de imagen o documento, separados por `;` (p. ej. `claude-opus-5-5-high (fotogramas); gemini-… (agy, video)`) |
| `intenta_probar` | qué hecho o circunstancia se busca acreditar con esta prueba |
| `comentario` | notas, observaciones o contexto del denunciante / investigador |
| `nota_transcripcion` | solo si hay dudas generales |

Los datos de los `.md` actuales (`md5`, `inspeccion`) pueden quedar; no borrarlos. No hay informes globales por carpeta: todo va en el `.md` de cada prueba, y la clave `informe` se quita si apunta a uno.

## Según el tipo de prueba

- **Video:** cuerpo completo de abajo (transcripción del audio + lectura de imagen por fotogramas) y, si el usuario lo autoriza, la segunda lectura con agy.
- **Audio:** solo `Transcripción del audio:` (punto 1 del cuerpo). Sin lectura de imagen ni fotogramas.
- **Imagen (foto, captura):** `Imagen (lectura de IA):` con una sola descripción, sin marca de segundos (`- Imagen: …`), con las mismas reglas del punto 2. Todo texto visible se transcribe literal; lo ilegible, `[ilegible]`. Sin `inicio`, `fin`, `duracion_s` ni `transcripcion`.
- **Documento (PDF, escrito, nota, captura de texto):** `Texto del documento:` con la transcripción literal, respetando páginas (`- Pág. 1: …`); lo que no se lee, `[ilegible]`; sellos, firmas, tachaduras y anotaciones a mano se describen donde aparecen. Un PDF con texto se extrae; uno escaneado se lee de la imagen. Sin resumen ni interpretación.
- **Testimonio (`.md` ya redactado por el usuario):** no tiene original aparte. No reescribirlo ni agregarle lectura de IA; solo moverlo a su fecha y completar el encabezado si falta, con `origen` según corresponda. Preguntar si falta la fecha.
- **Otros (video/imagen de otros casos, etc.):** el mismo tratamiento que su tipo; en `origen` indicar que no es del caso propio y, si no está claro, preguntar.

Las reglas generales de arriba (no sobreescribir, no inventar, no opinar, no identificar personas por la cara) valen para todos los tipos.

## Cuerpo, en este orden (video; audio, imagen y documento usan solo la parte que les toca)

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

## Informes globales (`informe-*.md`)

No debe haber informes por carpeta. Si existe uno (`videos/informe-videos.md`, etc.):

1. Pasar a cada `.md` individual lo que solo esté en el informe (método en `transcripcion`/`revision_ia`/`nota_transcripcion`, y `md5`, `inicio`, `fin`, `duracion_s` de la tabla), sin pisar lo que el `.md` ya tenga. Quitar la clave `informe` de los `.md` que apuntan a él.
2. Los originales que figuran en el informe pero no tienen `.md`: hacerles el proceso completo de «Pasos». La tabla del informe sirve para contrastar los datos técnicos, no reemplaza el cálculo.
3. Los que figuran en el informe pero ya no están en disco (el usuario los quitó): ignorarlos, sin crear nada.
4. Cuando todos los `.md` quedaron completos, borrar el informe y avisar al usuario.

## Pasos

0. Si el archivo está fuera de `AAAA/mm/dd/`, determinar su fecha y moverlo (ver «Nombres y ubicación»). Calcular `sha256`.
1. Solo audio y video: `ffprobe` para duración y `creation_time`.
2. Solo video: si el original trae etiqueta de rotación (`ffprobe` muestra `rotation`) y los fotogramas salen de costado, extraerlos con `-noautorotate`. No crear ni guardar copias rotadas del video.
3. Solo video: `ffmpeg -i ARCHIVO -vf "fps=1/3,scale=800:-1" …/NNN.jpg` en un directorio temporal fuera del proyecto.
4. Video: leer cada fotograma y escribir su línea de `Imagen (lectura de IA)`. Audio: transcribir. Imagen y documento: leerlos según «Según el tipo de prueba».
4b. Solo video: si el usuario autorizó `agy`, hacer la segunda lectura (sección de arriba) y completar `Imagen (lectura de IA, agy)` y `Diferencias entre lecturas`.
5. Armar el `.md` solo si no existe (reglas arriba).
6. Validar con `/data/www/iurix/sherlock/bin/formato` y corregir.
7. Informar al usuario: qué archivos moviste, cuáles no pudiste ubicar por falta de fecha, qué archivos escribiste, cuáles saltaste por existir y qué quedó dudoso. No comitear.

## Después (lo hace el usuario)

Comitea al repo `pruebas`, lo baja en Sherlock (`bajar` o desde la web), y desde la web asigna quién habla en cada línea y vincula la prueba a hallazgos.
