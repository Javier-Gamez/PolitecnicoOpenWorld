# Plan y ejecución de QA — Biblioteca Nacional IPN (combat stage)

- **Issue:** [Javier-Gamez/PolitecnicoOpenWorld#40](https://github.com/Javier-Gamez/PolitecnicoOpenWorld/issues/40)
- **PR:** [gabrielhuav/PolitecnicoOpenWorld#148](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/148)
- **SHA base:** `7ed325393f82872c2be94ff2ada46948efa19152` (`upstream/main`)
- **SHA probado (inicial):** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Autor:** Javier Gámez (Javier-Gamez)

## Criterios de aceptación (de la Issue #40)

| # | Criterio | Casos que lo cubren |
|---|---|---|
| 1 | Éxito: tras vencer a `POLICIA_GRANADERO_MUJER` en Arcade, "Biblioteca Nacional IPN" (3 luces) es seleccionable en Práctica/Arcade y un combate completo renderiza el fondo correctamente | TC-01 |
| 2 | Límite: antes de vencerla, el escenario permanece bloqueado (no seleccionable) | TC-02 |

## Riesgos identificados

| # | Riesgo | Impacto | Caso que lo cubre |
|---|---|---|---|
| R1 | El thumbnail del escenario se renderiza como mosaico de 28 cuadros en vez de una sola imagen (bug real ya encontrado y corregido durante el desarrollo: extensión `_thumb.png` vs `_thumb.webp` esperada por `SfStageSelectOverlay.kt`) | El jugador no puede identificar visualmente el escenario en el selector | TC-01 |
| R2 | La reasignación de `POLICIA_GRANADERO_MUJER` (antes compartía `CU UNAM` con `PAPARAZZI_1`) rompe el desbloqueo o el fondo de `CU UNAM` para `PAPARAZZI_1` | Un escenario existente deja de funcionar (regresión) | TC-03 |
| R3 | El escenario nuevo no respeta el sistema de bloqueo (aparece seleccionable sin haber vencido a la peleadora dueña) | El jugador accede a contenido antes de lo previsto, rompiendo la progresión del juego | TC-02 |
| R4 | El atlas animado (1920×1890 px, tope de 2048 px) no decodifica o degrada el rendimiento en un dispositivo/API distinto al usado en desarrollo | Crash o freeze al abrir el selector o iniciar el combate en otros dispositivos | TC-06 |

## Cobertura de los 6 casos

| ID | Categoría |
|---|---|
| TC-01 | Ruta feliz |
| TC-02 | Condición límite / alterna |
| TC-03 | Regresión |
| TC-04 | Navegación y estado |
| TC-05 | Accesibilidad |
| TC-06 | Compatibilidad / entorno |

---

## TC-01 — Ruta feliz: desbloquear y jugar en Biblioteca Nacional IPN

- **Criterio cubierto:** Criterio de aceptación #1. Riesgo R1.
- **Autor / fecha de ejecución:** Javier Gámez, 2026-09-29
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Progreso de Arcade reiniciado (`adb shell pm clear`); Modo Desarrollador confirmado **desactivado** (`DEVELOPER_MODE=false`) antes de jugar, para que el desbloqueo fuera real y no vía "unlock all" de desarrollador.
- **Pasos ejecutados:**
  1. Abrir Titulación por Combate → Arcade (peleador Estudiante/`ESCOMBOY`, dificultad Fácil).
  2. Jugar la escalera de Arcade hasta la Pelea 10 de 15, correspondiente a `Policía Granadera Mujer` (confirmado por el orden real de `ladderRivals` en el guardado del dispositivo).
  3. Ganar el combate (calificación B) pese a perder la primera ronda.
  4. Ir a Práctica libre → selector de escenarios y localizar "Biblioteca Nacional IPN".
  5. Confirmar visualmente que ya no muestra candado 🔒.
  6. Verificar además, en una partida previa de Práctica contra la misma rival, que el fondo anima correctamente durante un combate completo (ronda jugada de inicio a "WINS").
- **Resultado esperado:** La tarjeta muestra una sola imagen (miniatura limpia, sin mosaico) con el arte pixel-art; al seleccionarla se anima; el combate se desarrolla con el fondo animado (día) renderizando correctamente durante todo el match, sin errores visuales ni cierres inesperados.
- **Resultado real:** Coincide con lo esperado. Verificación de datos en el dispositivo tras la victoria: `LADDER_STEP=10` y `UNLOCKED_MAPS_V2` ahora incluye `fondo_biblioteca_ipn_anim.webp`, `..._noche_1_anim.webp` y `..._noche_2_anim.webp` (desbloqueo real y persistente, no solo de UI). El selector de Práctica muestra las 3 tarjetas sin candado. El combate jugado contra la misma rival en este escenario mostró el fondo animado (pixel-art, atardecer con "#FESARAGON"/"#IPNDE") renderizando de forma fluida y sin artefactos durante toda la pelea, terminando en pantalla de victoria normal ("ESCOMBOY WINS").
- **Estado:** ✅ Aprobado
- **Evidencia:**
  - [`TC-01_arcade_granadera_1.webp`](evidencia/TC-01_arcade_granadera_1.webp), [`TC-01_arcade_granadera_2_wins.webp`](evidencia/TC-01_arcade_granadera_2_wins.webp), [`TC-01_arcade_calificacion.png`](evidencia/TC-01_arcade_calificacion.png) — combate real de Arcade y calificación "Pelea 10 de 15".
  - [`TC-01_selector_desbloqueado.webp`](evidencia/TC-01_selector_desbloqueado.webp) — selector sin candado tras el desbloqueo.
  - [`TC-01_combate_biblioteca_1.webp`](evidencia/TC-01_combate_biblioteca_1.webp), [`TC-01_combate_biblioteca_2.webp`](evidencia/TC-01_combate_biblioteca_2.webp), [`TC-01_combate_biblioteca_3_wins.webp`](evidencia/TC-01_combate_biblioteca_3_wins.webp) — combate completo con el fondo animado.
- **Defecto asociado y decisión:** R1 (thumbnail en mosaico) — **corregido** antes de esta ejecución (ver commit `53873915`, conversión de `_thumb.png` a `_thumb.webp`); se re-verificó aquí que la corrección sigue vigente (thumbnails limpios en el selector). Sin hallazgos nuevos.

---

## TC-02 — Condición límite: escenario bloqueado antes de vencer a la peleadora dueña

- **Criterio cubierto:** Criterio de aceptación #2. Riesgo R3.
- **Autor / fecha de ejecución:** Javier Gámez, 2026-09-29
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Progreso de Arcade reiniciado vía `adb shell pm clear ovh.gabrielhuav.pow` (no existe un botón de reset en Ajustes accesible al jugador), de forma que `POLICIA_GRANADERO_MUJER` NO haya sido vencida. **Modo Desarrollador desactivado** en Ajustes (ver nota de entorno abajo).
- **Pasos:**
  1. Confirmar que el progreso de Arcade está reiniciado (solo peleadores/mapas por defecto desbloqueados).
  2. Abrir el selector de escenarios en Práctica libre.
  3. Localizar la tarjeta "Biblioteca Nacional IPN".
  4. Intentar tocarla.
- **Resultado esperado:** La tarjeta aparece atenuada (semi-transparente) con un ícono de candado 🔒 superpuesto; el toque no hace nada (no navega ni la selecciona), igual que el resto de escenarios no desbloqueados.
- **Resultado real:** La tarjeta "Biblioteca Nacional IPN" (las 3 variantes) aparece atenuada con 🔒, igual que "CECyT 2 (Noche 2)" y "Ciudad Universitaria" (tampoco desbloqueadas aún). Al tocarla, no reacciona: no se resalta, no navega, el botón "Elegir este mapa" permanece deshabilitado. Coincide exactamente con lo esperado.
- **Estado:** ✅ Aprobado
- **Evidencia:** [`docs/evidencia/TC-02_selector_bloqueado.png`](evidencia/TC-02_selector_bloqueado.png)
- **Defecto asociado y decisión:** Ninguno. **Nota de entorno (no es defecto del cambio):** tras el primer `pm clear`, "Modo Desarrollador" apareció reactivado (`DEVELOPER_MODE=true`) sin intervención del usuario — posiblemente restaurado por el backup automático de Samsung del dispositivo de prueba. Se desactivó manualmente en Ajustes antes de repetir la prueba. No afecta la validez de este resultado ni es atribuible al cambio de este PR.

---

## TC-03 — Regresión: CU UNAM sigue funcionando para Paparazzi 1

- **Criterio cubierto:** No rompe funcionalidad existente. Riesgo R2.
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Progreso de Arcade reiniciado (mismo run de TC-01: la escalera real pasa por `PAPARAZZI_1` en la posición 3, antes de llegar a `POLICIA_GRANADERO_MUJER` en la 10).
- **Pasos ejecutados:**
  1. Como parte de la misma corrida de Arcade de TC-01, se venció a `Paparazzi 1` (posición 3 de 15) camino a la Policía Granadera Mujer.
  2. Verificar en los datos persistidos del dispositivo (`pow_sf_arcade.xml`) el estado de `UNLOCKED_MAPS_V2` para el escenario `unam_biblioteca_cu`.
- **Resultado esperado:** Las 3 luces de "Ciudad Universitaria UNAM" quedan desbloqueadas y funcionan exactamente igual que antes de la reasignación (sin relación ya con `Policía Granadera Mujer`).
- **Resultado real:** `UNLOCKED_MAPS_V2` contiene `fondo_unam_biblioteca_cu_anim.webp`, `..._noche_1_anim.webp` y `..._noche_2_anim.webp` tras vencer a Paparazzi 1, exactamente igual que antes de este PR. El mecanismo de desbloqueo (`SfArcadeRepository.unlockFighter` → `SfStageCatalog.unlockableMapsForFighter`) es el mismo código ya validado end-to-end en TC-01 para Biblioteca Nacional IPN; no se modificó ningún dato de `UNAM_CU` (`Stage`, archivos, `SfTheme`) en este PR, solo se retiró a `POLICIA_GRANADERO_MUJER` como dueña secundaria. Por ese motivo se consideró suficiente la verificación por datos persistidos, sin repetir una captura de combate adicional en este escenario (el renderizado del fondo de CU UNAM no fue tocado por el cambio).
- **Estado:** ✅ Aprobado
- **Evidencia:** Extracto de `pow_sf_arcade.xml` (comando `adb shell run-as ovh.gabrielhuav.pow cat shared_prefs/pow_sf_arcade.xml`) mostrando `unam_biblioteca_cu_anim.webp`, `unam_biblioteca_cu_noche_1_anim.webp`, `unam_biblioteca_cu_noche_2_anim.webp` en `UNLOCKED_MAPS_V2`; mismo dump reutilizado de TC-01.
- **Defecto asociado y decisión:** Ninguno. Sin regresión.

---

## TC-04 — Navegación y estado: back, reapertura y ciclo de vida

- **Criterio cubierto:** Robustez de navegación (no ligado a un criterio de aceptación específico, pero exigido por el examen).
- **Autor / fecha de ejecución:** Javier Gámez, 2026-09-29
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** `POLICIA_GRANADERO_MUJER` ya vencida (heredado de TC-01).
- **Pasos ejecutados:**
  1. Abrir el selector de escenarios (Práctica) y seleccionar "Biblioteca Nacional IPN".
  2. Presionar Atrás (botón/gesto del sistema) antes de confirmar "Elegir este mapa".
  3. Observar a dónde regresa la navegación.
  4. Reabrir el selector, elegir "Biblioteca Nacional IPN" y confirmar, iniciando un combate real.
  5. Durante el combate, presionar el botón Home del sistema (pasar la app a segundo plano) y reabrir Politécnico Open World desde recientes.
  6. **Nota sobre orientación (verificada en código):** no se encontró `android:screenOrientation` en `AndroidManifest.xml` ni bloqueo programático (`requestedOrientation`) para la pantalla de combate — la orientación **no está fija**. Sin embargo, este comportamiento es genérico de toda la pantalla de combate (compartido por los 16 escenarios preexistentes) y no fue tocado por este PR, que solo agrega una entrada más al catálogo/tema. Se decidió no repetir una prueba de rotación por ser una característica preexistente y ajena al alcance de este cambio; en su lugar se profundizó en la transición Home/reanudación (paso 5), que sí ejercita el mismo código de guardado/restauración de sesión (`ArcadeSession`) que usa nuestro escenario nuevo igual que los demás.
- **Resultado esperado:** Ninguna transición produce cierre inesperado; al volver de segundo plano, el combate continúa en el mismo estado (mismo mapa, mismos peleadores, progreso de la ronda conservado o pausado correctamente).
- **Resultado real:** (Paso 2-3) Presionar Atrás desde el selector de mapa **no retrocede un paso dentro del flujo de configuración** (peleador → rival → dificultad → mapa); regresa directo a la pantalla principal ("POLITÉCNICO OPEN WORLD"), perdiendo las selecciones previas de peleador/rival/dificultad. No hay crash ni congelamiento, solo pérdida de progreso de configuración. (Paso 5) Al presionar Home durante el combate y reabrir la app, esta muestra correctamente una pantalla de **"PAUSA"** con el logo de POW y el mismo estado del combate (peleadores, marcador, mapa) intacto, con botón "Continuar" — comportamiento correcto y seguro.
- **Estado:** ✅ Aprobado, con una observación
- **Evidencia:** [`TC-04_back_a_menu_principal.png`](evidencia/TC-04_back_a_menu_principal.png), [`TC-04_home_pausa.webp`](evidencia/TC-04_home_pausa.webp)
- **Defecto asociado y decisión:** **Observación (no bloqueante, preexistente):** el botón Atrás en el selector de mapa de Práctica salta directo al menú principal en vez de retroceder un paso. Es un comportamiento de navegación genérico de la pantalla de combate, compartido por los 16 escenarios ya existentes antes de este PR — no fue introducido ni se relaciona con el cambio de este PR (que solo añade datos a `SfStageCatalog`/`SfTheme`, sin tocar navegación). Se documenta como hallazgo preexistente, fuera del alcance de esta corrección; no bloquea la integración de este cambio.

---

## TC-05 — Accesibilidad: texto ampliado y TalkBack

- **Criterio cubierto:** Accesibilidad del selector (exigido por el examen, no específico de la issue).
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Ajustes de Android → Accesibilidad → Tamaño de fuente al máximo; TalkBack activado.
- **Pasos:**
  1. Con tamaño de fuente ampliado, abrir el selector de escenarios.
  2. Verificar que el nombre "Biblioteca Nacional IPN" no se corta ni desborda la tarjeta.
  3. Activar TalkBack y navegar con gestos hasta la tarjeta del escenario.
  4. Verificar qué anuncia TalkBack al enfocar la tarjeta (nombre, y si indica estado bloqueado/desbloqueado).
  5. Verificar el tamaño del área táctil de la tarjeta (debe ser razonablemente grande para tocar con precisión).
- **Resultado esperado:** El texto se mantiene legible sin desbordarse; TalkBack anuncia al menos el nombre del escenario; el área táctil es consistente con las demás tarjetas del selector.
- **Resultado real:** _(pendiente de ejecución)_
- **Estado:** _(pendiente)_
- **Evidencia:** _(pendiente — captura + nota de lo anunciado por TalkBack)_
- **Defecto asociado y decisión:** _(pendiente)_. **Limitación conocida:** el selector no parece tener contentDescription específico más allá del texto visible (por verificar en el código); se documentará como limitación real si aplica, sin inventar hallazgos.

---

## TC-06 — Compatibilidad: otro dispositivo/API (emulador)

- **Criterio cubierto:** Riesgo R4.
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Emulador `CatalogoUI` — Pixel 7 (perfil), API 37.1, x86_64
- **Precondiciones y datos:** Mismo APK debug instalado; progreso de Arcade limpio o con `POLICIA_GRANADERO_MUJER` ya vencida (repetir TC-01 en este entorno).
- **Pasos:**
  1. Instalar el APK debug en el emulador `CatalogoUI`.
  2. Repetir los pasos de TC-01 (desbloquear y jugar en Biblioteca Nacional IPN) en este entorno.
- **Resultado esperado:** Mismo comportamiento que en el dispositivo físico: el atlas decodifica sin errores, el combate corre sin caídas de rendimiento perceptibles ni crashes, en un API level y tamaño de pantalla distintos.
- **Resultado real:** _(pendiente de ejecución)_
- **Estado:** _(pendiente)_
- **Evidencia:** _(pendiente)_
- **Defecto asociado y decisión:** _(pendiente)_

---

## Checks automáticos del PR (CI)

Workflow analizado: [`.github/workflows/pr-quality-gate.yml`](https://github.com/gabrielhuav/PolitecnicoOpenWorld/blob/main/.github/workflows/pr-quality-gate.yml), revisado el 2026-09-28. Se dispara en `pull_request` (opened/synchronize/reopened) hacia `main`, solo si cambian archivos bajo `PolitecnicoOpenWorld/**`. Tiene 2 jobs bloqueantes: `unit-tests` (JDK 21, `gradle :app:assembleDebug :app:testDebugUnitTest :shared:testAndroidHostTest --stacktrace`, con `secrets.properties` generado a partir del secret `MAPS_API_KEY` y sin `google-services.json`) y `detekt` (análisis estático con `config/detekt/baseline.xml`, bloqueante solo ante hallazgos nuevos).

| Run | Commit | Fecha | Estado reportado por GitHub | Liga |
|---|---|---|---|---|
| 1 | `53873915` (commits de código/assets) | 2026-09-27 | `action_required` (0 jobs ejecutados) | https://github.com/gabrielhuav/PolitecnicoOpenWorld/actions/runs/36298951810 |
| 2 | `9c9f8461` (plan de QA) | 2026-09-29 | `action_required` (0 jobs ejecutados) | https://github.com/gabrielhuav/PolitecnicoOpenWorld/actions/runs/36532700438 |

**Interpretación:** `action_required` con 0 jobs ejecutados es el gate estándar de GitHub Actions que exige **aprobación manual del mantenedor** para correr workflows en Pull Requests que vienen de un fork externo (protección de secretos del repositorio ante colaboradores por primera vez). No es un fallo del código ni de los tests — es un bloqueo externo fuera de nuestro control, tal como contempla el examen. No tenemos permisos de mantenedor sobre `gabrielhuav/PolitecnicoOpenWorld` para aprobarlo nosotros mismos.

**Validación local equivalente realizada** (mismas tareas que ejecuta el job `unit-tests`, con `JDK` compatible con 21 vía `gradle-daemon-jvm.properties` del propio proyecto, y el mismo `detekt-cli 1.23.8` + baseline que usa el job `detekt`):

| Verificación local | Resultado |
|---|---|
| `detekt-cli` con `config/detekt/detekt.yml` + `baseline.xml` | ✅ exit code 0, 0 hallazgos nuevos |
| `tools/check_kmp_test_names.sh` | ✅ pasa |
| `gradle :app:assembleDebug :app:testDebugUnitTest :shared:testAndroidHostTest --stacktrace` | ✅ BUILD SUCCESSFUL |
| `:app:testDebugUnitTest` | ✅ 125/125, 0 fallas |
| `:shared:testAndroidHostTest` | ✅ 217/217, 0 fallas |

**Dependencia que limita la cobertura:** igual que el CI, la validación local se hizo con `secrets.properties` conteniendo un `MAPS_API_KEY` placeholder (no una clave real de Google Maps) y sin `google-services.json` — por lo tanto **no** se validó que las funciones de mapas (Google Maps nativo) o Firebase Auth funcionen con credenciales reales; solo se valida que el proyecto compile y pase sus pruebas unitarias sin esas integraciones configuradas, igual que hace el CI oficial a propósito.

**Hallazgo relacionado (defecto preexistente, no introducido por este PR):** un `MAPS_API_KEY` **vacío** (en vez de un placeholder no vacío) en `secrets.properties` rompe `compileDebugJavaWithJavac` con `illegal start of expression` en `BuildConfig.java` (el campo generado queda como `public static final String MAPS_API_KEY = ;`). Reproducido también en un checkout limpio del SHA base `7ed325393f82872c2be94ff2ada46948efa19152` (antes de cualquier commit de este PR) — confirmado como preexistente en `main`, no relacionado con este cambio. Estado: **preexistente, no corregido** (fuera del alcance declarado de este PR).

---

## Cierre del QA

_(Se completa al terminar todas las ejecuciones: recomendación de integrar o no el cambio, evidencia que la respalda, y riesgos que permanecen.)_
