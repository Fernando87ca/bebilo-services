# Plan — Agentes de alérgenos y alternativas

[Spec](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Estado inicial

Koog 1.3.0 realiza composición sin herramientas. El resumen agrega alérgenos recibidos y validateComposition exige alérgenos exactos para ingredientes no custom. Babies aporta restrictions como texto. Cambios locales previos de custom e imágenes deben conservarse.

## Implementación

- Principal propone ingredientes y roles culinarios sin publicar el borrador.
- Herramienta nativa analyze_allergens ejecuta agente especializado con salida estructurada y cobertura exacta de ingredientes y conflictos por índice de bebé/restricción.
- find_ingredient_alternatives propone una sola tanda; servidor impone cobertura exacta de conflictos, reemplazos sin catalogId y omisión avalada por ambos agentes.
- Analizar de nuevo lista ajustada, componer con ingredientes congelados y verificar receta completa. Prueba interna vinculada al contenido impide Ready sin esas fases.
- Persistir ingredientChanges y exponer en GET/POST; compatibilidad histórica mediante lista vacía sin migración. Imágenes siguen al análisis final.
- Prompts separados, modelo de AI Configuration, métricas de fase sin contenido personal ni errores del proveedor; cerrar agentes por ejecución.

## Validación

Unitarias y HTTP con frontera OpenAI simulada; SDK Firestore con emulador local; builds local/cloud, distribución, Docker y smoke aislado. La evidencia distinguirá estas comprobaciones de una validación real de razonamiento LLM.
