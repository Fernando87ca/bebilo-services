# Plan — creación asíncrona de recetas

Estado: pendiente de validación real de Storage. Fecha: 2026-10-12. [Especificación](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Estado inicial

Agregador `916eaa7646e5b665b6c1080224a1e6fbe5f60832`; Recipes `afd18b61c4918bd60a3cc9b7e07e73ea154eefd2`; AI Configuration `2445451cae8b9c8c86b705e157b916d54c0cc84e`. Checkouts inicialmente limpios. Recipes solo tenía dos GET y exigía instrucciones completas en Loading/Error. AI Configuration ya expone instructionStyle, photoMode y modelId. Babies ya expone edad y restricciones. No se cambian contratos de esos servicios.

## Implementación

1. Añadir Created, validar contenido pendiente y definir DTO de creación separado de Domain.
2. Incorporar clientes autenticados de configuración/Babies, creación y actualización Firestore, contexto interno y despacho independiente del HTTP.
3. Añadir Koog 1.3.0, agentes funcionales de una llamada sin reparación/reintentos, prompts y esquemas empaquetados, Responses API y extensión de transporte para caché explícita.
4. Guardar texto antes de fotos; generar JPEG, subir a Storage privado y añadir ruta autenticada. Fotos fallidas son opcionales.
5. Configurar clave en local.properties y URLs en Compose, conservar soporte para Secret de Google en producción, actualizar OpenAPI/guías y ampliar tests, emulador y smoke.

## Límites y validación

Límite configurable de dos trabajos activos, espera en memoria y sin recuperación tras reinicio. Clientes HTTP tienen timeouts finitos. SDKs/LLM permanecen en data; casos de uso y dispatcher en domain; API y DI independientes. Bucket real pendiente: validar Storage con dobles y registrar validación real como pendiente, sin crear datos de prueba en Firebase real.

Comprobaciones desde recipe-services: test; build local/cloud; integrationTest con emulador demo; installDist/distTar; docker build; isolated-smoke.py. Comprobar links, diff, esquemas/prompt y ausencia de secretos en artefactos. Registrar resultados reales en implementation.md.

Referencias: [Koog structured output](https://docs.koog.ai/structured-output/), [caché Responses](https://developers.openai.com/api/docs/guides/prompt-caching), [GPT Image Flare](https://developers.openai.com/api/docs/models/gpt-image-2.5-flare).
