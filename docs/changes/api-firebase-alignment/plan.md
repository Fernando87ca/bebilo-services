# Plan — Alinear API, Firebase y servicios

Fecha: 8 de octubre de 2026. Estado: **pendiente de validación**. Identificador: `api-firebase-alignment`. [Especificación](spec.md) · [Tareas](tasks.md) · [Implementación](implementation.md) · [Revisión inicial](review.md).

## Objetivo y alcance

Actualizar únicamente el backend: Auth, Profile y Babies; crear `aiconfig-services`; integrar Gradle, submódulos, Traefik y Swagger. La app en `/Users/foca/AndroidStudioProjects/bebilo` se usa como contrato observado pero no se modifica. Firestore parte vacío; Firebase Auth conserva cuentas y no se migran documentos anteriores.

## Cambios

1. Auth amplía `/user-info` con UID sin retirar email.
2. Profile pasa a GET/POST/PUT `/user`, persiste por UID y gestiona JPEG privados versionados con compensación.
3. Babies mantiene rutas, adopta UUID del cliente, elimina `parentName` y hace creación/selección/borrado transaccionales.
4. IA se implementa en el repositorio `bebilo-ia-config`, ruta pública `/aiconfig`, puerto 8085 y colección propia.
5. Cada servicio actualiza código, OpenAPI, instrucciones y tests; el agregador registra el submódulo, `includeBuild` y Swagger.

## Orden y entrega

Aplicar Auth antes que sus consumidores; después Profile y Babies pueden validarse en paralelo lógico. Crear y publicar el primer commit de IA antes de registrar su gitlink. Terminar con builds local/cloud, gateway y smoke tests contra Firebase real. Los smoke tests usan datos manuales; ante fallo se conservan identificados para diagnóstico.

La aplicación móvil, el despliegue cloud y `recipe-services` quedan fuera. Una entrega móvil posterior cambiará la base de IA e interpretará `PROFILE_NOT_FOUND` como onboarding.

## Validación

Tests de rutas, dominio, mappers y repositorios; aislamiento por UID; foto y compensación; idempotencia Baby, selección y sustitución; defaults y reemplazo IA. Ejecutar por servicio `test -Penv=local`, `build -Penv=local` y `build -Penv=cloud`. Levantar gateway y comprobar health, rutas públicas/directas y cuatro OpenAPI cuando existan credenciales manuales válidas.

No quedan preguntas de diseño abiertas. Las decisiones completas viven en [spec.md](spec.md).
