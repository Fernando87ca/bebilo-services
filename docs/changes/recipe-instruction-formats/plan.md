# Plan — Recetas narradas y pasos concisos

Estado: completado; validación funcional con proveedor LLM simulado. Fecha: 2026-10-14. [Spec](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Estado inicial

Agregador 5a323a54985ac61c4b1c12896d184cab06c8d1f7; Recipes efd1bf14acb821fbaa9d87450376a844f939075a. Checkouts limpios. Steps usaba text/title; ambos estilos compartían prompt. AI Configuration ya admite steps/narrative. Rancher usa proyecto Compose bebilo-recipes.

## Diseño y fases

1. Actualizar DTO, dominio, mappers, validación, schema y OpenAPI a objective/tasks, conservando narrativa, estados e imágenes.
2. Separar composición en dos agentes Koog seleccionados por estilo con reglas comunes y prompts propios; conservar seguridad.
3. Adaptar fixtures y smoke; comprobar contrato, orden, imágenes, contenido inválido y selección del agente.
4. Ejecutar comprobaciones funcionales y desplegar la imagen final en Rancher Desktop, verificando health y OpenAPI.

## Validación y entrega

Desde recipe-services: test, build local/cloud, integrationTest con emulador demo en 127.0.0.1:18080, installDist/distTar, construir recipes-service:local y ejecutar smoke aislado. Desplegar con docker --context rancher-desktop compose -p bebilo-recipes up -d --no-build --no-deps recipes-service. Esperar health y ejecutar scripts/local-smoke.py; registrar imagen, resultados y revisiones.

Colección vacía confirmada: sin migración ni soporte legacy. No escribir pruebas en Firebase real. No alterar remotos, submódulos ni crear commits implícitamente. Backend y despliegue únicamente.
