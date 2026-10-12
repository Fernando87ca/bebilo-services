# Especificación — Categorías de ingredientes

Estado: completado; backend validado y desplegado. Fecha: 2026-10-12.
[Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Ambos GET de Recipes publican la categoría persistida de cada ingrediente. Fuente:
[contrato móvil](/Users/foca/AndroidStudioProjects/bebilo/docs/changes/app-recipes-implementation/backend-requirements.md),
sección «Categoría obligatoria y placeholders». Usuario solicita implementar el plan completo,
incluido redespliegue en Rancher Desktop. No incluye creación de recetas, clasificación visual
ni migración: el usuario confirma que todavía no hay información almacenada.

## Requisitos

| ID | Requisito | Estado/origen |
| --- | --- | --- |
| R-01 | category obligatorio, string enum exacto, sin null ni default en cada ingrediente | Confirmado; R-38 móvil |
| R-02 | Firestore → Domain → DTO conserva category en ambos GET y todos los estados | Confirmado; contrato móvil |
| R-03 | Categoría ausente/null/desconocida/de tipo incorrecto causa 500 INTERNAL_ERROR, sin listado parcial ni reparación | Confirmado; contrato y plan autorizado |
| R-04 | catalogId sigue opcional string, numérico como texto u opaco; libres requieren category | Confirmado; contrato móvil |
| R-05 | OpenAPI, pruebas aisladas, documentación y despliegue local actualizados | Confirmado; plan autorizado |

## Contratos y datos

[OpenAPI de Recipes](../../../recipe-services/src/main/resources/openapi.yaml) es la referencia
HTTP ejecutable. GET públicos `/recipes/v1/recipes` y `/recipes/v1/recipe/{recipeId}`,
internos `/v1/recipes` y `/v1/recipe/{recipeId}`. Bearer verificado por Auth antes de Firestore;
envelopes, estados HTTP e identidad por UID se conservan.

Persistencia: `users/{uid}/recipes/{documentId}`; cada `ingredients[]` debe incluir category
como string exacto: `vegetables`, `fruit`, `protein`, `dairy`, `cereal`, `seeds`,
`oil_and_fats`, `sweet`. No default, comodín ni clasificación por catalogId/nombre.
Aplica a ingredientes libres y recetas Loading/Ready/Error. catalogId no cambia de tipo,
no es obligatorio ni se renumera. No hay cambio de colección, índice ni ruta.

Los GET conservan lo guardado, sin escrituras ni consultas a IA, AIconfig, Babies o Profile.
Un documento inválido hace fallar su consulta y el listado completo con 500 INTERNAL_ERROR;
no se corrige ni se omite. Sin recetas sigue siendo 200 `{"recipes":[]}`.
El futuro productor debe clasificar y persistir category antes de guardar una receta.

## Aceptación

| ID | Requisitos | Resultado esperado | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01–02 | Ocho valores exactos conservados en ambos GET y los tres estados | Mapper, HTTP, SDK/emulador y gateway aislado |
| AC-02 | R-03 | Categorías inválidas dan 500, sin biblioteca parcial ni cambio del documento | Mapper, HTTP/SDK y smoke aislado |
| AC-03 | R-04 | catalogId omitido/null/numérico como texto/opaco preservado; ninguna inferencia desde nombre/ID | HTTP y SDK/emulador |
| AC-04 | R-05 | Tests, builds local/cloud, distribución, Docker, smoke y contrato servido correctos | AGENTS del servicio y despliegue |
| AC-05 | R-02–05 | Aislamiento, autenticación, IDs opacos e igualdad listado/detalle siguen correctos | Regresiones existentes |

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 2026-10-12 | Confirmada | Usuario confirma ausencia de datos; adoptar contrato obligatorio sin migración ni borrado |
| D-02 | 2026-10-12 | Confirmada | Usuario selecciona redesplegar Recipes en Rancher Desktop |
| D-03 | 2026-10-12 | Confirmada | Enums separados Domain/DTO y mapeo explícito; plan solicitado |
