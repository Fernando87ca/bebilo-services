# Plan — Límite de imágenes por receta

Estado: completado. Fecha: 2026-10-14. [Spec](spec.md) · [Tareas](tasks.md).

## Objetivo y alcance

Aplicar el límite al agente visual y al worker de Recipes. Documentar contrato; conservar texto y modos actuales.

## Estado actual y evidencia

`visual-planning.txt` selecciona pasos relevantes sin límite. `image-plan.schema.json` no limita longitud. `validateImagePlan` exige portada única, IDs únicos/válidos y modos compatibles; un error de planificación permite Ready sin fotos. Revisiones y cambios preexistentes en [implementación](implementation.md).

## Diseño

Prompt: hasta cuatro procesos más portada. Schema: maxItems=5. Servidor: hasta cuatro instructionId no nulos, más la validación existente de una portada. Rechazar el plan excesivo antes de llamar al proveedor de imágenes o subir objetos. No recortar pasos textuales ni cambiar validación de documentos históricos.

## Fases y dependencias

1. Actualizar prompt, schema, validación y OpenAPI.
2. Verificar límites en steps/narrative con/sin portada y recursos enviados por Koog.
3. Ejecutar comprobaciones del servicio y registrar resultados.

## Validación y entrega

Desde recipe-services: test/build local, build cloud, integrationTest con emulador demo, installDist/distTar, Docker y smoke aislado. Revisar diff y enlaces documentales. No desplegar al stack habitual ni usar Firebase real.

## Preguntas para cerrar el plan

Ninguna decisión bloqueante.
