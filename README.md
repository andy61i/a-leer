# ¡A leer!

Juego local para aprender a leer en español, pensado para niños pequeños.
Método silábico: la palabra aparece dividida en sílabas de colores, el botón
**Pista** revela el dibujo por trozos (puzzle), y **¡Lo leyó!** da un sonido de
premio, una estrella y confeti. Incluye álbum de pegatinas, repaso de vocales,
4 niveles (animales, comida, colores, transporte, objetos) y guarda el progreso
en el navegador.

Es un único archivo `index.html` + la carpeta `images/`. Funciona sin internet
y sin dependencias externas.

## Uso

Abre `index.html` en un navegador, o visita la versión publicada (GitHub Pages).

El nombre del saludo se puede personalizar con un parámetro en la URL:
`?name=Uliana` → muestra «¡Hola, Uliana!». Sin parámetro: «¡Hola!».

## Imágenes

Las ilustraciones están en `images/<palabra>.png`. Si falta alguna, la app
muestra un emoji como respaldo. Ver `images/COMO_AGREGAR_IMAGENES.txt`.
