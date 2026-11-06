# Conquista cada compás — notas para Claude

Léelo entero antes de tocar nada. Resume lo decidido con Gonzalo (el usuario) en el primer chat.

## Con quién trabajas

- **Gonzalo**, padre de **Darío** y **Gabriel**, que estudian batería con su profesor **Pedro**.
- **No sabe de música ni de programación.** Escríbele siempre en **español**, claro y sin jerga. Si usas un término musical (BPM, compás, tresillo…), explícalo.
- **Paso a paso, de uno en uno.** Lo pidió así expresamente: no le des cinco pasos de golpe cuando tiene que hacer algo en GitHub u otra web. Un paso, espera su captura, el siguiente.
- Suele mandar **capturas de pantalla**. Míralas con atención antes de responder.
- Antes de cambios grandes, explica la idea en pocas líneas y pregunta. Para cambios pequeños que pide claramente, hazlos directamente.
- Cuando algo no se pueda probar desde aquí (YouTube está bloqueado en el contenedor), díselo y pídele que lo compruebe.

## Qué es la aplicación

Una página web (un solo archivo `index.html`, sin servidor ni librerías externas) para que los niños ensayen batería con los vídeos de **Jack Young** en YouTube:

- **Mis canciones** es el primer panel de la columna derecha y lo integra todo. Pestañas «Canciones guardadas» y «Otros ensayos» (historial). Al desplegar una canción (que carga el vídeo en pausa en 0:00) aparece debajo, en pequeño, **«▶ Empezar canción» + velocidad** (1×, 0,95×, 0,9×, 0,85×, 0,8×, 0,75×, 0,7×; Gonzalo comprobó que YouTube acepta las intermedias). Debajo, «Compases guardados» y la lista. Al pinchar en un compás aparece bajo él **«▶ Empezar ensayo» + velocidad**. Los botones son elementos fijos (`#songCtl`, `#fragCtl`) que `renderLibrary()` aparca en `#boardPanel` (oculto) y vuelve a colocar.
- **Ensayar un compás**: repite la parte marcada **10 veces**, con **cuenta de 3 clics** antes de cada vuelta (100 BPM fijos ajustados a la velocidad) y 1 s de pausa. Sin bolas de progreso: el aviso grande sobre el vídeo dice solo «Repetición X de 10» (nada de porcentajes) y al terminar muestra «¡Ensayo completado!» unos segundos.
- **Empezar siempre igual (pensado para que ensayen solos)**: «Empezar canción», «Empezar ensayo» o la barra espaciadora muestran la pantalla «Empezar ensayo de canción / de compás» con un play grande y el texto «Pulsa al play o a la barra espaciadora cuando estés listo para ensayar» (en el móvil: «Pulsa al play cuando estés listo para ensayar»). Al pulsar play, la barra o el mismo botón otra vez, **cuenta atrás de 5** (un número por segundo, con clic): la canción empieza tras el 5; en el compás, la 1.ª repetición tiene cuenta de 5 y las demás de 3. Si algo suena, el botón o la barra lo para. Esc cancela. En el móvil, además, pantalla completa y giro a horizontal (Android).
- Si la columna derecha se oculta o la pantalla es estrecha, «Mis canciones» y «Guardar compases» pasan debajo del vídeo.
- **Guardar compases**: marcar inicio/final (−1 s, −0,2, +0,2, +1 s), escuchar y guardar. Con un compás elegido, «Guardar» modifica ese mismo.
- **Bateristas**: menú «Baterista» arriba (junto a «Cargar canción»). Cada baterista tiene sus canciones. Lápiz para renombrar. No se recuerda el baterista elegido entre visitas (se elige cada vez).
- **Compartir bateristas y canciones**: enlace `#c=…` (JSON en base64) por WhatsApp/correo. Si el baterista no tiene canciones, el botón es «Enviar a <nombre>» (caso de Pedro creando a sus alumnos). Al abrir un enlace solo se **añade y actualiza**, nunca se borra. Si llega un baterista con el mismo nombre que uno local, se pregunta «¿Es el mismo?» (unir o añadir como «Nombre (2)»); la respuesta se recuerda con `aka`. Compases con el mismo inicio y final (±0,05 s) se unen en uno.
- **Sincronizar mis dispositivos**: sube `canciones.json` al repositorio con la API de GitHub usando una **llave** (token *fine-grained*, Resource owner = la organización, permiso Contents: Read and write) que se guarda solo en el navegador. Los dispositivos «de la familia» (con llave o que han abierto el enlace de dispositivos `#Dario-y-Gabriel`) cargan esa lista al abrir. Aquí sí viajan los borrados (`deleted`).
- Pantalla completa propia, portada (`portada.webp`) y YouTube en modo `youtube-nocookie.com`. Tipos de letra incrustados en el HTML (no se piden a Google).

