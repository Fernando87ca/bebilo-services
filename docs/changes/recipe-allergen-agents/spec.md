# Especificación — Agentes de alérgenos y alternativas

Estado: completado; validación funcional con proveedor LLM simulado. Fecha: 2026-10-14. [Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Generar nuevas recetas con análisis obligatorio de todos los ingredientes, sustitución u omisión justificada de incompatibles y verificación final. Recipes y agregador; sin revisión del histórico ni cambios en Babies o AI Configuration.

## Requisitos y aceptación

| ID | Requisito confirmado | Aceptación |
| --- | --- | --- |
| R-01 | Dos agentes especializados como herramientas nativas Koog; fases obligatorias | Agentes reales con frontera HTTP simulada; sin prueba final no hay Ready |
| R-02 | Alérgenos de todos los ingredientes, sin depender de restricciones ni datos de entrada | Ingrediente ordinario/custom/añadido con entrada vacía; resumen exacto |
| R-03 | Compatibilidad conjunta con todas las restricciones de todos los bebés | Sustituir conflictos, analizar alternativas y rechazar conflictos finales |
| R-04 | Omitir solo cuando análisis y alternativas confirman viabilidad | Omission prescindible válida; ingrediente esencial o desacuerdo produce Error |
| R-05 | Trazabilidad estructurada y notas de adaptación | Snapshots originales, índice base cero, reemplazos finales sin catalogId |
| R-06 | POST asíncrono y Error conservando solicitud inicial ante fallo de análisis | Created/Loading/Ready/Error y ausencia de imágenes antes de verificación |
| R-07 | Lectura histórica sin reparación | Campo ausente se lee vacío; GET no escribe ni analiza |

## Contratos y datos

[OpenAPI de Recipes](../../../recipe-services/src/main/resources/openapi.yaml) mantiene las rutas y Bearer actuales: POST `/recipes/v1/recipe`, GET `/recipes/v1/recipes`, GET `/recipes/v1/recipe/{recipeId}`; rutas internas sin `/recipes`. POST responde 200 Created sin esperar al LLM; errores HTTP y SSE actuales se conservan.

Persistencia por cuenta en `users/{uid}/recipes/{documentId}`. Nuevo `ingredientChanges`: action replacement/omission, originalIngredientIndex base cero en solicitud, snapshot originalIngredient, replacementIngredient final o null y reason. Campo histórico ausente equivale a lista vacía sin escritura. Sin índices ni colecciones nuevos. Prueba de seguridad efímera no serializada.

Alérgenos conservan formato code/name, con 14 grupos UE canónicos y códigos estables adicionales; milk se conserva. Los declarados en ingredientes mantenidos no se eliminan ni renombrarán. El resumen solo incluye los ingredientes efectivos. No se infieren trazas de fábrica sin información.

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 2026-10-14 | Confirmada | Usuario: 14 grupos UE y otros relevantes; todas las incompatibilidades; solo recetas nuevas |
| D-02 | 2026-10-14 | Confirmada | Usuario: cambios estructurados y omisión si es prescindible |
| D-03 | 2026-10-14 | Confirmada | Usuario: implementar el plan completo |

Sustituye la conservación exacta de alérgenos no custom y la prohibición absoluta de cambiar ingredientes incompatibles de [custom](../recipe-custom-ingredients/spec.md). Mantiene reclasificación custom, métodos, raciones, identidad y límites de imágenes. El título solicitado solo puede adaptarse si los cambios lo vuelven incorrecto. No hay reintentos del proveedor ni reparación iterativa.
