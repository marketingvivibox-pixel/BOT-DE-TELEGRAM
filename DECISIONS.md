# DECISIONES

Actualizado: 2026-08-31 · Leer al tomar o cuestionar una decisión técnica.

## D-001 · Sondeo en lugar de webhook

El bot consulta `getUpdates` desde un activador por minuto. El webhook fue descartado por falta de
robustez: cada fallo menor obligaba a reconfigurar despliegues y ajustes. Se acepta una demora de
hasta ~1 minuto a cambio de que el sistema se recupere solo.

## D-002 · Confirmación instantánea

El bot responde de inmediato con una confirmación que indica el tiempo aproximado de espera, y
entrega el informe después. Un silencio de un minuto no es aceptable para el usuario final.

## D-003 · Costo cero

No se contrata hosting ni servicios de pago. Solo se usan las suscripciones ya pagas (Gemini,
ChatGPT, Claude), las capas gratuitas y la infraestructura ya existente (Sheets, Apps Script,
Firebase Hosting).

## D-004 · IA analítica sobre la capa gratuita de Gemini

Gerencia aprobó usar la capa gratuita de Gemini API para la redacción analítica, aceptando «por
ahora» que Google use los datos. Si eso cambia, se numera una decisión nueva.

## D-005 · Fuentes de Meta y TikTok de solo lectura

No se modifican los scripts de los backends de Meta ni de TikTok. Si se necesitan sus datos, el
código corre en el documento de prueba del bot y es este el que jala la información.

## D-006 · El documento de prueba es desechable

El archivo y el proyecto de Apps Script de prueba del bot pueden modificarse a placer. Esa libertad
existe justamente para no tener que tocar nada de producción.

## D-007 · Validar antes de construir

No se escribe código antes de validar con el usuario la estructura final y el funcionamiento
exacto. Construir sobre una estructura no confirmada se considera gasto de tokens, no avance.

## D-008 · Una sola fuente local

La carpeta visible del Escritorio es el repositorio documental principal. Cualquier entrada de otro
agente debe apuntar a ella, para que no existan copias divergentes.

## D-009 · Protocolo único multi-agente

Las instrucciones operativas viven solo en `PROTOCOLO.md`. `AGENTS.md`, `CLAUDE.md` y `CHATGPT.md`
son punteros finos y no repiten reglas.

## D-010 · ChatGPT trabaja con copias

ChatGPT no tiene acceso al disco. Participa mediante copias subidas a un Proyecto y devuelve el
texto completo de los archivos modificados para que el usuario los pegue en la carpeta. Ante
cualquier discrepancia, la carpeta del Escritorio gana sobre las copias del Proyecto.

## D-011 · Un escritor a la vez

Solo un agente edita la carpeta por vez. Antes de escribir se revisa que la última entrada de
`BITACORA.md` corresponda a una sesión cerrada.

## D-012 · Lectura por demanda

`PROTOCOLO.md` es el único archivo de lectura obligatoria. El resto se abre solo cuando su tabla de
enrutamiento lo indica, y por secciones. Leer la carpeta completa en cada sesión es un error de
procedimiento.

## D-013 · Bitácora acotada

`BITACORA.md` conserva un máximo de 3 entradas, de hasta 8 líneas cada una. Las anteriores se mueven
sin reescribir a `BITACORA-ARCHIVO.md`, que solo se lee en auditorías.

## D-014 · Escritura selectiva al cerrar

Se actualizan únicamente los archivos que cambiaron, según la tabla de la sección 4 del protocolo.
Una sesión de solo lectura no escribe nada.

## D-015 · Repositorio git local sin remoto

La carpeta es un repositorio git en `main`, sin remoto. Da historial, autoría y reversión, que es el
respaldo técnico de la regla de un solo escritor a la vez (D-011). No es respaldo ante pérdida del
equipo; si se quiere respaldo externo, se decide aparte.

## D-016 · Secretos fuera del repositorio

