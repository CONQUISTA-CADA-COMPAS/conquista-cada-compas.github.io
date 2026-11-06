# Conquista cada compás — notas para Claude

Léelo entero antes de tocar nada. Resume lo decidido con Gonzalo (el usuario) en el primer chat.

## Con quién trabajas

- **Gonzalo**, padre de **Dari** y **Gabi**, que estudian batería con su profesor **Pedro**.
- **No sabe de música ni de programación.** Escríbele siempre en **español**, claro y sin jerga. Si usas un término musical (BPM, compás, tresillo…), explícalo.
- **Paso a paso, de uno en uno.** Lo pidió así expresamente: no le des cinco pasos de golpe cuando tiene que hacer algo en GitHub u otra web. Un paso, espera su captura, el siguiente.
- Suele mandar **capturas de pantalla**. Míralas con atención antes de responder.
- Antes de cambios grandes, explica la idea en pocas líneas y pregunta. Para cambios pequeños que pide claramente, hazlos directamente.
- Cuando algo no se pueda probar desde aquí (YouTube está bloqueado en el contenedor), díselo y pídele que lo compruebe.

## Qué es la aplicación

Una página web (un solo archivo `index.html`, sin servidor ni librerías externas) para que los niños ensayen batería con los vídeos de **Jack Young** en YouTube:

- **Mis canciones** es el primer panel de la columna derecha y lo integra todo. Pestañas «Canciones guardadas» y «Otros ensayos» (historial). Al desplegar una canción (que carga el vídeo en pausa en 0:00) aparece debajo, en pequeño, **«▶ Empezar canción» + velocidad** (1×, 0,95×, 0,9×, 0,85×, 0,8×, 0,75×, 0,7×; Gonzalo comprobó que YouTube acepta las intermedias). Debajo, «Compases guardados» y la lista. Al pinchar en un compás aparece bajo él **«▶ Empezar ensayo» + velocidad**. Los botones son elementos fijos (`#songCtl`, `#fragCtl`) que `renderLibrary()` aparca en `#boardPanel` (oculto) y vuelve a colocar.
- **Ensayar un compás**: repite la parte marcada **10 veces**, con **cuenta de 3 clics** antes de cada vuelta (100 BPM fijos ajustados a la velocidad) y 1 s de pausa. Sin bolas de progreso: el aviso grande sobre el vídeo dice solo «Repetición X de 10» (nada de porcentajes) y al terminar muestra «¡Ensayo completado!» unos segundos.
- **En el móvil o tablet (pantalla táctil)**, «Empezar canción» y «Empezar ensayo» ponen el vídeo a pantalla completa y muestran «Empezar ensayo de canción / de compás» con un play grande: no empieza nada hasta pulsarlo (para que les dé tiempo a colocar el móvil). En el ordenador empieza directamente.
- Si la columna derecha se oculta o la pantalla es estrecha, «Mis canciones» y «Guardar compases» pasan debajo del vídeo.
- **Guardar compases**: marcar inicio/final (−1 s, −0,2, +0,2, +1 s), escuchar y guardar. Con un compás elegido, «Guardar» modifica ese mismo.
- **Bateristas**: menú «Baterista» arriba (junto a «Cargar canción»). Cada baterista tiene sus canciones. Lápiz para renombrar. No se recuerda el baterista elegido entre visitas (se elige cada vez).
- **Compartir bateristas y canciones**: enlace `#c=…` (JSON en base64) por WhatsApp/correo. Si el baterista no tiene canciones, el botón es «Enviar a <nombre>» (caso de Pedro creando a sus alumnos). Al abrir un enlace solo se **añade y actualiza**, nunca se borra. Si llega un baterista con el mismo nombre que uno local, se pregunta «¿Es el mismo?» (unir o añadir como «Nombre (2)»); la respuesta se recuerda con `aka`. Compases con el mismo inicio y final (±0,05 s) se unen en uno.
- **Sincronizar mis dispositivos**: sube `canciones.json` al repositorio con la API de GitHub usando una **llave** (token *fine-grained*, Resource owner = la organización, permiso Contents: Read and write) que se guarda solo en el navegador. Los dispositivos «de la familia» (con llave o que han abierto el enlace de dispositivos `#Dari-y-Gabi`) cargan esa lista al abrir. Aquí sí viajan los borrados (`deleted`).
- Pantalla completa propia, portada (`portada.webp`) y YouTube en modo `youtube-nocookie.com`. Tipos de letra incrustados en el HTML (no se piden a Google).

