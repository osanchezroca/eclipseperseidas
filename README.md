# Eclipsi + Perseides · Sabadell

**→ [osanchezroca.github.io/eclipseperseidas](https://osanchezroca.github.io/eclipseperseidas/)**

Guía web "en directo" para vivir el **eclipse solar del 12 de agosto de 2026** y las **Perseidas de esa misma noche** desde Sabadell. Hecha para gente de aquí y para gente que no sabe nada de astronomía: te dice qué toca ahora, hacia dónde mirar y qué no hacer.

Toda la información buscada estaba en formato astrónomo — azimut 286°, altura 4°, magnitud. Aquí las direcciones son el paisaje que se ve desde la ciudad: encima de Montserrat, el arco entre La Mola y el Montseny.

**En català por defecto**, castellano a un toque con el selector `CA · ES`.

## Qué hace

- **Línea de tiempo que se mueve sola**, de las 18:00 a las 04:00: marca el momento actual, tacha lo pasado y anuncia lo siguiente. Al abrir la página se coloca en la tarjeta de ahora, sin scroll ni botones.
- **Brújula HUD fija arriba.** Cinta de rumbos con el norte, los cardinales y los puntos del paisaje; el borde superior del móvil es el puntero. El objetivo cambia con la fase: el Sol sobre Montserrat durante el eclipse, el arco de las fugaces por la noche, y modo "ve hacia allí" mientras te desplazas.
- **Vibración como guía.** Un toque por cada marca de 5° al barrer, pulsos cada vez más seguidos al acercarte al objetivo y una ráfaga distinta al clavarlo. Se puede usar sin mirar la pantalla. Calla si el móvil no se mueve.
- **Esquemas dibujados** en el momento en que hacen falta: desde la grada del anfiteatro con el Sol sobre la silueta de Montserrat, y en el campo estirado con los pies hacia el Montseny.
- **Pictogramas de qué mirar sin gafas** en el máximo: luz gris, sombras afiladas, medias lunas en el suelo, Venus.
- **Modo noche.** A partir de las 22:30 todo lo que no es "ahora" se apaga y se desatura para no molestar la vista adaptada; el ratón o un toque lo devuelve a color para repasar lo que viene.
- **Aviso flotante por fase** bajo la brújula. Mientras el Sol está fuera, la regla de las gafas; luego "móvil guardado 30 min" y "mirada relajada".
- **Cielo animado** de fondo: el color real de cada hora, el oscurecimiento del máximo con Venus y Júpiter, Casiopea, el radiante de Perseo y lluvia de meteoros.
- **Slider de previsualización** para recorrer las diez horas sin esperar a la noche.
- **Instalable** en la pantalla de inicio y con vista previa cuidada al compartir el enlace.

## Seguridad

Gafas certificadas **ISO 12312-2** siempre que se mire al Sol. En Sabadell el eclipse es casi total (~99%) pero **no hay totalidad**: no existe ningún momento seguro sin gafas, y las de sol no valen. El aviso está fijo en pantalla toda la tarde y no se puede cerrar.

## Lo que a 99% no se ve

Es la mitad del valor de la guía, porque mucha gente lo busca y se pierde el momento: **no hay corona, ni anillo de diamante, ni perlas de Baily**. Eso solo ocurre dentro de la franja de totalidad, que pasa por el sur de Lleida y Tarragona. El 1% de Sol que queda sigue siendo unas mil veces más brillante que la corona.

Sí se ve: la luz volviéndose gris (sin llegar a noche), las sombras más afiladas, medias lunas proyectadas por las hojas y **Venus** (magnitud −4,4) como lo más fácil a simple vista. El Sol está a 2–4° del horizonte, así que hace falta horizonte oeste limpio.

## Cómo está hecha

Un solo `index.html` estático: sin build, sin dependencias, sin frameworks. Cuatro `<script>` independientes (idioma, timeline, cielo canvas, brújula) que se comunican por `window.__nightMin`, `window.T()` y `window.__buzz()`.

- **Brújula:** `DeviceOrientationEvent` (`webkitCompassHeading` en iOS, `deviceorientationabsolute` en Android). **Requiere https**, así que funciona en el deploy y no en `file://`.
- **Vibración:** `navigator.vibrate`. Solo Android/Chrome — iOS no expone vibración web — y el navegador la bloquea hasta el primer toque en la página, así que hay un aviso que lo desbloquea y desaparece.
- **Idiomas:** el HTML está escrito en catalán y cada elemento traducible lleva su `data-es`. Los textos dinámicos viven en un diccionario. Para añadir texto: escríbelo en catalán y añade el `data-es`.
- **Sin service worker** a propósito: nada que invalidar en cada deploy. Sin cobertura en el campo, mejor abrirla antes de salir.

Ficheros: `index.html` (toda la app), `manifest.webmanifest` + `icon.svg` (instalable), `og.jpg` (vista previa al compartir), `CLAUDE.md` (contexto y decisiones del proyecto).

Desplegada con **GitHub Pages** desde `main`. Se prueba en móvil por https y con el slider de previsualización, que cubre las diez horas sin esperar a la noche.

## Datos del evento

| | |
|---|---|
| Inicio del eclipse | 19:35 CEST |
| Máximo (~99%) | 20:29 · Sol a ~4° de altura, sobre la silueta de Montserrat |
| Fin del eclipse | 20:54 |
| Perseidas | pico la noche del 12 al 13, con luna nueva · mejor ventana 01:00–03:00 |

Para el eclipse sirve cualquier sitio con el **horizonte oeste despejado** (la guía usa el anfiteatro del Parc Catalunya como referencia). Para las fugaces, cualquier campo a las afueras y de espaldas a las luces de la ciudad.

---

Hecho por [Oscar Sánchez](https://www.linkedin.com/in/osanchezroca/) con Claude. Código libre ([Unlicense](LICENSE)): cógelo y adáptalo a tu ciudad.
