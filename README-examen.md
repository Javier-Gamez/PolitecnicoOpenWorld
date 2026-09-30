# Índice de entrega — Examen Parcial 1: Pull Request con aseguramiento de calidad

Instituto Politécnico Nacional · Escuela Superior de Cómputo
Desarrollo de aplicaciones móviles nativas · Periodo 2027-1 · Grupo 7CV4

> **Nota:** este archivo es el reporte académico de la entrega. Vive en la rama `entrega-examen` de este fork, **separado del Pull Request al repositorio original** (tal como pide el examen), para no mezclar datos del equipo/curso con el diff que revisa el mantenedor.

## Equipo

| Integrante | Usuario de GitHub |
|---|---|
| Javier Gámez | [Javier-Gamez](https://github.com/Javier-Gamez) |

Equipo de un solo integrante para este examen (sin compañeros de equipo adicionales).

## Objetivo y alcance

Agregar un nuevo escenario seleccionable al modo "Titulación por Combate" de Politécnico Open World: **Biblioteca Nacional IPN**, con sus 3 variantes de iluminación (día, atardecer/noche, noche/apocalipsis), siguiendo el mismo patrón que los 16 escenarios ya existentes (`SfStageCatalog.kt` + `SfTheme.kt` + assets generados con `tools/build_map_backgrounds.py`).

**Fuera de alcance (declarado desde la Issue):** el mundo abierto (mapas exteriores/interiores), nuevos peleadores o movesets, y la lógica de red/multijugador del modo de combate.

## Issue

[Javier-Gamez/PolitecnicoOpenWorld#40](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/issues/40) — "Add Biblioteca Nacional IPN as a selectable combat stage", con comportamiento actual/esperado, usuario afectado, archivos a modificar, fuera de alcance y 2 criterios de aceptación (éxito + límite).

## Pull Request

[gabrielhuav/PolitecnicoOpenWorld#148](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/148) — "Add Biblioteca Nacional IPN combat stage", en estado **Draft**, con las secciones `What changed?` / `Why?` / `QA evidence` / `Risk / rollback` en inglés, abierto desde `Javier-Gamez:feature/biblioteca-ipn-stage` hacia `gabrielhuav:main`.

## SHAs

- **SHA base:** [`7ed325393f82872c2be94ff2ada46948efa19152`](https://github.com/gabrielhuav/PolitecnicoOpenWorld/commit/7ed325393f82872c2be94ff2ada46948efa19152) (`upstream/main` al momento de crear la rama del PR).
- **SHA final entregado:** [`92dddec434e79b5dc687e665865baedab9c9080f`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/commit/92dddec434e79b5dc687e665865baedab9c9080f) (`feature/biblioteca-ipn-stage`, 11 commits sobre la base).

## Matriz de pruebas y evidencias

Documento completo: [`docs/pruebas.md`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/blob/feature/biblioteca-ipn-stage/docs/pruebas.md) (en la rama del PR) — 4 riesgos identificados, 6 casos de prueba ejecutados con evidencia real (capturas, datos crudos de `SharedPreferences` vía `adb`, árbol de accesibilidad vía `uiautomator`), todos aprobados.

| Caso | Categoría | Estado |
|---|---|---|
| TC-01 | Ruta feliz (desbloqueo real en Arcade) | ✅ Aprobado |
| TC-02 | Condición límite (bloqueo antes de desbloquear) | ✅ Aprobado |
| TC-03 | Regresión (CU UNAM / Paparazzi 1) | ✅ Aprobado |
| TC-04 | Navegación y estado (Atrás, Home/Pausa) | ✅ Aprobado, con observación no bloqueante |
| TC-05 | Accesibilidad (fuente ampliada, árbol de accesibilidad) | ✅ Aprobado, con limitación real documentada |
| TC-06 | Compatibilidad (emulador, API/arquitectura distinta) | ✅ Aprobado |

Evidencias (capturas, XML de accesibilidad): [`docs/evidencia/`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/tree/feature/biblioteca-ipn-stage/docs/evidencia) en la misma rama.

## Checks automáticos (CI)

Workflow [`pr-quality-gate.yml`](https://github.com/gabrielhuav/PolitecnicoOpenWorld/blob/main/.github/workflows/pr-quality-gate.yml) — 2 ejecuciones, ambas en estado `action_required` (bloqueo externo de GitHub que exige aprobación del mantenedor para correr workflows de un fork, no un fallo propio):
- Run 1: https://github.com/gabrielhuav/PolitecnicoOpenWorld/actions/runs/36298951810
- Run 2: https://github.com/gabrielhuav/PolitecnicoOpenWorld/actions/runs/36532700438

Validación local equivalente (mismas tareas que el CI): `detekt` sin hallazgos nuevos, `:app:testDebugUnitTest` 125/125, `:shared:testAndroidHostTest` 217/217, `:app:assembleDebug` exitoso. Detalle completo en `docs/pruebas.md`.

## Revisión de un compañero

_(Pendiente — el examen exige que otra persona ajena a este PR reproduzca al menos un caso y deje una observación técnica citable en el PR. Se actualizará este apartado con el enlace a la conversación de revisión y las respuestas dadas.)_

## Conclusiones

Se recomienda integrar el cambio: ambos criterios de aceptación de la Issue #40 quedaron verificados con evidencia real (no solo capturas de UI, sino datos persistidos del dispositivo confirmando el desbloqueo real vía la escalera de Arcade). Se documentaron 4 hallazgos menores, ninguno introducido por este PR (todos verificados como preexistentes contra el SHA base o contra pantallas no modificadas por este cambio): navegación Atrás al menú principal, truncamiento visual de nombres largos con fuente ampliada, layout roto del menú principal en landscape en el emulador de prueba, y ruptura de compilación con `MAPS_API_KEY` vacío. El riesgo residual del cambio es bajo por ser aditivo y no tocar lógica de red ni persistencia de otros sistemas.

## Herramientas de IA utilizadas

Se usó **Claude** (Anthropic), a través de Claude Code, como asistente durante todo el proceso: exploración del código del proyecto para entender el patrón de escenarios existentes, generación de los assets del nuevo escenario con la herramienta ya provista por el repositorio (`tools/build_map_backgrounds.py`), redacción y automatización de la ejecución de varios de los 6 casos de prueba (capturas vía `adb`, extracción de árboles de accesibilidad vía `uiautomator`, instalación y prueba en emulador), y redacción de la documentación (`docs/pruebas.md`, esta entrega, la Issue y la descripción del PR). El desarrollador (Javier Gámez) definió el alcance del cambio, tomó las decisiones de diseño (a qué peleador reasignar el escenario, qué material fuente usar), ejecutó personalmente las partidas reales de Arcade necesarias para las pruebas TC-01/TC-03, y revisó/aprobó cada paso antes de aplicarlo al repositorio. Ningún commit ni el PR llevan atribución de autoría de IA, por preferencia explícita del desarrollador.

## Referencias

- Repositorio obligatorio del examen: https://github.com/gabrielhuav/PolitecnicoOpenWorld/
- Fork de trabajo: https://github.com/Javier-Gamez/PolitecnicoOpenWorld
- Rama del PR (código + `docs/pruebas.md` + evidencias): [`feature/biblioteca-ipn-stage`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/tree/feature/biblioteca-ipn-stage)
- Esta rama académica (solo este índice, sin tocar el diff del PR): [`entrega-examen`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/tree/entrega-examen)