### Diseño

- Verde de marca `--accent: #00d49e`; magenta `--done: #ff0b5e` para el menú de bateristas y los botones «Parar». Tema claro y oscuro con tokens en `:root`.
- Tipos: Bricolage Grotesque (títulos), Atkinson Hyperlegible (texto), JetBrains Mono (números).
- Columna derecha con «Mis canciones» arriba; debe caber todo sin hacer scroll en un portátil (~1900×920). Gonzalo cuida mucho el espacio y la alineación: compruébalo con capturas de Playwright antes de dar algo por terminado.

## Dónde está publicada

- Repositorio: **`CONQUISTA-CADA-COMPAS/conquista-cada-compas.github.io`** (organización de GitHub; se movió desde `CONQUISTA-CADA-COMPAS/conquista-cada-compas`).
- Dirección: **https://conquista-cada-compas.github.io/** (GitHub Pages desde `main`; tarda uno o dos minutos en actualizarse; Ctrl + F5 si no se ve).
- Se dejó **Netlify** porque cada subida gastaba créditos del plan gratuito. No volver a proponerlo.
- Claude sube los cambios directamente (commit y push a `main`). Gonzalo ya no descarga ni sube archivos a mano.
- `canciones.json` lo escribe también la propia app (commits «Sincronizar canciones desde la app»): **haz siempre `git pull` / rebase antes de hacer push** y no lo sobrescribas.

## Datos guardados (no romper)

- `localStorage["ensayo-en-bucle-v1"]`: toda la biblioteca. **No cambies el nombre de la clave** o se pierden las canciones.
- `localStorage["ensayo-en-bucle-github-key"]`: la llave de GitHub.
- Formato: `{ drummers:[{id,name,updated,aka?}], songs:[{id,videoId,title,drummer,updated,saved?,lastUsed?,fragments:[{id,name,start,end,updated,aka?}]}], deleted:{ "drummer:<id>"|"song:<drummer>:<videoId>"|<fragmentId>: timestamp } }`. Al unir listas gana la versión con `updated` más reciente.

## Cómo probar

- No hay YouTube en el contenedor: se prueba con Playwright sustituyendo `https://www.youtube.com/iframe_api` por un reproductor simulado (con `seekTo` que arranca la reproducción si el vídeo está «preparado», como hace YouTube).
- Dos personas distintas = dos contextos de navegador distintos. (A Gonzalo: dos ventanas de incógnito del mismo navegador comparten memoria; para simular a Pedro y a un padre, usar dos navegadores diferentes.)

## Siguiente proyecto: «Mis partituras»

Pedro manda partituras en papel que Gonzalo escanea. Idea acordada, **dentro de la misma app**, en un apartado **«Mis partituras»** debajo de los paneles de compartir:

1. Subir la imagen de la partitura (por baterista, compartible y sincronizable como las canciones).
2. Detectar automáticamente las **líneas divisorias de compás** (rayas verticales del pentagrama) y dejar que Gonzalo corrija con un clic. **No** intentar leer cada nota con IA (no es fiable para batería).
3. Velocidad en BPM (muchas partituras la indican arriba, p. ej. «♩ = 80»; si no, preguntar a Pedro; para principiantes, 60–80), botón **Tap**, y compás de 4 tiempos por defecto.
4. Animación, **empezando por el estilo de Jack Young en canciones difíciles**: una barra vertical recorre cada fila hacia la derecha, baja a la siguiente, y cuando llega abajo suben las filas nuevas. Más adelante, el estilo fácil: barra quieta y partitura desplazándose a la izquierda (recortando las filas y poniéndolas en una tira).
5. Metrónomo con clics, cuenta previa, repeticiones y velocidad más lenta/rápida, igual que «Ensayar un compás».

Empezar pidiéndole una o dos partituras escaneadas reales para probar.
