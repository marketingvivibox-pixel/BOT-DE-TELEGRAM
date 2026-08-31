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
