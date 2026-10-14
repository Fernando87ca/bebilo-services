# Implementación — Ingredientes manuales custom

Estado: completado; backend validado y redesplegado. Fecha: 2026-10-14. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

Agregador HEAD `2ea74cde6a094207996cf9ebabd2c94c39aaadd8`; Recipes HEAD/gitlink `2019e2cec224275bce43815c9dc0f09b13a826ca`. Checkouts inicialmente limpios. macOS, Docker rancher-desktop, proyecto bebilo-recipes. App HEAD `ed0e963a74ab7e90856e5db1d62e1058137f26e6` inspeccionada, no modificada. Sin cambios de remotos/submódulos, commits o push.

## Cambios realizados

T-01: decisiones/contrato registrados. T-02: custom en API/Domain/Firestore/OpenAPI; prompt reclasifica y amplía alérgenos, validación preserva datos e impide custom en composiciones. Schemas LLM conservan ocho valores. T-03: tests y smoke ampliados, AGENTS/README actualizados.

## Evidencia

Comprobaciones del 14 de octubre de 2026, desde recipe-services salvo indicación.

| Tarea / AC | Comando o comprobación | Resultado |
| --- | --- | --- |
| T-02–03 / AC-01–03 | `./gradlew test build installDist distTar -Penv=local --console=plain` | BUILD SUCCESSFUL; 201 tests, cero fallos/errores/omisiones; distribución construida |
| T-04 / AC-04 | `./gradlew build -Penv=cloud --console=plain` | BUILD SUCCESSFUL; tareas reutilizadas, sin integración cloud adicional |
| T-03–04 / AC-01 | Emulador demo temporal en 18080; `FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local --console=plain` | 5 pruebas SDK/HTTP/SSE, cero fallos/errores/omisiones; categorías incluyen custom |
| T-04 / AC-04 | `docker --context rancher-desktop build -t recipes-service:local .` | Imagen construida |
| T-03–04 / AC-01–04 | `DOCKER_CONTEXT=rancher-desktop python3 scripts/isolated-smoke.py` | PASS: 120 comprobaciones; incluye custom en GET, POST y reclasificación con alérgenos en steps/narrative, fallo conserva entrada; Koog/SDK/Traefik reales, proveedores simulados |
| T-04 / AC-04 | `docker --context rancher-desktop compose -p bebilo-recipes up -d --build` | Solo Recipes recreado; running, cero reinicios, inicio 09:14:09 Europe/Madrid |
| T-04 / AC-04 | `python3 scripts/local-smoke.py` | PASS: 37 comprobaciones de lectura; contrato idéntico directo/gateway, Swagger Recipes/central y configuración central con referencia a Recipes |
| T-04 / AC-04 | SHA-256 JAR local/contenedor | Iguales: `2183357e6b3c548ce3446f1153eae92203f6ee82d9705c99e321593578e60956` |
| T-05 / AC-04 | Ruby YAML, JSON schemas, AST Python, enlaces SDD y `git diff --check` en ambos repositorios | OpenAPI: nueve categorías y category requerido; schemas LLM: ocho, sin custom; sintaxis/enlaces/whitespace correctos |

Imagen desplegada `sha256:eb2252c555621bd3e976b49adcfa5c027ad22622e5a33446fcccc733ab09e08e`. Swagger central disponible en `http://api.bebilo.localhost:8088/swagger/`; contrato Recipes servido íntegro desde `/recipes/openapi.yaml`.

El primer intento de Gradle desde el agregador falló porque no existe tarea raíz test; se corrigió ejecutando desde Recipes, como exige AGENTS. No hubo fallos funcionales posteriores. Emulador auxiliar `bebilo-recipes-custom-firestore` detenido/eliminado; smoke limpia sus propios recursos.

[Informe unitario/HTTP](../../../recipe-services/build/reports/tests/test/index.html) · [Informe SDK/emulador](../../../recipe-services/build/reports/tests/integrationTest/index.html).

Revisiones finales iguales a las bases registradas: cambios locales sin commit en agregador y Recipes, incluidos nuevos documentos/tests; no commits/push ni gitlink actualizado sin commit del servicio. Otros submódulos sin modificaciones.

## Pendientes y limitaciones

Interpretación semántica de LLM real y recorrido móvil autenticado no se prueban con dobles/emulador. Sin migraciones ni escrituras de prueba en Firebase real.
