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

[gabrielhuav/PolitecnicoOpenWorld#148](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/148) — "Add Biblioteca Nacional IPN combat stage", en estado **Ready for review** (pasó por Draft mientras se ejecutaba el QA), con las secciones `What changed?` / `Why?` / `QA evidence` / `Risk / rollback` en inglés, abierto desde `Javier-Gamez:feature/biblioteca-ipn-stage` hacia `gabrielhuav:main`.

## SHAs

- **SHA base:** [`7ed325393f82872c2be94ff2ada46948efa19152`](https://github.com/gabrielhuav/PolitecnicoOpenWorld/commit/7ed325393f82872c2be94ff2ada46948efa19152) (`upstream/main` al momento de crear la rama del PR).
- **SHA final entregado:** [`3e3963d2e06aa6a4e138fb9ba0f26d7d266d23fb`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/commit/3e3963d2e06aa6a4e138fb9ba0f26d7d266d23fb) (`feature/biblioteca-ipn-stage`, 14 commits sobre la base — incluye las correcciones pedidas en la revisión de @jesusGoliat, ver más abajo).

## Matriz de pruebas y evidencias

Documento completo: [`docs/pruebas.md`](docs/pruebas.md) (en esta misma rama académica, fuera del diff del PR) — 4 riesgos identificados, 6 casos de prueba ejecutados con evidencia real (capturas, datos crudos de `SharedPreferences` vía `adb`, árbol de accesibilidad vía `uiautomator`), todos aprobados.

| Caso | Categoría | Estado |
|---|---|---|
| TC-01 | Ruta feliz (desbloqueo real en Arcade) | ✅ Aprobado |
| TC-02 | Condición límite (bloqueo antes de desbloquear) | ✅ Aprobado |
| TC-03 | Regresión (CU UNAM / Paparazzi 1) | ✅ Aprobado |
| TC-04 | Navegación y estado (Atrás, Home/Pausa) | ✅ Aprobado, con observación no bloqueante |
| TC-05 | Accesibilidad (fuente ampliada, árbol de accesibilidad) | ✅ Aprobado, con limitación real documentada |
| TC-06 | Compatibilidad (emulador, API/arquitectura distinta) | ✅ Aprobado |

Evidencias (capturas, XML de accesibilidad): [`docs/evidencia/`](docs/evidencia/) en esta misma rama.

## Checks automáticos (CI)

Workflow [`pr-quality-gate.yml`](https://github.com/gabrielhuav/PolitecnicoOpenWorld/blob/main/.github/workflows/pr-quality-gate.yml) — ejecuciones en estado `action_required` (bloqueo externo de GitHub que exige aprobación del mantenedor para correr workflows de un fork, no un fallo propio):
- Run 1: https://github.com/gabrielhuav/PolitecnicoOpenWorld/actions/runs/36298951810
- Run 2: https://github.com/gabrielhuav/PolitecnicoOpenWorld/actions/runs/36532700438

Validación local equivalente (mismas tareas que el CI), tras el SHA final: `detekt` sin hallazgos nuevos, `:app:testDebugUnitTest` 125/125, `:shared:testAndroidHostTest` 221/221 (incluye los 4 tests nuevos de `SfStageCatalogTest`), `:app:assembleDebug` exitoso. Detalle completo en `docs/pruebas.md`.

## Revisión de un compañero

**Recibidas en el PR #148:**
1. [`luisAgt`](https://github.com/luisAgt) — Approved, comentario "QA complete" (genérico; se le pidió ampliarlo con un caso específico reproducido, pendiente de su respuesta).
2. [`jesusGoliat`](https://github.com/jesusGoliat) — [revisión completa](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/148#pullrequestreview-5383208475) con reconstrucción y ejecución real de la rama en un emulador (Pixel 3a, API 33): reprodujo TC-02, probó un caso extra de ruta de actualización para guardados previos al PR, y dejó 3 hallazgos reales. Los 3 fueron atendidos:
   - `docs/` fuera del diff del PR → movido a esta rama (`dab34caf`, `ecb07047`).
   - Comentarios con conteo desactualizado (16→17 escenarios) → corregido (`6f095953`).
   - Falta de test automatizado para el cambio de datos → agregado `SfStageCatalogTest.kt` con 4 casos, incluida una verificación de consistencia cruzada `SfStageCatalog` ↔ `SfTheme` (`3e3963d2`).
   - [Respuesta punto por punto en el PR](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/148#issuecomment-5937590495).

**Hechas por mí a otros equipos** (cumpliendo la exigencia de revisar trabajo ajeno, no solo recibir revisión):
1. [PR #153](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/153#pullrequestreview-5382836438) (Alexis177, tamaño de controles) — confirmé el uso correcto de un parámetro de tamaño real (no `Modifier.scale()`) citando una advertencia ya documentada en el código sobre un bug previo en iOS, más una nota menor de estilo.
2. [PR #170](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/170#pullrequestreview-5382828840) (luisAgt, audio) — hallazgo real: el PR quita una protección contra condición de carrera de `SoundPool` que el propio proyecto ya resuelve correctamente en otro lugar, en vez de reutilizarla.
3. [PR #172](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/172#pullrequestreview-5382926923) (jesusGoliat, validación de IP LAN) — confirmé el orden seguro de evaluación que evita overflow en `toInt()`, y verifiqué el caso de ceros a la izquierda contra la lógica de la función.

## Bitácora (Javier Gámez)

| Fecha | Qué hice |
|---|---|
| 2026-09-22 | Definí el alcance del cambio (nuevo escenario de combate) y revisé el patrón existente de `SfStageCatalog`/`SfTheme`. |
| 2026-09-23 | Commits `23695269`–`53873915`: catálogo, reasignación de peleador, registro en el selector, material fuente y assets generados. Validé build/tests localmente y probé en dispositivo físico por primera vez. |
| 2026-09-24 | Corregí el bug real de thumbnail en mosaico (`_thumb.png` vs `_thumb.webp` esperado por la UI) y reemplacé las imágenes por las versiones pixel-art generadas con Gemini. |
| 2026-09-27 a 29 | Preparé la Parte 1 del examen: Issue #40, SHA base, Draft PR #148 con las 4 secciones en inglés. |
| 2026-09-29 | Diseñé y ejecuté los 6 casos de `docs/pruebas.md` con evidencia real en dispositivo físico y emulador (commits `9c9f8461`–`92dddec4`). Escribí el Cierre del QA. |
| 2026-10-01 | Revisé los PR #153, #170 y #172 de compañeros con comentarios técnicos citables. Recibí revisión de `jesusGoliat`, corregí los 3 hallazgos reales (moví `docs/` fuera del diff, corregí comentarios desactualizados, agregué `SfStageCatalogTest.kt`), respondí en el PR y lo marqué como Ready for review. |

Commits de código del PR: [`23695269`..`3e3963d2`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/compare/7ed3253...3e3963d2) (14 commits). Casos ejecutados: los 6 de la matriz de arriba. Revisiones hechas: ver sección anterior.

## Conclusiones

Se recomienda integrar el cambio: ambos criterios de aceptación de la Issue #40 quedaron verificados con evidencia real (no solo capturas de UI, sino datos persistidos del dispositivo confirmando el desbloqueo real vía la escalera de Arcade), y confirmados de forma independiente por la revisión de @jesusGoliat construyendo y corriendo la rama por su cuenta. Los hallazgos reales que surgieron durante la revisión de pares (ubicación de `docs/`, comentarios desactualizados, falta de un test de consistencia) ya fueron corregidos. Quedan documentados 4 hallazgos menores adicionales, ninguno introducido por este PR (todos preexistentes o en pantallas no tocadas por el cambio): navegación Atrás al menú principal, truncamiento visual de nombres largos con fuente ampliada, layout roto del menú principal en landscape en el emulador de prueba, y ruptura de compilación con `MAPS_API_KEY` vacío. El riesgo residual del cambio es bajo por ser aditivo y no tocar lógica de red ni persistencia de otros sistemas.

## Herramientas de IA utilizadas

Se usó **Claude** (Anthropic), a través de Claude Code, como asistente durante todo el proceso: exploración del código del proyecto para entender el patrón de escenarios existentes, generación de los assets del nuevo escenario con la herramienta ya provista por el repositorio (`tools/build_map_backgrounds.py`), redacción y automatización de la ejecución de varios de los 6 casos de prueba (capturas vía `adb`, extracción de árboles de accesibilidad vía `uiautomator`, instalación y prueba en emulador), y redacción de la documentación (`docs/pruebas.md`, esta entrega, la Issue y la descripción del PR). El desarrollador (Javier Gámez) definió el alcance del cambio, tomó las decisiones de diseño (a qué peleador reasignar el escenario, qué material fuente usar), ejecutó personalmente las partidas reales de Arcade necesarias para las pruebas TC-01/TC-03, y revisó/aprobó cada paso antes de aplicarlo al repositorio. Ningún commit ni el PR llevan atribución de autoría de IA, por preferencia explícita del desarrollador.

## Referencias

- Repositorio obligatorio del examen: https://github.com/gabrielhuav/PolitecnicoOpenWorld/
- Fork de trabajo: https://github.com/Javier-Gamez/PolitecnicoOpenWorld
- Rama del PR (solo código y assets del escenario): [`feature/biblioteca-ipn-stage`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/tree/feature/biblioteca-ipn-stage)
- Esta rama académica (índice + `docs/pruebas.md` + evidencias, sin tocar el diff del PR): [`entrega-examen`](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/tree/entrega-examen)
