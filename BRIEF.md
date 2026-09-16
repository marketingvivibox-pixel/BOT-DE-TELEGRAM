# BRIEF — Ficha técnica

Actualizado: 2026-09-16 · **Leer solo la sección que la tarea pida** (ver tabla 3 de `PROTOCOLO.md`).

Índice: 1 Objetivo · 2 Recursos e IDs · 3 Fuentes de datos y esquema · 4 Arquitectura de la capa
intermedia · 5 Secretos y configuración · 6 Limitaciones conocidas · 7 Próximo paso operativo

## 1. Objetivo

Permitir que el equipo pida información por chat (Telegram, y más adelante WhatsApp) y reciba de
vuelta un dato puntual o el enlace a un informe generado a partir de las bases en Google Sheets.
Las solicitudes pueden abarcar 1 o varios días y **cruzar varias fuentes**. La misma base debe poder
alimentar dashboards en Looker Studio.

Plan a futuro: sumar a estas mismas bases información de ventas y de campañas publicitarias.

## 2. Recursos e IDs

- **Consolidado diario** — `📊 Vivibox — Contenido Orgánico (consolidado)`
  - Spreadsheet ID: `1e32NdR_lL_mMKiC9MVHivz3Fz8m_d7fHpkWqtC7o3-M`
  - URL: https://docs.google.com/spreadsheets/d/1e32NdR_lL_mMKiC9MVHivz3Fz8m_d7fHpkWqtC7o3-M/edit?gid=997427818
  - gid: `997427818`
- **Detalle por publicación** — `📋 Vivibox — Contenido Orgánico (publicaciones individuales)`
  - Spreadsheet ID: `1tq4b83Se2yDOZjYR1148w-7I2mcdhJJCzdxSUyH07IY`
  - URL: https://docs.google.com/spreadsheets/d/1tq4b83Se2yDOZjYR1148w-7I2mcdhJJCzdxSUyH07IY/edit?gid=1692174054
  - gid: `1692174054` · pestaña `publicaciones`
- Carpeta de Drive que los contiene: `1_JCedeKnG3URbyezE1A9KVq6GUz1X-ch`
- **Proyecto de Apps Script del bot**: `1Ip8I1RlhtxIVoqHNxXSjJNEg0vDYANi0mDleDP15yBBYuC521JfKd2RQ`
  - URL: https://script.google.com/u/0/home/projects/1Ip8I1RlhtxIVoqHNxXSjJNEg0vDYANi0mDleDP15yBBYuC521JfKd2RQ/edit
- Bot de Telegram: nombre/handle `por confirmar` · chat_id de pruebas `por confirmar`
- Sitio de informes (Firebase Hosting): https://vivibox-analisis.web.app
- Repositorio privado de GitHub: https://github.com/marketingvivibox-pixel/BOT-DE-TELEGRAM
- Copia local del repositorio: `C:\Users\D. Cordova\Desktop\Vivibox - Bot de informes`
- Propietario de los archivos: `marketingvivibox@gmail.com`
- Zona horaria: `America/Lima`

Se agregarán más fuentes (ventas, campañas publicitarias). Toda fuente nueva se registra en el
catálogo de la sección 4, no en el código.

## 3. Fuentes de datos y esquema

### 3.1 Consolidado diario — grano `fecha × plataforma × cuenta`

`fecha`, `plataforma`, `id_cuenta`, `nombre_cuenta`, `publicaciones`, `alcance`, `impresiones`,
`interacciones`, `me_gusta`, `comentarios`, `compartidos`, `guardados`, `reproducciones_video`,
`seguidores_nuevos`, `ultima_actualizacion`, `origen`.

La columna `origen` indica de dónde salió la fila (`detalle` cuando se derivó del detalle).

### 3.2 Detalle por publicación — grano `publicación`

`fecha_publicacion`, `plataforma`, `nombre_cuenta`, `id_publicacion`, `url_publicacion`,
`tipo_contenido`, `descripcion`, `alcance`, `impresiones`, `interacciones`, `me_gusta`,
`comentarios`, `compartidos`, `guardados`, `reproducciones_video`, `tasa_interaccion`,
`ultima_actualizacion`.

### 3.3 Llave de cruce

`fecha + plataforma + nombre_cuenta` es hoy la única llave común entre ambas fuentes.

`id_cuenta` está vacío hoy porque aún no corre el paso que lo trae: **se poblará desde la API de
cada plataforma** en la etapa de ingesta correspondiente. Una vez poblado, es la llave de cruce
definitiva y `nombre_cuenta` queda solo como etiqueta legible.

Mientras tanto el cruce depende de `nombre_cuenta`, que con más de 30 cuentas es frágil (tildes,
mayúsculas, cambios de nombre). No es un bloqueo de diseño, sí un orden: el router y el ejecutor se
escriben asumiendo `id_cuenta` como llave desde el principio.

### 3.4 Estado

Ambas hojas tienen estructura definida y filas de ejemplo en cero. **La estructura de la base de
producción (más de 30 cuentas) sigue por definir**; la muestra actual tiene 5.

## 4. Arquitectura de la capa intermedia

### 4.0 Dónde vive

Toda la capa vive **dentro del proyecto de Apps Script del bot** (§2), que es autónomo: no está
contenido en ninguna de las hojas de datos. Las fuentes se leen desde fuera con
`SpreadsheetApp.openById(...)` en modo lectura.

