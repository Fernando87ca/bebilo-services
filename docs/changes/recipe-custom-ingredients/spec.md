# Especificación — Ingredientes manuales custom

Estado: completado; backend validado y redesplegado. Fecha: 2026-10-14. [Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Aceptar ingredientes manuales en Recipes y desplegar el contrato en Swagger local. Solo Recipes y documentación del agregador; sin cambios móviles ni migraciones.

## Requisitos

| ID | Requisito | Origen |
| --- | --- | --- |
| R-01 | POST acepta custom; persistencia y GET conservan el valor explícito en todos los estados | Plan autorizado por usuario |
| R-02 | Composición reclasifica custom entre ocho categorías alimentarias y añade alérgenos sin eliminar/modificar los declarados | Decisiones del usuario |
| R-03 | Conserva nombre, orden, catalogId y cantidades/unidades/preparaciones indicadas; ingredientes no custom conservan categoría/alérgenos | Plan autorizado |
| R-04 | Ingredientes no identificables/incompatibles siguen la regla de solicitud incompatible; fallo conserva ingredientes iniciales y termina Error | Plan autorizado |
| R-05 | OpenAPI, prompts, documentación, tests y despliegue alineados | Plan autorizado |

## Contratos y datos

[OpenAPI](../../../recipe-services/src/main/resources/openapi.yaml) es la referencia HTTP. POST público `/recipes/v1/recipe`, GET `/recipes/v1/recipes` y `/recipes/v1/recipe/{recipeId}` (internos sin prefijo `/recipes`), Bearer Firebase y UID resuelto por Auth. POST responde 200 Created sin esperar al LLM; errores y rutas existentes conservados. name contiene el texto manual, category incorpora custom; catalogId sigue opcional. Esquemas de salida LLM conservan ocho categorías, sin custom. Datos en `users/{uid}/recipes/{documentId}`, sin nuevos campos/índices/colecciones. Created/Loading/Error pueden conservar custom; los GET pueden leer cualquier categoría válida en cualquier estado sin inferencia ni reparación. Composición válida nunca devuelve custom, tampoco en añadidos. Resumen de alérgenos calculado desde ingredientes resultantes. Sin nuevos reintentos ni recuperación.

## Aceptación

| ID | Requisitos | Escenario | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01 | POST conserva entrada manual; ambos GET conservan nueve categorías y todos los estados | HTTP, SDK/emulador, smoke |
| AC-02 | R-02–03 | Custom reclasificado con alérgenos ampliados; alteraciones no permitidas rechazadas | Tests de worker en steps/narrative |
| AC-03 | R-04 | Composición inválida termina Error conservando entrada | Worker y smoke aislado |
| AC-04 | R-05 | Checks completos y contrato servido idéntico; Swagger central accesible | Builds, smoke local y despliegue |

## Decisiones

| ID | Fecha | Estado | Decisión |
| --- | --- | --- | --- |
| D-01 | 2026-10-14 | Confirmada | Usuario selecciona reclasificación durante generación |
| D-02 | 2026-10-14 | Confirmada | Usuario selecciona completar alérgenos sin eliminar los declarados |
| D-03 | 2026-10-14 | Confirmada | Usuario autoriza implementar plan completo y redesplegar Recipes en Rancher Desktop |

La obligación anterior de conservar todas las categorías/alérgenos se sustituye únicamente para ingredientes iniciales custom. No modifica el historial del cambio anterior.

Actualización 2026-10-14: [agentes de alérgenos](../recipe-allergen-agents/spec.md) sustituye la conservación exacta de alérgenos ordinarios y permite cambios de ingredientes incompatibles con trazabilidad. El historial anterior se conserva.
