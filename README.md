# Eclipse + Perseidas · Sabadell

Guía web "en directo" para vivir el **eclipse solar del 12 de agosto de 2026** y las **Perseidas de esa misma noche**, hecha **para gente de Sabadell**: horarios en hora local, y las direcciones siempre referidas al paisaje que se ve desde aquí (Montserrat, La Mola, el Montseny) en vez de coordenadas o grados sueltos.

No hace falta saber nada de astronomía: la página te dice qué toca ahora, hacia dónde mirar y qué no hacer. **En català per defecte**, con castellano a un toque.

## Qué incluye

- **Timeline en directo** desde las 18:00 hasta las 04:00: marca el momento actual, tacha lo que ya pasó y avisa de lo siguiente. De noche lo que no es "ahora" se apaga y se desatura para no molestar la vista adaptada (pasa el ratón o toca una tarjeta para verla entera).
- **Brújula HUD fija arriba**: cinta de rumbos con el norte, los cardinales y los puntos del paisaje. El borde superior del móvil es el puntero. Vibra con detentes al barrer, con pulsos cada vez más seguidos al acercarse al objetivo y con una confirmación al fijarlo.
- **Objetivos por fase**: el Sol eclipsado sobre Montserrat (~286°) durante la tarde; el arco entre La Mola y el Montseny (~13°) para las fugaces.
- **Consejo flotante** bajo la brújula, distinto en cada momento. Mientras el Sol está fuera manda la regla de seguridad.
- **Cielo animado** de fondo: color real de cada hora, oscurecimiento en el máximo del eclipse (con Venus y Júpiter), Casiopea, el radiante de Perseo y lluvia de meteoros.
- **Esquema dibujado** del momento máximo: desde tu sitio, por encima del escenario, el Sol sobre la silueta de Montserrat. Y pictogramas de qué mirar sin gafas (luz gris, sombras afiladas, medias lunas en el suelo, Venus).
- **Català por defecto** y castellano con el selector `CA · ES` de la barra inferior.
- **Slider de previsualización** para recorrer las 10 horas sin esperar a la noche.

## Seguridad

Gafas certificadas **ISO 12312-2** siempre que se mire al Sol. En Sabadell el eclipse es casi total (~99%) pero **no hay totalidad**, así que no existe ningún momento seguro sin gafas. Las gafas de sol no valen.

## Cómo funciona

Un `index.html` estático: sin build, sin dependencias, sin frameworks. Al lado, `manifest.webmanifest` + `icon.svg` para poder **instalarla en la pantalla de inicio** y abrirla a pantalla completa. La brújula usa `DeviceOrientationEvent` y la vibración `navigator.vibrate`, así que **hace falta abrirla por https desde el móvil** (en iOS hay que pulsar "Activar sensores"; iOS no vibra porque Safari no expone vibración web).

Desplegado en Vercel (framework "Other", sin build). No hay service worker: sin cobertura en el campo, mejor abrirla antes de salir.

## Datos del evento

| | |
|---|---|
| Inicio del eclipse | 19:35 CEST |
| Máximo (~99%) | 20:29 · Sol a ~4° de altura, azimut ~286° sobre Montserrat |
| Fin del eclipse | 20:54 |
| Perseidas | pico la noche del 12 al 13, Luna nueva · mejor ventana 01:00–03:00 |

Para el eclipse sirve cualquier sitio con el **horizonte oeste despejado** (la guía usa el anfiteatro del Parc Catalunya como referencia). Para las fugaces, cualquier campo a las afueras y de espaldas a las luces de la ciudad.

---

Hecho por [Oscar](https://www.linkedin.com/in/osanchezroca/) + Claude.
