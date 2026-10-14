# Implementación — Límite de imágenes por receta

Estado: completado. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

Fecha: 2026-10-14. Agregador `2ea74cde6a094207996cf9ebabd2c94c39aaadd8`; Recipes `2019e2cec224275bce43815c9dc0f09b13a826ca`. App y otros servicios no modificados. Se conservan cambios previos sin commit del tema recipe-custom-ingredients en los índices documentales, AGENTS/README, mappers, DTO/domain, composition, OpenAPI, worker, fixtures, smoke y CustomIngredientsTest. Este cambio también modifica worker/OpenAPI sin sustituir esas ediciones.

## Cambios realizados

Prompt y esquema visual limitados; validateImagePlan exige máximo cuatro instrucciones además de la portada única existente. OpenAPI describe el límite solo para recetas nuevas. Tests del worker verifican número real de llamadas/subidas y persistencia, con ambos estilos y cabecera opcional; Koog verifica el schema y prompt enviados al transporte simulado.

## Evidencia

| Fecha | Tarea / AC | Directorio y comando o comprobación | Resultado |
| --- | --- | --- | --- |
| 2026-10-14 | T-02 / AC-01/02/03 | Agregador: `./gradlew -p recipe-services test -Penv=local`; servicio: `./gradlew build -Penv=local` | 209 tests, sin fallos ni omitidos. Incluye ocho escenarios nuevos del presupuesto y seis escenarios Koog con comprobación del recurso enviado. |
| 2026-10-14 | T-03 | recipe-services: `./gradlew build -Penv=cloud` | BUILD SUCCESSFUL; tareas reutilizadas UP-TO-DATE. |
| 2026-10-14 | T-03 | recipe-services: `FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local` | 5 tests SDK/emulador, sin fallos ni omitidos; contenedor demo propio retirado al terminar. |
| 2026-10-14 | T-03 | recipe-services: `./gradlew installDist distTar -Penv=local`, `docker build -t recipes-service:local .` | Distribución e imagen construidas correctamente. |
| 2026-10-14 | T-03 | recipe-services: `python3 scripts/isolated-smoke.py` | PASS: 120 checks; servicio real, Koog, SDK Firebase, emulador Firestore y Traefik; dependencias externas simuladas. Stack aislado limpiado. |
| 2026-10-14 | T-03 | `git diff --check`, `git -C recipe-services diff --check`, enlaces relativos de los cuatro documentos | Sin errores; enlaces resueltos. |

## Despliegue Rancher Desktop

- `recipe-services`: `./gradlew distTar -Penv=local` y `docker --context rancher-desktop compose -p bebilo-recipes up -d --build recipes-service`: correctos; contenedor `bebilo-recipes-recipes-service-1` recreado y running.
- Imagen activa: `sha256:ade61bf4d28e38dac003e7ebc86cb03d83ea18e0516dddba4e218d6fe0e06906`.
- JAR local y dentro del contenedor con SHA-256 coincidente: `55861b8ac53a2a55b49664a153d1f4edf05bbc0bc0f06f0d74dd991a4f127c83`.
- `python3 scripts/local-smoke.py`: PASS, 37 comprobaciones del despliegue. Health, OpenAPI (coincidencia exacta con contrato actualizado), Swagger y autenticación verificadas directamente y vía gateway; sin crear recetas ni realizar llamadas LLM reales.

## Pendientes y limitaciones

Desplegado en el stack habitual de Rancher Desktop por petición del usuario el 2026-10-14. Las pruebas de IA y Storage usan dobles; no se generan fotos reales ni se accede a Firebase real.
