# BRIEF — Ficha técnica

Actualizado: 2026-08-31 · **Leer solo la sección que la tarea pida** (ver tabla 3 de `PROTOCOLO.md`).

Índice: 1 Objetivo · 2 Recursos e IDs · 3 Bases de datos y esquema · 4 Comportamiento previsto ·
5 Secretos y configuración · 6 Limitaciones conocidas · 7 Próximo paso operativo

## 1. Objetivo

Permitir que el equipo pida información por chat (Telegram, y más adelante WhatsApp) y reciba de
vuelta un dato puntual o el enlace a un informe generado a partir de las bases en Google Sheets.
Las solicitudes pueden abarcar 1 o varios días. La misma base debe poder alimentar dashboards en
Looker Studio.

Plan a futuro: sumar a estas mismas bases información de ventas y de campañas publicitarias.

## 2. Recursos e IDs

_Completar con datos reales. No inventar: si falta uno, pedirlo._

- Sheet de prueba (campañas): Spreadsheet ID `por confirmar` · URL `por confirmar`
- Base de contenido orgánico (carpeta de Drive): `por confirmar` — ver expediente de contenido orgánico
- Proyecto de Apps Script del bot: project ID `por confirmar` · URL `por confirmar`
- Bot de Telegram: nombre/handle `por confirmar` · chat_id de pruebas `por confirmar`
- Sitio de informes (Firebase Hosting): https://vivibox-analisis.web.app
- Zona horaria: `America/Lima`

## 3. Bases de datos y esquema

- Muestra actual: 5 cuentas, con gasto, impresiones, alcance y mensajes iniciados por cuenta de
  Meta y TikTok.
- Producción: **más de 30 cuentas** de redes sociales.
- **La estructura definitiva de la base de producción está por definir.** Es el bloqueo principal:
  nada de código de consulta se da por bueno hasta cerrarla.
- Fuentes de Meta y TikTok: solo lectura. El código del bot vive en el documento de prueba y es
  este el que jala la información.

## 4. Comportamiento previsto

- Un activador por minuto llama a la función de sondeo, que consulta `getUpdates` de la API de
  Telegram y procesa los mensajes nuevos.
- El `update_id` del último mensaje procesado se guarda para no reprocesar (offset).
- Ante una solicitud válida: responder **de inmediato** con una confirmación que indique el tiempo
  aproximado de espera; luego entregar el dato o el enlace al informe.
- La redacción analítica del informe se apoya en la capa gratuita de Gemini API.
- Los informes se publican como páginas HTML estáticas en el sitio de Firebase Hosting.

_Todo esto es el diseño acordado, no comportamiento verificado. No afirmar que algo funciona sin
haberlo probado._

## 5. Secretos y configuración

El token del bot y cualquier API key **no se guardan en esta carpeta ni en el repositorio**. Viven
en las Propiedades del script del proyecto de Apps Script.

| Propiedad | Contenido | Estado |
|---|---|---|
| `TELEGRAM_BOT_TOKEN` | token del bot de Telegram | por crear |
| `GEMINI_API_KEY` | API key de la capa gratuita de Gemini | por crear |

Lectura en código: `PropertiesService.getScriptProperties().getProperty('TELEGRAM_BOT_TOKEN')`.
Aquí solo se documenta el **nombre** de la propiedad, nunca su valor.

## 6. Limitaciones conocidas

- El sondeo introduce una demora de hasta ~1 minuto; es un costo aceptado a cambio de robustez.
- Apps Script tiene cuotas diarias de ejecución y de `UrlFetch`; con más de 30 cuentas hay que
  medirlas antes de dar la v1 por buena.
- La capa gratuita de Gemini tiene límites de uso; gerencia aceptó que Google use los datos
  «por ahora».
- WhatsApp no tiene camino gratuito equivalente al de Telegram; queda fuera de la v1.

## 7. Próximo paso operativo

Definir con el usuario la estructura de la base de producción y el alcance exacto de la v1
(campañas vs. orgánico). Recién entonces se escribe código.
