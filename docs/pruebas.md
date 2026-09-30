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
- **Autor / fecha de ejecución:** Javier Gámez, 2026-09-29
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** `font_scale` del sistema puesto en `1.3` vía `adb shell settings put system font_scale 1.3` (equivalente a "Tamaño de fuente" ampliado en Ajustes); restaurado a `1.0` al finalizar. En vez de activar TalkBack y transcribir audio, se extrajo el **árbol de accesibilidad real** de la pantalla (`adb shell uiautomator dump`), que es la misma información que un lector de pantalla como TalkBack usa para decidir qué anunciar — evidencia técnica equivalente y más precisa que una transcripción manual.
- **Pasos ejecutados:**
  1. Aumentar `font_scale` a 1.3 y volver a entrar al selector de escenarios (Práctica).
  2. Localizar visualmente la tarjeta "Biblioteca Nacional IPN" y comparar con la captura a escala normal.
  3. Extraer el árbol de accesibilidad de la pantalla con `uiautomator dump` y revisar los nodos `text` y `content-desc` de la tarjeta, además de los `bounds` del contenedor clickeable.
- **Resultado esperado:** El texto se mantiene legible sin desbordarse; la información expuesta a un lector de pantalla incluye al menos el nombre del escenario; el área táctil es consistente con las demás tarjetas del selector.
- **Resultado real:**
  - **Visual (fuente ampliada):** el rótulo se trunca a **"Biblioteca"** (pierde "Nacional IPN"). El mismo problema afecta a un escenario ya existente ajeno a este PR: "Ciudad Universitaria UNAM" se trunca a **"Ciudad"**. Es una limitación real, pero del layout genérico de la tarjeta (ancho fijo + una sola línea), no introducida por este cambio.
  - **Árbol de accesibilidad:** el nodo `TextView` interno SÍ contiene el texto completo `text="Biblioteca Nacional IPN"` (el corte es solo de renderizado visual, no del dato). Además, un nodo hermano expone `content-desc="Biblioteca Nacional IPN"` (nombre completo, sin truncar) — es decir, un lector de pantalla real dispone del nombre completo aunque la persona con baja visión que usa fuente grande vea el texto cortado.
  - **Área táctil:** el contenedor clickeable de la tarjeta mide aprox. 417×337 px (`bounds="[244,322][661,659]"` en pantalla landscape ~2400×1080), muy por encima del mínimo recomendado (~48dp).
- **Estado:** ✅ Aprobado, con una limitación real documentada (no bloqueante)
- **Evidencia:**
  - [`TC-05_fuente_normal.png`](evidencia/TC-05_fuente_normal.png) / [`TC-05_biblioteca_fuente_ampliada.png`](evidencia/TC-05_biblioteca_fuente_ampliada.png) — comparación visual antes/después.
  - [`TC-05_ui_dump.xml`](evidencia/TC-05_ui_dump.xml) — árbol de accesibilidad crudo con los nodos `text`/`content-desc`/`bounds` citados arriba.
- **Defecto asociado y decisión:** **Limitación real (preexistente, no bloqueante):** el rótulo visible se trunca con fuente ampliada del sistema en TODAS las tarjetas de nombre largo (no solo la nuestra); el layout de `SfStageSelectOverlay` no fue modificado por este PR. Impacto acotado porque el `content-desc` sí lleva el nombre completo para lectores de pantalla. **Hallazgo adicional (fuera de este caso, ver TC-04):** cambiar `font_scale` del sistema fuerza una recreación de actividad que reinicia la navegación del flujo de combate a la pantalla de elección de peleador — mismo patrón que ya se documentó como preexistente y ajeno al alcance de este PR.

---

## TC-06 — Compatibilidad: otro dispositivo/API (emulador)

- **Criterio cubierto:** Riesgo R4.
- **Autor / fecha de ejecución:** Javier Gámez, 2026-09-29
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Emulador `CatalogoUI` — perfil Pixel 7, **API 37 (Android 17 preview)**, x86_64, idioma del sistema en **inglés** (a diferencia del Samsung físico en español) — cubre además la variante de idioma de la categoría "Compatibilidad o entorno".
- **Precondiciones y datos:** Instalación limpia del mismo APK debug (sin progreso de Arcade previo en este emulador).
- **Pasos ejecutados:**
  1. Instalar el APK debug en el emulador `CatalogoUI` y conceder el permiso de ubicación al abrir.
  2. Navegar Titulación por Combate → Other modes → Practice → peleador → rival → dificultad → selector de escenario.
  3. Ubicar la tarjeta "Biblioteca Nacional IPN" (día) desplazando la lista horizontal.
  4. Intentar enfocarla/seleccionarla (sin haberla desbloqueado en este emulador).
