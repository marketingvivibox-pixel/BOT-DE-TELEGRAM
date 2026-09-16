# PROTOCOLO — Documento principal

Actualizado: 2026-09-16 · Proyecto: Vivibox — Bot de informes por chat (Telegram/WhatsApp).

Este es el **único archivo de lectura obligatoria**. Todo lo demás se lee solo cuando la tarea lo
exige, según la tabla de la sección 3. Leer la carpeta completa en cada sesión es un error: gasta
contexto sin aportar.

## 1. Qué es el proyecto (resumen suficiente para el 80% de los pedidos)

Un bot de chat al que se le pide información y devuelve un dato puntual o el enlace a un informe.
Las fuentes son bases en Google Sheets (contenido orgánico y datos de campañas) alojadas en Drive;
la entrega se apoya en el sitio estático ya existente en Firebase Hosting.

- Canal: Telegram (WhatsApp queda como extensión posterior).
- Arquitectura acordada: **sondeo** con `getUpdates` y un activador de Apps Script por minuto,
  no webhook. La demora aceptada es de hasta ~1 minuto.
- Respuesta al usuario: **confirmación instantánea** con el tiempo aproximado de espera, y luego
  el informe o el dato.
- Redacción analítica con IA sobre la capa **gratuita** de Gemini API, aprobada por gerencia.
- **Capa intermedia**: el bot no consulta las hojas directo. Un catálogo declara las fuentes, un
  índice ubica los datos, un router con IA traduce el mensaje a un plan de consulta JSON y un
  ejecutor lee solo lo necesario y cruza fuentes. Ver `BRIEF.md` §4 y D-017 a D-021.
- Fuentes actuales: consolidado diario (`fecha × plataforma × cuenta`) y detalle por publicación,
  ambas de contenido orgánico. Se sumarán ventas y campañas.
- Alcance de la v1: 5 cuentas de muestra. En producción la base recibirá **más de 30 cuentas**.
- Estado actual: **estructura de la base de producción y alcance final de la v1 aún por definir.**
  No construir antes de validar la estructura.

Si el pedido se resuelve con esto, no abras ningún otro archivo.

## 2. Reglas duras (siempre vigentes)

- **No tocar los scripts de los backends de Meta ni de TikTok.** Son fuentes de solo lectura. Si se
  extraen datos de ahí, el código corre en el documento de prueba y es este el que jala la información.
- El documento/proyecto de prueba del bot sí puede modificarse a placer.
- Costo cero: no se contrata hosting ni servicios de pago. Solo se usan las suscripciones ya pagas
  (Gemini, ChatGPT, Claude) y capas gratuitas.
- No volver al enfoque de webhook sin registrar una decisión nueva: fue descartado por falta de
  robustez (cada fallo menor exigía reconfigurar el despliegue).
- No gastar tokens construyendo antes de validar con el usuario la estructura final y el
  funcionamiento exacto.
- **Ningún secreto en la carpeta ni en el repo.** El token del bot vive en las Propiedades del
  script; aquí solo se documenta el nombre de la propiedad (ver D-016).
- No inventar IDs, enlaces, cifras ni resultados de pruebas. Falta un dato → pedirlo.
- Zona horaria siempre `America/Lima`.
- Si se toca el Apps Script, entregar el `.gs` completo, nunca fragmentos.
- No revertir una decisión de `DECISIONS.md` sin registrar el motivo y numerar una nueva.
- Un solo agente escribe en la carpeta a la vez.

## 3. Qué leer según la tarea

