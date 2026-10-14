# Cambios

| Cambio | Estado | Documentos |
| --- | --- | --- |
| API y Firebase | Pendiente de validación | [Spec](api-firebase-alignment/spec.md) · [Plan](api-firebase-alignment/plan.md) · [Tareas](api-firebase-alignment/tasks.md) · [Implementación](api-firebase-alignment/implementation.md) · [Revisión](api-firebase-alignment/review.md) |
| Onboarding Auth local | Completado | [Spec](auth-onboarding-local/spec.md) · [Plan](auth-onboarding-local/plan.md) · [Tareas](auth-onboarding-local/tasks.md) · [Implementación](auth-onboarding-local/implementation.md) |

| Consulta de recetas GET | Pendiente de validación móvil | [Spec](recipes-get/spec.md) · [Plan](recipes-get/plan.md) · [Tareas](recipes-get/tasks.md) · [Implementación](recipes-get/implementation.md) |

| Categorías de ingredientes | Completado | [Spec](recipe-ingredient-categories/spec.md) · [Plan](recipe-ingredient-categories/plan.md) · [Tareas](recipe-ingredient-categories/tasks.md) · [Implementación](recipe-ingredient-categories/implementation.md) |

Para un nuevo cambio, copiar [_template](_template/plan.md) a una carpeta con nombre estable y añadir una fila. Seguir el [proceso](../spec-driven-development.md). No modificar las plantillas para registrar la ejecución de un cambio concreto.

- [Creación asíncrona de recetas con Koog](recipes-post-koog/plan.md): **pendiente de validación**, implementación y checks locales completos; bucket real pendiente.

- [Prompts exclusivos de cocina infantil](recipe-child-only-prompts/plan.md): recursos reforzados y validación local.

- [SSE de estados de Recipes](recipes-sse/plan.md): **completado**, observación por cuenta y snapshot inicial; [evidencia](recipes-sse/implementation.md) con tests, emulador y gateway aislado.

- [URLs permanentes de imágenes de Recipes](recipes-permanent-image-urls/plan.md): implementado y redesplegado localmente; pendiente de validación real de Storage.

- [Ingredientes manuales custom](recipe-custom-ingredients/plan.md): completado; aceptación, reclasificación y alérgenos validados y redesplegados en Recipes.

- [Límite de imágenes por receta](recipe-image-limit/plan.md): **completado**, máximo cuatro instrucciones y una portada opcional.

- [Agentes de alérgenos y alternativas](recipe-allergen-agents/plan.md): completado con proveedor LLM simulado; análisis obligatorio, sustituciones y omisiones verificadas.