- **Resultado esperado:** El atlas decodifica sin errores (miniatura limpia, sin mosaico); el estado de bloqueo se respeta igual que en el dispositivo físico; no hay crashes.
- **Resultado real:** La tarjeta "Biblioteca Nacional IPN" se renderiza correctamente (imagen única, arte pixel-art, sin mosaico ni artefactos) en esta API/arquitectura distinta (API 37 x86_64 vs. API 34 arm64 del Samsung). Al no estar desbloqueada en esta instalación limpia, aparece con 🔒 y **no se puede enfocar ni seleccionar** al tocarla (el foco permanece en la tarjeta previamente seleccionada) — mismo comportamiento de bloqueo verificado en TC-02, consistente entre dispositivos. No se repitió la escalera completa de Arcade en este entorno (la lógica de desbloqueo es Kotlin puro, ya verificada end-to-end en TC-01 de forma independiente del dispositivo); el alcance de este caso se limitó a la parte sensible al entorno: decodificación/render del atlas y la pantalla de selección.
- **Hallazgo adicional (fuera de alcance, no relacionado a este PR):** la pantalla de **menú principal** (no la de combate) se renderiza rota en landscape en este emulador — el contenido queda comprimido en la mitad izquierda de la pantalla y la mitad derecha queda en negro. La pantalla de combate/selector de escenarios (donde vive nuestro cambio) **sí** se ve correctamente a pantalla completa. Se documenta como limitación observada del entorno de prueba / posible defecto preexistente del menú principal, ajeno al alcance de este PR (que no toca esa pantalla).
- **Estado:** ✅ Aprobado
- **Evidencia:** [`TC-06_biblioteca.png`](evidencia/TC-06_biblioteca.png) (tarjeta renderizada correctamente), [`TC-06_biblioteca_preview2.png`](evidencia/TC-06_biblioteca_preview2.png) (intento de selección sin efecto, bloqueada), [`TC-06_combate1.png`](evidencia/TC-06_combate1.png) (menú principal roto en landscape, hallazgo adicional), [`TC-06_combate2.png`](evidencia/TC-06_combate2.png) (pantalla de combate correcta en el mismo emulador).
- **Defecto asociado y decisión:** Ninguno relacionado a este PR. El hallazgo del menú principal en landscape se deja registrado como observación, fuera del alcance de esta corrección (no se modificó esa pantalla).

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

**Resumen de los 6 casos:**

| Caso | Categoría | Estado |
|---|---|---|
| TC-01 | Ruta feliz | ✅ Aprobado |
| TC-02 | Condición límite | ✅ Aprobado |
| TC-03 | Regresión | ✅ Aprobado |
| TC-04 | Navegación y estado | ✅ Aprobado (con observación no bloqueante) |
| TC-05 | Accesibilidad | ✅ Aprobado (con limitación real documentada) |
| TC-06 | Compatibilidad | ✅ Aprobado |

**Recomendación:** se recomienda **integrar este cambio**. Los dos criterios de aceptación de la Issue #40 quedaron verificados con evidencia real y reproducible (datos persistidos del dispositivo, no solo capturas de UI): el escenario se desbloquea correctamente al vencer a `POLICIA_GRANADERO_MUJER` en Arcade real, permanece bloqueado antes de eso, no rompe el escenario que antes compartía (`CU UNAM`/Paparazzi 1), y el atlas renderiza correctamente tanto en el dispositivo físico como en un emulador con API y arquitectura distintas.

**Hallazgos que quedan pendientes (ninguno bloqueante para este PR):**
1. El botón Atrás en el selector de mapa de Práctica regresa al menú principal en vez de retroceder un paso — preexistente, ajeno a este cambio (TC-04).
2. Los rótulos largos de escenario se truncan visualmente con `font_scale` grande, aunque el `content-desc` para lectores de pantalla sí lleva el nombre completo — preexistente, afecta también a escenarios previos como CU UNAM (TC-05).
3. El menú principal se renderiza roto en landscape en el emulador de prueba — preexistente, pantalla no tocada por este PR (TC-06).
4. Un `MAPS_API_KEY` vacío rompe la compilación (`compileDebugJavaWithJavac`) — preexistente, confirmado también en el SHA base sin nuestros commits (Parte 3 / CI).

Ninguno de estos 4 hallazgos fue introducido por este PR (se verificó explícitamente en cada caso, comparando contra el comportamiento preexistente o contra el SHA base). Se dejan documentados como transparencia del proceso de QA, no como bloqueantes de esta contribución específica.

**Riesgo residual:** bajo. El cambio es aditivo (nuevo dato de catálogo + reasignación de un peleador + assets), no toca lógica de red, persistencia de otros sistemas, ni pantallas fuera del modo de combate. El único checkpoint pendiente fuera de nuestro control es la aprobación manual del CI por parte del mantenedor (`action_required`), y la revisión de un compañero de equipo (Parte 3.2, pendiente de coordinar).
