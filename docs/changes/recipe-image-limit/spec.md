# Especificación — Límite de imágenes por receta

Estado: completado. Fecha: 2026-10-14. [Plan](plan.md) · [Tareas](tasks.md).

## Objetivo y alcance

Limitar la generación a cuatro imágenes de instrucciones y una cabecera opcional por receta nueva. No modificar recetas persistidas.

## Requisitos

| ID | Requisito | Estado/origen |
| --- | --- | --- |
| R-01 | Máximo cuatro imágenes de pasos o párrafos y una portada: cinco en total. Sin portada siguen siendo cuatro de instrucciones. | Confirmado por petición del usuario |
| R-02 | Conservar modos none y cover y la opcionalidad de todas las imágenes. Un plan excesivo se rechaza antes de generar o subir fotos; el texto puede terminar Ready. | Comportamiento existente de planes inválidos aplicado al límite |

## Contratos y datos

POST interno `/v1/recipe`, público `/recipes/v1/recipe`, conserva Bearer, request, respuesta 200 y estados Created/Loading/Ready/Error. El límite afecta al worker; no cambia los DTOs ni las rutas. [OpenAPI](../../../recipe-services/src/main/resources/openapi.yaml) documenta el límite de nuevas recetas. Se conserva persistencia por cuenta `users/{uid}/recipes/{documentId}` sin migración ni cambios en GET.

## Aceptación

| ID | Requisito | Escenario y resultado esperado | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01 | Cuatro instrucciones y portada generan/suben cinco imágenes; sin portada generan cuatro. | Worker con dobles para steps y narrative |
| AC-02 | R-01/R-02 | Cinco instrucciones, con o sin portada, no llaman a generación ni Storage y conservan Ready textual. | Worker con dobles para ambos estilos |
| AC-03 | R-01 | El agente recibe instrucciones explícitas y schema maxItems=5. | Test Koog con transporte simulado |

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 2026-10-14 | Acordada | Petición del usuario: máximo 5 imágenes (4 + cabecera). |
| D-02 | 2026-10-14 | Adoptada | Mantener rechazo de planes inválidos sin reintentos ni reparación LLM; todas las fotos siguen siendo opcionales. |
