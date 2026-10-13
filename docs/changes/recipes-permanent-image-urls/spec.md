# Especificación — URLs permanentes de imágenes de Recipes

Estado: acordado. Fecha: 2026-10-13. [Plan](plan.md) · [Tareas](tasks.md).

## Objetivo y alcance

Recipes devuelve enlaces directos Firebase sin caducidad automática; redespliegue local
Rancher Desktop. No modifica la app ni crea fixtures en Firebase real.

## Requisitos

| ID | Requisito | Estado/origen |
| --- | --- | --- |
| R-01 | JPEG privados con token aleatorio de descarga; URL y referencia persistidas tras subir | Confirmado por usuario |
| R-02 | URLs en DTO de listado/detalle; retirar GET de imágenes | Confirmado por usuario |
| R-03 | Portada y fotos de pasos/párrafos opcionales; plan vacío válido | Confirmado por usuario |
| R-04 | Migración idempotente offline con inventario previo y validación de propiedad | Plan autorizado |
| R-05 | Checks funcionales y redespliegue local; evidenciar límites reales de Storage | Plan autorizado |

## Contratos y datos

[OpenAPI](../../../recipe-services/src/main/resources/openapi.yaml) conserva los DTO:
summary.coverImage y image de cada paso/párrafo son opcionales (ausencia o null).
Si existen, contienen url y altText opcional. GET listado/detalle y POST continúan con Bearer.
GET /v1/recipe/{recipeId}/images/{imageId} desaparece: 404, también bajo /recipes.
URLs apuntan a Firebase, incluyen token de descarga y no caducan automáticamente.
Firestore conserva URLs y generationImages: imageId → recipe-images/{uid}/{documentId}/{imageId}.jpg.
GET no escribe ni consulta Storage. Objetos privados, sin ACL pública, metadata
firebaseStorageDownloadTokens; conocer el enlace autoriza descarga sin identidad de cuenta.
none no genera; single permite como máximo portada; multiple permite selección sin portada.
Fallo de foto no impide Ready con texto válido. SSE no cambia.

## Aceptación

| ID | Requisito | Escenario y resultado esperado | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01/R-02 | URL codificada, token persistido, descarga sin Bearer, ruta retirada | Unitarias y smoke aislado |
| AC-02 | R-03 | Ambos estilos sin portada, plan vacío y proceso selectivo terminan Ready | Unitarias/HTTP |
| AC-03 | R-04 | Preserva tokens/metadatos, rechazo de ajenos/inconsistentes, repetición sin cambios | Unitarias, integración y CLI |
| AC-04 | R-05 | Contrato servido actualizado y servicio saludable | Builds, Docker y smoke local |

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 2026-10-13 | Confirmada | Usuario acepta que cualquiera con la URL descargue las fotos culinarias de IA |
| D-02 | 2026-10-13 | Confirmada | Sustituye GET autenticado de recipes-post-koog y propuesta de firma temporal de una hora |
| D-03 | 2026-10-13 | Confirmada | Portada opcional; IA selecciona imágenes útiles; app sin renovación temporal |
