# BITÁCORA — últimas 3 sesiones

Más reciente arriba. Las entradas anteriores viven en `BITACORA-ARCHIVO.md`.

## 2026-08-31 · Fuentes reales y capa intermedia

**Sesión:** Claude/Cowork.

- **Hecho:** documentadas las dos fuentes reales con IDs, gids y esquema de columnas, más el
  proyecto de Apps Script del bot. Escrita la sección 4 de `BRIEF.md` con la arquitectura de la
  capa intermedia: catálogo `_FUENTES`, índice `_INDICE`, router con IA y ejecutor.
- **Decidido:** D-017 a D-023 (capa intermedia, catálogo, índice, rol acotado de la IA, cruce por
  `id_cuenta`, alojamiento en el proyecto del bot y adaptadores por tipo de fuente).
- **Encontrado:** `id_cuenta` está vacío hasta que corra la ingesta por API; el cruce provisional usa
  `nombre_cuenta`. La capa se aloja fuera de las fuentes para no depender de su Apps Script.
- **Pendiente:** poblar `id_cuenta` y crear `_FUENTES` e `_INDICE` antes de escribir el router.

## 2026-08-31 · Expediente del bot creado

**Sesión:** Claude/Cowork.

- **Hecho:** creada la carpeta del expediente replicando la estructura del proyecto de pagos:
  protocolo, brief, avance, decisiones, bitácora, archivo histórico y punteros de agente.
  Volcado a los archivos el contexto ya acordado del bot.
- **Decidido:** D-016, los secretos viven en las Propiedades del script, nunca en la carpeta.
- **Encontrado:** el proyecto no tenía expediente en disco; el contexto estaba solo en conversación.
- **Pendiente:** cargar IDs reales en `BRIEF.md` §2 y definir la estructura de la base de producción.
