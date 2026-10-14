# Plan — Ingredientes manuales custom

Estado: completado; backend validado y redesplegado. Fecha: 2026-10-14. [Spec](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y estado actual

API, Domain y Firestore aceptaban ocho categorías; app ya contiene custom. Composición exigía identidad de categorías y alérgenos de todos los ingredientes. Swagger central carga el OpenAPI servido por Recipes. Checkouts inicialmente limpios; submódulo conservado.

## Diseño y fases

1. Ampliar enum Domain/DTO y mappers con custom; ampliar OpenAPI y ejemplo. GET sin escrituras.
2. Prompt distingue entrada manual; schemas de salida mantienen ocho categorías. Validación permite reclasificación y ampliación de alérgenos solo para custom, sin modificar otros datos indicados.
3. Tests HTTP, worker, SDK/emulador y smoke; documentación e índices SDD.
4. Builds local/cloud, distribución, Docker; redesplegar únicamente Recipes en Rancher Desktop y comprobar contrato directo/gateway/Swagger.
5. Registrar evidencia real y revisiones. Sin decisiones abiertas ni cambios en otros servicios.

## Validación

Seguir AGENTS del servicio: test, build local/cloud, integrationTest con emulador demo, installDist/distTar, Docker, isolated-smoke y local-smoke. Sin escrituras de prueba en Firebase real. Dobles y emulador no prueban interpretación semántica de un LLM real ni recorrido móvil autenticado.
