# Especificación — creación de recetas con Koog

Estado: pendiente de validación real de Storage. Fecha: 2026-10-12. [Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Crear recetas por cuenta Firebase mediante POST y generar su contenido fuera de la petición HTTP, con Koog integrado en Recipes. La app móvil, recuperación, reintentos, idempotencia, checkpoints y habilitación de facturación/Storage quedan fuera de alcance.

## Requisitos acordados

| ID | Requisito | Origen |
| --- | --- | --- |
| R-01 | POST autenticado consulta AI Configuration y Babies antes de crear; devuelve 200 Created sin esperar al LLM. | Plan autorizado por el usuario |
| R-02 | Persistir solicitud y contexto fijo sin Bearer; identidad pública independiente del documentId. | Plan autorizado |
| R-03 | Ciclo Created → Loading → Ready/Error en coroutines de aplicación, dos trabajos simultáneos por defecto. | Plan autorizado |
| R-04 | Conservar ingredientes iniciales y métodos; añadir ingredientes compatibles, considerar todas las restricciones de los bebés. | Decisión del usuario |
| R-05 | Agentes de composición y planificación visual con modelo exacto seleccionado; prompts españoles empaquetados y prefijo cacheable. | Plan autorizado |
| R-06 | none sin fotos; single portada; multiple portada y procesos relevantes, asociados a IDs existentes. | Decisión del usuario |
| R-07 | Storage privado y GET de imagen autenticado; fallos de imágenes no impiden Ready. | Decisión del usuario |
| R-08 | Desarrollo: OPENAI_API_KEY en local.properties ignorado. Producción: Secret de Google por variable o archivo. Nunca versionar ni registrar. | Corrección explícita del usuario, 2026-10-12 |

## Contratos y datos

Referencia HTTP ejecutable: [OpenAPI Recipes](../../../recipe-services/src/main/resources/openapi.yaml).

- POST interno `/v1/recipe`, público `/recipes/v1/recipe`; Bearer Firebase validado por Auth. Body: babyIds, ingredients, cookingMethods; opcionales title, servings, additionalInstructions. Ingredientes con categoría explícita y alérgenos. Cada POST crea una receta nueva.
- 200 devuelve RecipeDto con Created. Errores `{code,message}`: 400 solicitud inválida; 401/403 identidad; 404 bebé inexistente/ajeno; 502 respuesta inválida de dependencia; 503 indisponibilidad; 500 fallo interno.
- GET de biblioteca/detalle lee los cuatro estados y permite instrucciones vacías salvo Ready, sin consultar otros servicios. Mantiene validación estricta de categorías y documentos inválidos.
- Documentos en `users/{uid}/recipes/{documentId}`: campos de RecipeDto más generationContext y generationImages internos. summary.id opaco generado en servidor. createdAt y updatedAt UTC del servidor. generationContext conserva solicitud, configuración y bebés al crear; nunca credenciales.
- generationImages enlaza imageId a objeto `recipe-images/{uid}/{documentId}/{imageId}.jpg`. GET `/v1/recipe/{recipeId}/images/{imageId}` valida identidad y propietario, devuelve JPEG con Cache-Control private,no-store; no entrega tokens de descarga.
- Ready exige contenido textual válido. Fotos omitidas por fallos permanecen null. Error conserva contenido disponible. Una caída de Firestore puede impedir guardar Error: solo se registra el código de fallo.

## Aceptación

| ID | Requisitos | Resultado verificable |
| --- | --- | --- |
| AC-01 | R-01,R-02 | POST Created y GET pendiente, contexto e ingredientes preservados, UID aislado. |
| AC-02 | R-03 | HTTP/cancelación independientes; dos activos y tercero Created; un fallo no cancela otro. |
| AC-03 | R-04,R-05 | Modelo/estilo exactos, esquema estricto, prefijo estable, invariantes y Error ante resultado inválido. |
| AC-04 | R-06,R-07 | Portada y procesos selectivos, fallos parciales Ready, acceso privado y persistencia de referencias tras subir. |
| AC-05 | R-08 | Secret ignorado, permisos 0600, distribución/imagen sin credencial. |
| AC-06 | Todos | Checks de Recipes, emulador y smoke Docker aislado sin datos en Firebase real. |

## Decisiones

Todas acordadas el 2026-10-12 en conversación: Koog dentro de Recipes; consulta Babies por IDs; texto e imágenes reales; ingredientes ampliables y métodos fijos; fotos fallidas permiten Ready; descarga autenticada; multiple ilustra procesos relevantes. Bucket real pendiente de habilitar por facturación, confirmado por el usuario.

Actualización R-08: el usuario sustituye el Secret de Compose local por local.properties; se conserva soporte para Secret de producción mediante OPENAI_API_KEY_FILE o OPENAI_API_KEY.

## Actualización 2026-10-13

La descarga autenticada de imágenes se sustituye por [URLs permanentes Firebase](../recipes-permanent-image-urls/spec.md), autorizadas por el usuario. Este documento conserva el diseño anterior como antecedente.

Actualización 2026-10-14: [agentes de alérgenos](../recipe-allergen-agents/spec.md) amplía R-04/R-05 con herramientas nativas, sustitución u omisión de incompatibles y verificación final obligatoria. Se conserva este historial.