### Diseño

- Verde de marca `--accent: #00d49e`; magenta `--done: #ff0b5e` para el menú de bateristas, los botones «Parar» y los **números de la cuenta atrás** (sobre el vídeo cubierto de blanco casi opaco, al 88 %, como el fondo blanco de las partituras; y **todo lo que sale en la cuenta atrás en magenta**: números, «Repetición X de 10», «Empieza la canción» y «¡Ensayo completado!»). Gonzalo quiere que la cuenta de canciones y la de partituras se parezcan. Tema claro y oscuro con tokens en `:root`.
- Los botones rellenos de verde se ponen **magenta al pasar el ratón** por encima.
- Tipos: Bricolage Grotesque (títulos), Atkinson Hyperlegible (texto), JetBrains Mono (números).
- Columna derecha con «Mis canciones» arriba; debe caber todo sin hacer scroll en un portátil (~1900×920). Gonzalo cuida mucho el espacio y la alineación: compruébalo con capturas de Playwright antes de dar algo por terminado.

## Dónde está publicada

- Repositorio: **`CONQUISTA-CADA-COMPAS/conquista-cada-compas.github.io`** (organización de GitHub; se movió desde `gruizdelatorre/conquista-cada-compas`).
- Dirección: **https://conquista-cada-compas.github.io/** (GitHub Pages desde `main`; tarda uno o dos minutos en actualizarse; Ctrl + F5 si no se ve).
- Se dejó **Netlify** porque cada subida gastaba créditos del plan gratuito. No volver a proponerlo.
- Claude sube los cambios directamente (commit y push a `main`). Gonzalo ya no descarga ni sube archivos a mano.
- **Si Gonzalo dice «no carga»** tras una subida: GitHub Pages se ha atascado. Se arregla con un commit vacío «Reintentar la publicación de la página» (`git commit --allow-empty`) y push; tarda 2–3 minutos.
- `canciones.json` lo escribe también la propia app (commits «Sincronizar canciones desde la app»): **haz siempre `git pull` / rebase antes de hacer push** y no lo sobrescribas.

## Datos guardados (no romper)

- `localStorage["ensayo-en-bucle-v1"]`: toda la biblioteca. **No cambies el nombre de la clave** o se pierden las canciones.
- `localStorage["ensayo-en-bucle-github-key"]`: la llave de GitHub.
- Formato: `{ drummers:[{id,name,updated,aka?}], songs:[{id,videoId,title,drummer,updated,saved?,lastUsed?,fragments:[{id,name,start,end,updated,aka?}]}], deleted:{ "drummer:<id>"|"song:<drummer>:<videoId>"|<fragmentId>: timestamp } }`. Al unir listas gana la versión con `updated` más reciente.

## Cómo probar

- No hay YouTube en el contenedor: se prueba con Playwright sustituyendo `https://www.youtube.com/iframe_api` por un reproductor simulado (con `seekTo` que arranca la reproducción si el vídeo está «preparado», como hace YouTube).
- Dos personas distintas = dos contextos de navegador distintos. (A Gonzalo: dos ventanas de incógnito del mismo navegador comparten memoria; para simular a Pedro y a un padre, usar dos navegadores diferentes.)

## Siguiente proyecto: «Mis partituras» (en marcha)

Decidido con Gonzalo en el chat de partituras:

