# CLAUDE.md — eclipse-perseidas-sabadell

Guía web "en directo" para vivir el eclipse solar del 12 de agosto de 2026 y las Perseidas de esa misma noche desde Sabadell. Un único `index.html` estático (sin build, sin dependencias, sin frameworks) desplegado en Vercel.

## Contexto del evento (datos reales, no inventar)

- **Fecha:** miércoles 12 de agosto de 2026. Eclipse solar casi total en Sabadell (~99,5% de ocultación; la franja de totalidad pasa por el sur de Lleida/Tarragona, no por el Vallès).
- **Horarios del eclipse (CEST, zona Barcelona/Sabadell):** inicio 19:35 · máximo 20:29 · fin 20:54. En el máximo el Sol está a solo ~4° de altura, azimut ~286° (O-NO).
- **Perseidas:** pico la noche del 12 al 13. Luna nueva (la misma del eclipse) → cielo óptimo. Mejor ventana: 01:00–03:00.
- **Seguridad:** gafas certificadas ISO 12312-2 obligatorias siempre; en Sabadell no hay ningún momento seguro sin gafas (no hay totalidad). No suavizar ni eliminar este aviso.

## Ubicaciones y referencias visuales (azimuts desde los puntos del usuario)

- **Punto 1 (eclipse):** anfiteatro del Parc Catalunya, Sabadell. Se mira desde la grada **hacia el escenario** (o sea al oeste); el Sol eclipsado queda un poco a la derecha y por encima. En la app las referencias son siempre "mirando al escenario", nunca coordenadas.
- **Punto 2 (Perseidas):** en la app es solo **"el campo"**. Anonimizado a propósito (decisión del usuario): no reintroducir el nombre del paraje ni coordenadas en ningún fichero. Los que van ya saben cuál es.
- **Montserrat:** azimut ~283–286° desde ambos puntos; el Sol eclipsado queda justo encima de su silueta en el máximo. Referencia del eclipse.
- **La Mola (Sant Llorenç del Munt):** azimut ~333° (N-NO), "la montaña de la ermita plana". Zona de observación de fugaces.
- **Montseny / Turó de l'Home:** azimut ~53° (NE). El radiante de las Perseidas (Perseo) sale sobre él. Los pies apuntan aquí al tumbarse.
- **Zona a evitar:** luces de Sabadell al SE.
- **Objetivos de la brújula:** eclipse → 286° ±12°; Perseidas → 13° ±40° (centro del arco La Mola–Montseny).

## Arquitectura de index.html

Todo vive en un solo archivo con tres `<script>` IIFE independientes que se comunican por `window.__nightMin` y `window.__buzz`:

1. **Motor de timeline** (primer script): reloj, marcador "aura", estados de eventos, cielo de fondo, demo slider, botón "⌖ Ahora", clase `body.night`, y `window.__buzz(patrón)` (háptica compartida).
2. **Motor de cielo canvas** (`#sky`, segundo script): estrellas, planetas del eclipse, constelaciones, lluvia de meteoros.
3. **Brújula** (tercer script): `DeviceOrientationEvent`, cinta HUD, háptica y consejo flotante.

**Dos barras fijas:** `.cpanel` (brújula) arriba y `.livebar` (reloj + estado) abajo. `.cpanel` está fuera de `.livebar` en el DOM a propósito: el `backdrop-filter` del padre creaba bloque contenedor y rompía el `position:fixed`. Un `ResizeObserver` sobre `.cpanel` mantiene `body.paddingTop` igual a su alto (cambia con el texto) para que no tape el header; `body` lleva `padding-bottom` por la barra de abajo.

### Sistema de tiempo (crítico)

- Unidad interna: **minutos desde las 18:00** del día 12. Rango 0 (18:00) → 600 (04:00).
- `nowInfo()` mapea la hora real: 18:00–23:59 → `base-1080`; 00:00–03:59 → `base+360`; **04:00–17:59 = modo pre-start** (`{min:30, pre:true}`): se precarga la fase de las 18:30 (aura en el primer evento, nada tachado, cuenta atrás "Faltan X h").
- Eventos en `data-min` (y `data-end` en el último): 18:30=30, 19:00=60, 19:35=95, 20:29=149, 20:54=174, **21:30=210 (cenar)**, 22:30=270, 23:00=300, 01:00=420 (end 540).
- El slider "Previsualizar la noche" (0–600) sustituye a la hora real cuando está activo; todo (fondo, canvas, brújula) debe responder también a él vía `window.__nightMin`.

