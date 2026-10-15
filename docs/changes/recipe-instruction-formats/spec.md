# Especificación — Recetas narradas y pasos concisos

Estado: completado; validación funcional con proveedor LLM simulado. Fecha: 2026-10-14. [Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Requisitos acordados

- R-01: Narrativa conserva paragraphs con id, text e imagen opcional, explicados en prosa clara.
- R-02: Steps agrupa procesos con id, objective obligatorio no blanco y tasks ordenadas, no vacías y con textos no blancos. Eliminar text y title; conservar durationMinutes e image opcionales.
- R-03: instructions.type selecciona el DTO móvil, fijado por instructionStyle al crear. La app numera tasks desde 1 dentro de cada bloque y muestra su imagen al final. Cambiar configuración no reinterpreta recetas guardadas.
- R-04: Dos agentes Koog especializados con reglas comunes; ejecutar solo el del estilo configurado. Pasos evita introducciones y repeticiones sin perder cantidades, tiempos, textura ni indicaciones necesarias. Conservar análisis de alérgenos, alternativas y verificación final.
- R-05: Ready exige contenido completo; Created/Loading/Error admiten instrucciones vacías. Fotos opcionales: máximo cuatro imágenes de instrucciones y portada opcional.
- R-06: Validar y desplegar Recipes en Rancher Desktop; verificar health, imagen, smoke y contrato servido.

## Contratos y persistencia

[OpenAPI Recipes](../../../recipe-services/src/main/resources/openapi.yaml) es la referencia HTTP. POST /v1/recipe y GET de detalle/lista entregan el nuevo contrato. Rutas, autenticación, request, estados HTTP y SSE id/status se conservan.

Firestore users/{uid}/recipes/{documentId} guarda el mismo formato discriminado. Los IDs enlazan imágenes al bloque completo. No se admiten antiguos bloques steps con text/title. No hay migración ni borrado automático.

## Aceptación

- AC-01: HTTP y roundtrip Firestore conservan objetivo, orden de tareas, IDs e imágenes; narrativa mantiene prosa e imágenes.
- AC-02: Objetivos/tareas vacíos, tipos inválidos, IDs duplicados y estilo incorrecto se rechazan; generación inválida termina Error antes de imágenes.
- AC-03: Transporte Koog recibe prompt/schema del estilo seleccionado y modelo solicitado; seguridad y verificaciones conservadas.
- AC-04: Checks funcionales y smoke aislado pasan. Rancher usa la imagen verificada y sirve el OpenAPI actualizado directamente y por gateway.

## Decisiones

Confirmadas en conversación el 2026-10-14: DTOs discriminados; pasos con objetivo y tareas; narrativa existente válida; sin compatibilidad porque el usuario ha borrado la colección de recetas; implementación y despliegue autorizados. Móvil y AI Configuration fuera de alcance. Sin decisiones pendientes.