| Si la tarea es… | Lee | No leas |
|---|---|---|
| Pregunta general sobre el proyecto | solo este archivo | el resto |
| Continuar el trabajo pendiente | `AVANCE.md` | bitácora, brief, `.gs` |
| Tocar el código de Apps Script | `bot_informes.gs` + sección 4 de `BRIEF.md` | bitácora |
| Cambiar la estructura de las bases en Sheets | sección 3 de `BRIEF.md` | bitácora, `.gs` |
| Necesitar un ID, enlace o URL | sección 2 de `BRIEF.md` | el resto |
| Discutir o proponer una decisión técnica | `DECISIONS.md` | bitácora |
| Saber por qué algo quedó así, o qué pasó antes | primera entrada de `BITACORA.md` | el archivo histórico |
| Auditoría o reconstrucción completa | todo, incluido `BITACORA-ARCHIVO.md` | — |

Lee **secciones**, no archivos completos: los `.md` tienen encabezados numerados; usa el índice del
propio archivo para saltar a lo que necesitas.

## 4. Cómo cerrar la sesión (escritura mínima)

Escribe **solo** los archivos que realmente cambiaron:

| Cambió… | Actualiza |
|---|---|
| Cualquier trabajo real | `BITACORA.md` (entrada nueva arriba) + `AVANCE.md` con su fecha |
| Un ID, enlace, esquema, regla o restricción | `BRIEF.md` |
| Una decisión técnica | `DECISIONS.md` con número nuevo |
| El código de Apps Script | `bot_informes.gs` completo |
| El resumen o las reglas del proyecto | la sección 1 o 2 de este archivo |

Una sesión de solo lectura o de solo preguntas **no** escribe nada.

Formato de entrada de bitácora, breve — máximo 8 líneas:

```markdown
## AAAA-MM-DD · Título corto

**Sesión:** Codex | Claude/Cowork | ChatGPT.

- **Hecho:** ...
- **Decidido:** ...
- **Encontrado:** ...
- **Pendiente:** ...
```

`BITACORA.md` conserva como máximo las **3 entradas más recientes**. Al agregar una cuarta, mueve
la más antigua al inicio de `BITACORA-ARCHIVO.md` sin reescribirla.

## 5. Control de versiones

La carpeta es un repositorio git en la rama `main`, conectado al remoto privado
`https://github.com/marketingvivibox-pixel/BOT-DE-TELEGRAM`. El historial local y el remoto son el
respaldo contra ediciones simultáneas y pérdida del equipo.

- Al cerrar una sesión con trabajo real: `git add -A` y un commit cuyo título diga qué cambió y
  entre paréntesis el agente. Ejemplo: `Ajusta el sondeo getUpdates (Codex)`.
- Después de confirmar el commit, subir `main` a `origin` y verificar que quede sincronizado.
- Antes de escribir: `git status`. Si hay cambios sin commitear de otra sesión, commitearlos o
  consultarlo antes de encimar trabajo.
- Para ver qué cambió desde la última sesión: `git log --oneline -5` y `git diff HEAD~1`.
- Para revertir un archivo: `git checkout HEAD -- <archivo>`. Nunca reescribir historia publicada.
- `git log` no reemplaza la bitácora: el commit dice qué líneas cambiaron; la bitácora dice por qué.

## 6. Agentes

| Agente | Entrada | Acceso |
|---|---|---|
| Codex | `AGENTS.md` → este archivo | lectura y escritura directas |
| Claude / Cowork | `CLAUDE.md` → este archivo | lectura y escritura directas |
| ChatGPT | `CHATGPT.md` | sin acceso al disco; trabaja con copias subidas al Proyecto |

Para ChatGPT: en el Proyecto se sube **solo este archivo** más lo que la tabla de la sección 3 pida
para la tarea en curso. Al cerrar, ChatGPT devuelve el texto completo de los archivos que cambiaron
y el usuario los pega en la carpeta. Ante discrepancia, gana la carpeta. Detalles en `CHATGPT.md`.

## 7. Proyecto hermano

El expediente de **Vivibox — Control de pagos publicitarios** vive en su propia carpeta del
Escritorio y usa este mismo protocolo. Son proyectos separados: no mezclar bitácoras, decisiones ni
commits entre ambos.
