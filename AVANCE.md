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

## Falta

- [ ] Definir la estructura de la base de producción (más de 30 cuentas).
- [ ] Cerrar el alcance de la v1: campañas, orgánico, o ambos.
- [ ] Cargar en `BRIEF.md` sección 2 los IDs y enlaces reales (Sheet de prueba, Apps Script, bot).
- [ ] Crear el bot en Telegram y guardar el token en las Propiedades del script (`TELEGRAM_BOT_TOKEN`).
- [ ] Escribir `bot_informes.gs`: sondeo con offset, confirmación instantánea y respuesta.
- [ ] Medir las cuotas de Apps Script y de Gemini con el volumen real de cuentas.
- [ ] Definir el formato de entrega del informe (enlace al sitio estático vs. dato en el chat).
- [ ] Montar el Proyecto en ChatGPT con `PROTOCOLO.md` y probar que devuelve archivos completos.