### Aura y estados

- `--aura` (CSS var) toma el color del evento activo (`data-color`); tiñe marcador, tarjeta activa, barra sticky, botones y flecha de brújula.
- Estados: `.past` (opacidad baja + ✓ vía CSS ::after), `.active` (borde + glow + scale). El marcador `.now` se posiciona interpolando linealmente entre los `offsetTop` de los dots (`yFor(min)`).
- No reintroducir etiquetas "AHORA" textuales en tarjetas ni junto al marcador: se eliminaron por redundantes. El estado se comunica solo visualmente + barra sticky.

### Cielo de fondo (body)

- `SKY[]`: paradas `[minuto, hex]` interpoladas en RGB. Claves: azul tarde → dorado → **bajón a casi negro en 149 (máximo del eclipse)** → recuperación → púrpura atardecer → **negro puro #000 desde min 300 (23:00)** en adelante.
- Antes del inicio se clampa a min 30 (colores de las 18:30). `--night` y `--skybar` se actualizan a la vez para que dots y barra se fundan con el fondo.

### Canvas #sky (position:fixed, z-index:0; contenido en z-index:1)

- `dark(min)`: 0 de día; pico 0.85 durante el dip del eclipse (140–158, solo estrellas brillantes: filtro `st.m > d`); rampa 0.1→1 entre 180 y 300.
- **Dip del eclipse:** entre min 141–157 aparecen **Venus y Júpiter etiquetados** (hecho real del eclipse). No quitar.
- Desde `dark>0.65`: Casiopea (la "W") dibujada con líneas + etiqueta; desde min 280, radiante turquesa etiquetado "Radiante · Perseo (sobre el Montseny)".
- Meteoros: nacen cerca del radiante (x .82, y .30), viajan abajo-izquierda, estela con gradiente blanco→turquesa transparente; ~12% son "bolas de fuego" (más largas, con cabeza). `rate(min)`: 0 antes de 270; máx 0.018/frame entre 420–540.
- `prefers-reduced-motion`: estrellas estáticas, sin meteoros animados.

### Brújula

- Siempre visible, fija arriba. No hay botón para abrirla/cerrarla: en Android y escritorio los sensores se enganchan al cargar; en iOS `DeviceOrientationEvent.requestPermission()` requiere gesto → botón "Activar sensores". Heading: `webkitCompassHeading` (iOS) o `360 - alpha` con `deviceorientationabsolute` (Android).
- **Requiere https** → funciona en el deploy de Vercel, no en file://.
- El objetivo cambia con la fase (`target(min)`: <185 eclipse, ≥185 Perseidas). Feedback: "Gira X° a la derecha/izquierda" → "✓ ¡Ahí es!" con glow del aura.
- **Cinta HUD** (`#ctape`), no dial redondo: se dibuja una vez de -180° a 540° a 4 px/grado (así el desplazamiento nunca da la vuelta) y se traslada con `translate(W/2 - heading*4)`. `layout()` iguala el viewBox al ancho real en px. Lleva marcas cada 5°/15°, cardinales de 16 puntos cada 45°, la banda del objetivo (3 copias, ±360°) y los puntos del paisaje con halo `paint-order:stroke`. El borde superior del móvil es el puntero: línea + triángulo fijos en el centro.
- **Háptica** (`navigator.vibrate`, solo Android/Chrome; iOS no expone vibración web): detente `[8]` por cada marca de 5° al barrer lejos → pulsos `[18]` cada vez más seguidos al acercarse (60 ms pegado al objetivo, ~660 ms a `tol+24`) → ráfaga `[70,50,70,50,220]` al fijarlo y latido `[12]` cada 1,4 s. Calla si el móvil no se mueve en 2,5 s o la pestaña no está visible. Al arrancar un evento del timeline: `[90,70,150]` (nunca con el slider de previsualización).
- **Consejo flotante** (`#ctip`, dentro de `.cpanel`): 4 estados por fase en `TIPS`. Mientras el Sol está fuera (min < 185) es la regla de las gafas con estilo de aviso (`.ctip.rule`); luego "móvil guardado 30 min" y "mirada relajada". Sustituye a la vieja sección "3 claves", que se eliminó.

