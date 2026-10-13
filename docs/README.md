# Documentación de Bebilo Services

- [Proceso de spec-driven development](spec-driven-development.md): organización, estados y fuente de verdad.
- [Índice de cambios](changes/README.md): planes actuales y futuros.
- [Alineación API/Firebase](changes/api-firebase-alignment/plan.md): implementación backend desplegada localmente y pendiente de validación autenticada y de habilitar Firebase Storage.
- [Onboarding autoritativo en Auth](changes/auth-onboarding-local/plan.md): custom claim, endpoint idempotente y AI Configuration validados y desplegados en Rancher Desktop.

- [Consulta de recetas GET](changes/recipes-get/plan.md): implementación del servicio de lectura por cuenta y despliegue local.

- [Categorías de ingredientes](changes/recipe-ingredient-categories/plan.md): contrato obligatorio de Recipes validado y redesplegado localmente.

Los planes transversales de la API viven en este repositorio agregador. El código y OpenAPI pertenecen a cada servicio; la documentación de pantallas permanece en la app.

- [Creación asíncrona de recetas con Koog](changes/recipes-post-koog/plan.md): **pendiente de validación**, implementación y checks locales completos; bucket real pendiente.

- [SSE de estados de Recipes](changes/recipes-sse/plan.md): observación por cuenta con snapshot inicial y cambios de estado.

- [URLs permanentes de imágenes de Recipes](changes/recipes-permanent-image-urls/plan.md): implementado y redesplegado localmente; pendiente de validación real de Storage.
