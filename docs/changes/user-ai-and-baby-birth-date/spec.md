# Especificación — IA por usuario y nacimiento

Estado: completado; [evidencia](implementation.md). Fecha: 2026-10-14. [Plan](plan.md) · [Tareas](tasks.md).

## Requisitos y aceptación confirmados

- R-01 / AC-01: IA en users/{uid}/aiConfigurations/default, aislamiento por UID, defaults y reemplazo conservados; sin escrituras en perfil.
- R-02 / AC-02: birthDate obligatorio ISO YYYY-MM-DD sustituye ageMonths en alta, edición, listado y Firestore de Babies. Fecha real hasta hoy Europe/Madrid. Validar roundtrip, idempotencia, ausencia, formato, fecha imposible y futura.
- R-03 / AC-03: Cada solicitud calcula meses completos con reloj inyectable y referencia única Europe/Madrid. Contexto persiste nacimiento, referencia y edad derivada sin tokens; worker conserva snapshot. Probar aniversario, recién nacido, fin de mes, bisiesto, medianoche y días distintos.
- R-04 / AC-04: Conservar selector móvil Android/iOS, edad visible con fecha local y creación mediante babyIds. Sin migración ni compatibilidad.
- R-05 / AC-05: Tests/builds local/cloud, emulador y smoke; desplegar IA/Babies/Recipes en Rancher Desktop Compose y verificar imágenes, health y contratos directo/gateway.

## Contratos

[Babies OpenAPI](../../../baby-services/src/main/resources/openapi.yaml), [IA OpenAPI](../../../aiconfig-services/src/main/resources/openapi.yaml), [Recipes OpenAPI](../../../recipe-services/src/main/resources/openapi.yaml). Rutas y autenticación existentes conservadas. Entrada inválida Babies: 400; documento Baby corrupto: 503 de almacenamiento; dependencia inválida Recipes: error antes de crear receta. GET Recipes no recalcula históricos.

## Decisiones

Usuario acordó subcolección singleton default y cálculo al solicitar en Europe/Madrid; autorizó implementación y despliegue. Meses completos: 31 enero–28 febrero=0, al 1 marzo=1; 29 febrero–28 febrero siguiente=11, al 1 marzo=12. Datos vacíos: sin migración ni fallback. Conservar trabajo local previo.