## Idiomas (ca por defecto, es alternativo)

- Un cuarto `<script>`, el primero de todos, monta el módulo de idioma: `window.T(clave)` para textos dinámicos, `window.__lang()`, `window.__setLang(l)` y `window.__i18n` (array de repintados que cada motor registra).
- **El HTML está escrito en catalán.** Cada elemento traducible lleva `data-es="…"` con el castellano; al cambiar de idioma se guarda el catalán original en `data-ca` y se intercambia el `innerHTML`. Para añadir texto nuevo: escríbelo en catalán y añade su `data-es`.
- Los textos dinámicos (barra de estado, brújula, consejos) viven en el diccionario `D.ca` / `D.es`. Las frases con preposición van **completas** (`to2`, `at2`…), no concatenadas: en catalán "Cap a" + "el Sol" daría "cap a el Sol".
- Selector `CA · ES` en la barra de abajo, junto a "⌖ Ara". Se recuerda en `localStorage.lang`.

## Datos reales del 99,5% (comprobados, no inventar)

- A 99,5% **no** se ve corona, ni anillo de diamante, ni perlas de Baily: eso es solo en la franja de totalidad. El 1% de Sol que queda es ~1000 veces más brillante que la corona. La app lo dice explícitamente ("No ho busquis") para que nadie pierda el momento buscándolo.
- Sí se ve: luz gris apagada (no llega a noche), sombras más afiladas, medias lunas proyectadas por las hojas, y **Venus** (mag −4,4) como el objeto más fácil a simple vista. El Sol está a ~2–4° del horizonte, así que hace falta horizonte oeste limpio.
- No prometer bajada de temperatura ni silencio de los pájaros como hechos seguros: a 99,5% el cambio es leve.

## Diseño

- Tipografías: **Fraunces** (display, horas y títulos) + **Instrument Sans** (cuerpo), Google Fonts.
- Paleta: `--night #0A0E1F` (base, dinámica), `--dusk #F2A65A`, `--eclipse #E4572E`, `--meteor #7FD8C4`, `--card #151B3B`, `--line #2A3158`.
- Filas de eventos: estructura visual con chip de icono + etiqueta de color por tipo — 📍 Dónde (azul), 👁 Mirar (naranja), ✦ Qué hacer (turquesa), 📷 Foto (ámbar), 💡 Clave (rosa). Mantener este código de color consistente.
- Tono de los textos: español, para "cero expertos", frases cortas, referencias a paisaje local en vez de coordenadas o grados. Sin astrología (se eliminó una sección de horóscopo a petición del usuario).

## Decisiones ya tomadas (no revertir sin preguntar)

1. Sin etiquetas "AHORA" redundantes.
2. Sin horóscopo.
3. Pre-start = precarga visual de la fase 18:30, nunca "todo apagado/tachado" de día.
4. Negro absoluto (#000) durante toda la ventana de Perseidas.
5. Direcciones siempre con landmarks visibles (Montserrat, La Mola, Montseny), no solo puntos cardinales.
6. Brújula fija arriba y barra de reloj/estado fija abajo, no al revés: arriba porque el borde superior del móvil es el puntero.
7. Sin sección "3 claves" ni aviso estático: los consejos salen flotando bajo la brújula cuando toca. La regla de las gafas nunca se suaviza ni desaparece mientras el Sol está fuera.
8. De noche (min ≥ 270) lo que no es "ahora" se apaga y se desatura (`body.night`); `:hover`/`:active` lo devuelve a color y opacidad plena para poder repasar lo que viene.
9. Sin botón para la brújula: está siempre visible.

## Deploy

- Repo: `osanchezroca/eclipseperseidas` → Vercel (framework "Other", sin build). En la raíz: `index.html` (toda la app), `manifest.webmanifest` + `icon.svg` (instalable, `display:standalone`, `viewport-fit=cover` para los `env(safe-area-inset-*)` de las dos barras fijas), `README.md`. Sin service worker a propósito: nada que invalidar en cada deploy.
- El repo se creó de cero para que el historial no contenga la ubicación del punto 2. No traer commits antiguos de otros repos ni volver a escribir coordenadas en ningún fichero.
- Flujo: editar → `git add . && git commit && git push` → Vercel auto-despliega.
- Probar siempre en móvil por https (brújula y sensores) y con el slider de previsualización (cubre las 10 horas sin esperar a la noche).
