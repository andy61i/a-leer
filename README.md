# ¡A leer!

Juego local para aprender a leer en español, pensado para niños pequeños.
Método silábico: la palabra aparece dividida en sílabas de colores, el botón
**Pista** revela el dibujo por trozos (puzzle), y **¡Lo leyó!** da un sonido de
premio, una estrella y confeti.

Los niveles son **escalones de dificultad**, no temas. Cada escalón enseña una
regla de lectura: primero sílabas abiertas con consonantes de un solo sonido,
después `c` y `g` fuertes, la `r` doble, los dígrafos, la `u` muda, la `h` muda,
los grupos consonánticos, los diptongos y por último las tildes. Son 15 escalones
y 111 palabras.

Cada escalón se abre con el trofeo del anterior. Al completarlo aparece una
pantalla de fiesta y el trofeo se queda en el estante. Hay también un repaso de
vocales y un modo fácil que muestra el dibujo desde el inicio.

El progreso se guarda en el navegador. Es un único archivo `index.html` + la
carpeta `images/`. Funciona sin internet y sin dependencias externas.

## Uso

Abre `index.html` en un navegador, o visita la versión publicada (GitHub Pages).

El nombre del saludo se puede personalizar con un parámetro en la URL:
`?name=Ana` → muestra «¡Hola, Ana!». Sin parámetro: «¡Hola!».

Un nivel cerrado muestra un candado. El adulto puede abrirlo igual: basta con
mantener el dedo sobre el candado en el menú.

## Imágenes

Las ilustraciones están en `images/<palabra>.webp` (también sirven `.png`,
`.jpg`, `.jpeg`). Si falta alguna, la app muestra un emoji como respaldo.
Ver `images/COMO_AGREGAR_IMAGENES.txt`.