- **Dónde**: dentro de la app, en un panel **«Mis partituras» debajo de «Guardar compases»**.
- **Por líneas, siempre**: cada fila de pentagrama es una **«línea»** (el término técnico es «sistema»; no usarlo). Al desplegar una partitura salen casillas «Línea 1, Línea 2…» y «Partitura completa». Aunque una línea tenga varios ejercicios numerados (p. ej. 4 por línea), se elige la línea entera.
- **Animación: tira continua** (estilo fácil de Jack Young): las líneas marcadas se ponen una tras otra y se repiten **10 veces** sin pausa; la tira se mueve a la izquierda y la barra que marca el golpe se queda quieta. Misma pantalla de play, cuenta atrás y «Repetición X de 10» que en «Ensayar un compás».
- **Pasar a limpio** (le encantó la idea): Gonzalo manda la hoja al chat, Claude la lee nota a nota y la guarda en formato digital; la app la dibuja siempre con los mismos símbolos, la toca con sonidos de batería y resalta cada nota «un pelín» (Pedro) al pasar la barra. Errores posibles en hojas borrosas: revisar escuchando.
- **Metrónomo con macro y micro pulso** (Pedro): clic fuerte en cada tiempo (más fuerte en el primero del compás) y clic suave en la subdivisión (2, 3 o 4 por tiempo según la partitura). Casilla «Subdivisión».
- **Base de notación: la «Drumeo Notation Key»** (la eligió Gonzalo como referencia). Pentagrama de 5 líneas, contando desde abajo (línea 1 = la de abajo; espacio 1 = entre las líneas 1 y 2):
  - **Caja**: nota normal en el espacio 3. **Cross stick**: × en el mismo sitio que la caja.
  - **Bombo**: espacio 1. **2.º bombo**: debajo del pentagrama (bajo la línea 1).
  - **Charles cerrado**: × encima del pentagrama (espacio sobre la línea 5). **Charles abierto**: ⊗ (× dentro de un círculo) en el mismo sitio. **Charles con el pie** (y *splash* de pie): × bajo el pentagrama; el *splash* de pie, ⊗.
  - **Ride**: × sobre la línea 5. **Campana del ride**: rombo sobre la línea 5.
  - **Crash**: × sobre la línea adicional encima del pentagrama. **Choke** (cortar el plato): el crash con la marca de corte. **China**: ⊗ también por encima, con línea adicional. **Cencerro**: rombo en el espacio 4.
  - **Toms**: alto en el espacio 4, medio en la línea 4, bajo en la línea 3, **base** (*floor tom*) en el espacio 2.
  - **Nota fantasma**: la nota entre paréntesis. **Acento**: «>» encima.
  - **Doble**: una raya cruzada en la plica (dos golpes). **Redoble**: tres rayas en la plica.
  - **Crescendo / decrescendo**: horquillas debajo (abriéndose / cerrándose).
  - **Flam**: una notita pequeña justo antes. **Drag**: dos notitas pequeñas antes.
  - Lo ya dibujado en la app (charles × y ⊗ encima, caja en el espacio 3, bombo en el espacio 1) ya coincide con esta base.
- **Colores de Jack Young como opción** (apagados por defecto): charles amarillo (× cerrado, ⊗ abierto), caja roja con «S», bombo negro con «K», crash verde, ride azul, toms 1/2/3 (amarillo/azul/verde) con su número.
- **Tap**: botón que pone la velocidad tocando al ritmo; cada toque debe notarse (destello magenta) y cambiar la velocidad desde el 2.º toque.
- Botones verdes con texto blanco; casillas verdes con la marca en blanco.
- Darío y Gabriel van por **grado 3, 4 y 5**: hará falta semicorcheas, tresillos, toms, platos, acentos, notas fantasma, flams.
- **Ya en la app**. El panel `#scorePanel` (tras «Guardar compases») es **solo la lista de partituras: ahí no se configura nada**. Al pinchar en una, todo (tira de ensayo arriba, «Empezar ensayo», velocidad, Tap, casillas de sonido/metrónomo/subdivisión/colores, «Partitura completa» y las líneas con su casilla) aparece en `#scoreView`, en el sitio del vídeo y con su mismo ancho; solo hace scroll la lista de líneas. En el móvil el recuadro se alarga (`.stage.scoring`). Las partituras están en la constante `SCORES` del código (cada compás: 8 corcheas, `hh` x/o/. y `low` S/B/.), iguales para todos. Se van añadiendo **solo cuando Gonzalo las da por buenas**. Velocidad y opciones se guardan aparte en `localStorage["ensayo-partituras-v1"]`; las líneas marcadas **no se recuerdan**: al abrir una partitura sale marcada **la partitura entera** (lo pidió Gonzalo después de probar otras opciones). Con «Partitura completa» marcada, pinchar una línea deja solo esa; desmarcar «Partitura completa» deja todo sin marcar. Se puede quedar sin ninguna línea: al pulsar play sale el aviso «Marca al menos una línea…». La pantalla «Empezar ensayo» y la cuenta atrás tapan **solo la ventana de la tira**, no las líneas. Para más ritmos (semicorcheas, tresillos, toms…) habrá que ampliar el formato y el dibujo.
- Gonzalo solo tiene **7 hojas** (las del chat): «2 bar drum beats» (hecha), «1 + 1», «2 + 1 = Drums», «Solo Easy Batería», página 154 (32 ejercicios), «Snare/Bass drum linear exercises» y el recorte de tresillos (compases 24–25).
- **Prueba anterior** (fuera de la app): página publicada https://claude.ai/artifact/WtpoDeYSAWoNP1dkXMNpRq con «2 bar drum beats» pasada a limpio (solo corcheas). Siguiente paso: pasar a limpio una partitura real de grado 3–5 y, si sale bien, meterlo en la app.
- La detección automática de líneas en la imagen funciona bien en escaneos (6 de 7 hojas); la de rayas de compás es poco fiable. Si se usa la imagen original, dejar corregir con un toque.