El token del bot de Telegram y las API keys viven exclusivamente en las Propiedades del script del
proyecto de Apps Script. En la carpeta se documenta solo el **nombre** de cada propiedad, nunca su
valor. Motivo: la carpeta está versionada en git y la abren tres agentes distintos; un secreto
commiteado queda en el historial de forma permanente.

## D-017 · Capa intermedia entre el chat y los datos

El bot no consulta las hojas directamente. Entre el mensaje y la respuesta hay un router que
interpreta el pedido, un ejecutor que resuelve la consulta y una etapa de redacción. Motivo: las
fuentes van a crecer (ventas, campañas) y sin esa capa cada fuente nueva obliga a reescribir el
bot. Detalle en la sección 4 de `BRIEF.md`.

## D-018 · Catálogo de fuentes en vez de fuentes cableadas

Las fuentes se declaran como filas de una hoja `_FUENTES` (id, spreadsheet, pestaña, grano, llave,
columnas, métricas, rango de fechas). Agregar una fuente es agregar una fila, no editar código.

## D-019 · Índice previo, no barrido

Una hoja `_INDICE` mapea `fecha × plataforma × cuenta × fuente` al rango de filas correspondiente y
se reconstruye con un activador nocturno. El bot ubica por índice y lee solo el rango y las
columnas necesarias. Motivo: Sheets no es una base de datos y las cuotas de Apps Script se agotan
leyendo rangos completos.

## D-020 · La IA interpreta y redacta, no consulta

Gemini se usa en dos puntos separados: convertir el mensaje en un plan de consulta JSON, y redactar
sobre el resultado ya agregado. Nunca recibe la base ni decide qué filas leer. Motivo: controlar el
consumo de la capa gratuita y que un error del modelo no se traduzca en datos inventados. Un plan
mal formado se rechaza y se le pide precisión al usuario.

## D-021 · Cruce por id_cuenta, no por nombre

La llave de cruce entre fuentes es `fecha + plataforma + id_cuenta`. `id_cuenta` se trae desde la
API de cada plataforma en el paso de ingesta; hoy está vacío porque ese paso todavía no corre.
`nombre_cuenta` queda como etiqueta legible, nunca como llave. El router y el ejecutor se escriben
asumiendo `id_cuenta` desde el inicio, aunque en la muestra actual todavía no esté poblado.

## D-022 · La capa vive en el proyecto del bot, no en las fuentes

Todo el código de la capa intermedia reside en el proyecto de Apps Script del bot, que es autónomo.
Las fuentes se leen desde fuera con `SpreadsheetApp.openById(...)`. Motivo: hay y habrá fuentes cuyo
Apps Script no podemos ni debemos tocar —sea por la regla de solo lectura de Meta y TikTok, sea
porque pertenecen a otro equipo—. El único requisito para sumar una fuente es permiso de lectura
para la cuenta que ejecuta el bot; su código nunca se modifica.

Las hojas de control `_FUENTES` e `_INDICE` viven en un Sheet de control propio del bot, no dentro
de una hoja de datos ajena.

## D-023 · Adaptadores por tipo de fuente

Cada fuente declara en `_FUENTES` un `tipo_fuente` que apunta a una función lectora registrada en el
código, que devuelve filas normalizadas al esquema común. El ejecutor no lee fuentes: las pide al
adaptador. Una fuente de forma conocida se agrega con una fila y cero código; una de forma nueva
agrega además un adaptador corto. Motivo: sin esta separación, «configurable» se degrada a un
`if` por fuente dentro del ejecutor.

## D-024 · Repositorio privado de GitHub como remoto

El repositorio local conserva la rama `main` y usa como remoto `origin` el repositorio privado
`https://github.com/marketingvivibox-pixel/BOT-DE-TELEGRAM`. Esta decisión sustituye únicamente el
estado «sin remoto» de D-015 y añade respaldo externo; D-016 sigue vigente y prohíbe guardar tokens,
claves u otros secretos en el repositorio o su historial.