Consecuencia importante: **no hace falta tocar el Apps Script de ninguna fuente**, ni ahora ni
cuando se agreguen las próximas. El único requisito para incorporar una fuente es que la cuenta que
ejecuta el bot tenga permiso de lectura sobre ella. Si una fuente pertenece a otro equipo, basta con
que la compartan; su código no se modifica ni se lee.

Las hojas de control `_FUENTES` e `_INDICE` viven en un **Sheet de control propio del bot**, nunca
dentro de una hoja de datos ajena.

El bot no consulta las hojas directamente. Entre el mensaje y la respuesta hay cuatro piezas:

### 4.1 Catálogo de fuentes (`_FUENTES`)

Una hoja de control con una fila por fuente: `id_fuente`, `nombre`, `spreadsheet_id`, `pestaña`,
`grano`, `llave`, `columnas_expuestas`, `métricas`, `rango_fechas_disponible`, `activa`.

Agregar una fuente nueva = agregar una fila. El código no se toca. Esta es la pieza que hace
sostenible el «se agregarán más fuentes».

### 4.2 Índice (`_INDICE`)

Una fila por combinación `fecha × plataforma × cuenta × fuente`, con puntero al rango de filas que
le corresponde. Permite saltar directo al bloque de datos sin recorrer la base completa. Se
reconstruye con un activador nocturno; el bot solo lo lee.

### 4.3 Router (interpretación con IA)

Gemini **no** ve los datos ni redacta todavía: traduce el mensaje del usuario a un plan de consulta
en JSON — intención, métricas, dimensiones, rango de fechas, cuentas, plataformas, formato de
salida. Un plan mal formado se rechaza y se le pide precisión al usuario, no se adivina.

### 4.4 Ejecutor

Con el plan en mano: elige del catálogo qué fuentes cubren esas métricas al grano pedido, usa el
índice para ubicar los rangos, lee **solo** esas columnas y filas, cruza por la llave común y
agrega. Si la pregunta se responde al grano diario, no se toca el detalle por publicación.

### 4.5 Redacción y entrega

Gemini recibe el agregado ya reducido (decenas de filas, nunca la base) y redacta. La entrega es un
dato en el chat o el enlace a una página HTML en el sitio de Firebase Hosting.

### 4.5.1 Adaptadores por tipo de fuente

Cada fila de `_FUENTES` declara un `tipo_fuente` que apunta a una función lectora registrada en el
código (`sheet_tabular`, `sheet_pivote`, `csv_drive`, `api_rest`, …). El ejecutor no sabe leer
fuentes: le pide al adaptador que corresponda que le devuelva filas normalizadas al esquema común
(`fecha`, `plataforma`, `id_cuenta`, métricas).

Esto fija la frontera de lo configurable:

- Fuente nueva **con una forma ya conocida** → una fila en `_FUENTES`. Cero código.
- Fuente nueva **con una forma distinta** (otro grano, otra orientación, un API) → una fila más una
  función adaptadora nueva de pocas líneas. Sigue sin tocarse el código de la fuente ni el resto de
  la capa.

### 4.6 Flujo de mensajes

Activador por minuto → `getUpdates` con offset guardado → confirmación instantánea con tiempo
estimado → router → ejecutor → redacción → respuesta final.

_Todo lo de esta sección es el diseño acordado, no comportamiento verificado._

## 5. Secretos y configuración

El token del bot y cualquier API key **no se guardan en esta carpeta ni en el repositorio**. Viven
en las Propiedades del script del proyecto de Apps Script.

| Propiedad | Contenido | Estado |
|---|---|---|
| `TELEGRAM_BOT_TOKEN` | token del bot de Telegram | por crear |
| `GEMINI_API_KEY` | API key de la capa gratuita de Gemini | por crear |
| `TELEGRAM_OFFSET` | último `update_id` procesado | por crear |

Lectura en código: `PropertiesService.getScriptProperties().getProperty('TELEGRAM_BOT_TOKEN')`.
Aquí solo se documenta el **nombre** de la propiedad, nunca su valor.

## 6. Limitaciones conocidas

- El sondeo introduce una demora de hasta ~1 minuto; es un costo aceptado a cambio de robustez.
- Apps Script tiene cuotas diarias de ejecución y de `UrlFetch`; con más de 30 cuentas hay que
  medirlas antes de dar la v1 por buena.
- Google Sheets no es una base de datos: el índice y el filtrado por columnas existen justamente
  para no leer rangos completos. Si el volumen la desborda, la salida natural es BigQuery, que
  tiene capa gratuita.
- La capa gratuita de Gemini tiene límites de uso; gerencia aceptó que Google use los datos
  «por ahora».
- `id_cuenta` se llena recién en la ingesta desde API; hasta entonces el cruce usa `nombre_cuenta` (ver 3.3).
- WhatsApp no tiene camino gratuito equivalente al de Telegram; queda fuera de la v1.

## 7. Próximo paso operativo

Definir la estructura de la base de producción. Luego crear las hojas `_FUENTES` e `_INDICE` con
las dos fuentes actuales, antes de escribir el router. `id_cuenta` llega por API en el paso de
ingesta y no bloquea este avance.
