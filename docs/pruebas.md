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
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Progreso de Arcade limpio o con `POLICIA_GRANADERO_MUJER` aún no vencida (usar "Reset" del arcade si ya se había vencido antes en pruebas manuales previas).
- **Pasos:**
  1. Abrir el modo Arcade / Titulación por Combate.
  2. Jugar la escalera de Arcade hasta enfrentar y vencer a `Policía Granadera Mujer`.
  3. Ir a Práctica libre y abrir el selector de escenarios.
  4. Localizar la tarjeta "Biblioteca Nacional IPN".
  5. Seleccionarla y confirmar "Elegir este mapa".
  6. Jugar un combate completo (ganar o perder una ronda) sin salir de la pantalla.
- **Resultado esperado:** La tarjeta muestra una sola imagen (miniatura limpia, sin mosaico) con el arte pixel-art; al seleccionarla se anima; el combate se desarrolla con el fondo animado (día) renderizando correctamente durante todo el match, sin errores visuales ni cierres inesperados.
- **Resultado real:** _(pendiente de ejecución)_
- **Estado:** _(pendiente)_
- **Evidencia:** _(pendiente — capturas/video)_
- **Defecto asociado y decisión:** R1 (thumbnail en mosaico) — **corregido** antes de esta ejecución (ver commit `53873915`, conversión de `_thumb.png` a `_thumb.webp`). Se re-verifica aquí que la corrección sigue vigente.

---

## TC-02 — Condición límite: escenario bloqueado antes de vencer a la peleadora dueña

- **Criterio cubierto:** Criterio de aceptación #2. Riesgo R3.
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Progreso de Arcade reiniciado (Ajustes → Reset del progreso de Arcade), de forma que `POLICIA_GRANADERO_MUJER` NO haya sido vencida.
- **Pasos:**
  1. Confirmar en Ajustes que el progreso de Arcade está reiniciado (solo peleadores/mapas por defecto desbloqueados).
  2. Abrir el selector de escenarios en Práctica libre.
  3. Localizar la tarjeta "Biblioteca Nacional IPN".
  4. Intentar tocarla.
- **Resultado esperado:** La tarjeta aparece atenuada (semi-transparente) con un ícono de candado 🔒 superpuesto; el toque no hace nada (no navega ni la selecciona), igual que el resto de escenarios no desbloqueados.
- **Resultado real:** _(pendiente de ejecución)_
- **Estado:** _(pendiente)_
- **Evidencia:** _(pendiente — captura)_
- **Defecto asociado y decisión:** Ninguno esperado; si la tarjeta apareciera seleccionable, sería un defecto crítico (rompe la progresión) → bloqueante.

---

## TC-03 — Regresión: CU UNAM sigue funcionando para Paparazzi 1

- **Criterio cubierto:** No rompe funcionalidad existente. Riesgo R2.
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** Progreso de Arcade reiniciado.
- **Pasos:**
  1. Jugar la escalera de Arcade hasta vencer a `Paparazzi 1`.
  2. Ir a Práctica libre, abrir el selector de escenarios.
  3. Verificar el estado de la tarjeta "Ciudad Universitaria UNAM" (día, noche, noche 2).
  4. Seleccionarla y jugar un combate corto.
- **Resultado esperado:** Las 3 luces de "Ciudad Universitaria UNAM" quedan desbloqueadas y funcionan exactamente igual que antes de la reasignación (sin relación ya con `Policía Granadera Mujer`); el combate corre sin errores.
- **Resultado real:** _(pendiente de ejecución)_
- **Estado:** _(pendiente)_
- **Evidencia:** _(pendiente)_
- **Defecto asociado y decisión:** Ninguno esperado.

---

## TC-04 — Navegación y estado: back, reapertura y ciclo de vida

- **Criterio cubierto:** Robustez de navegación (no ligado a un criterio de aceptación específico, pero exigido por el examen).
- **Autor / fecha de ejecución:** _(pendiente)_
- **SHA probado:** `538739150a811b3b095555a55c63dd1ff13d6580`
- **Versión de la app:** `1.0.0.18` (debug)
- **Dispositivo/API/config:** Samsung SM-G998U, Android 14, API 34
- **Precondiciones y datos:** `POLICIA_GRANADERO_MUJER` ya vencida (heredado de TC-01).
- **Pasos:**
  1. Abrir el selector de escenarios y seleccionar "Biblioteca Nacional IPN".
  2. Presionar Atrás antes de confirmar "Elegir este mapa".
  3. Confirmar que se regresa a la pantalla anterior sin crash y sin haber cambiado el mapa.
  4. Reabrir el selector, elegir "Biblioteca Nacional IPN" y confirmar.
  5. Durante el combate, presionar el botón Home (pasar la app a segundo plano) y volver a abrirla.
  6. **Nota sobre orientación:** si la pantalla de combate tiene la orientación fija (verificar en el manifest/código), documentarlo aquí y omitir la prueba de rotación, sustituyéndola por la transición Home/reanudación del paso 5.
- **Resultado esperado:** Ninguna transición produce cierre inesperado; al volver de segundo plano, el combate continúa en el mismo estado (mismo mapa, mismos peleadores, progreso de la ronda conservado o pausado correctamente).
- **Resultado real:** _(pendiente de ejecución)_
- **Estado:** _(pendiente)_
- **Evidencia:** _(pendiente — video corto de la secuencia)_
- **Defecto asociado y decisión:** _(pendiente)_

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

## Cierre del QA

_(Se completa al terminar todas las ejecuciones: recomendación de integrar o no el cambio, evidencia que la respalda, y riesgos que permanecen.)_
