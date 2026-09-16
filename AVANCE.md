# AVANCE — Estado

Actualizado: 2026-08-31 · Leer al continuar el trabajo pendiente.

## Hecho

- Definido el objetivo: informes y datos puntuales pedidos por chat, con fuentes en Google Sheets
  y Drive, y dashboards en Looker Studio sobre las mismas bases.
- Descartado el enfoque de webhook y acordada la migración a **sondeo** (`getUpdates` + activador
  por minuto).
- Gerencia aprobó el uso de la **capa gratuita de Gemini API** e integrar IA analítica de redacción.
- Acordado el requisito de **confirmación instantánea** con tiempo aproximado de espera.
- Autorizado construir el primer modelo del bot con datos publicitarios sobre una Sheet de prueba.
- Sitio de informes ya existente en Firebase Hosting (`vivibox-analisis.web.app`).
- Expediente compartido Codex–Claude–ChatGPT creado con lectura por demanda (`PROTOCOLO.md`).
- Identificadas y documentadas las dos primeras fuentes reales (consolidado diario y detalle por
  publicación) y el proyecto de Apps Script del bot; esquemas volcados en `BRIEF.md` §3.
- Acordada la capa intermedia: catálogo de fuentes, índice, router con IA y ejecutor
  (`BRIEF.md` §4, decisiones D-017 a D-021).

## Falta

- [ ] Definir la estructura de la base de producción (más de 30 cuentas).
- [ ] Cerrar el alcance de la v1: campañas, orgánico, o ambos.
- [ ] Traer `id_cuenta` desde la API en el paso de ingesta y adoptarlo como llave de cruce (D-021).
- [ ] Crear las hojas `_FUENTES` e `_INDICE` con las dos fuentes actuales.
- [ ] Definir el activador nocturno que reconstruye el índice.
- [ ] Definir el esquema del plan de consulta JSON que devuelve el router.
- [ ] Definir el esquema común normalizado y el registro de adaptadores por `tipo_fuente` (D-023).
- [ ] Crear el Sheet de control propio del bot que aloja `_FUENTES` e `_INDICE` (D-022).
- [ ] Cargar el handle del bot de Telegram y el chat_id de pruebas en `BRIEF.md` §2.
- [ ] Crear el bot en Telegram y guardar el token en las Propiedades del script (`TELEGRAM_BOT_TOKEN`).
- [ ] Escribir `bot_informes.gs`: sondeo con offset, confirmación instantánea y respuesta.
- [ ] Medir las cuotas de Apps Script y de Gemini con el volumen real de cuentas.
- [ ] Definir el formato de entrega del informe (enlace al sitio estático vs. dato en el chat).
- [ ] Montar el Proyecto en ChatGPT con `PROTOCOLO.md` y probar que devuelve archivos completos.
