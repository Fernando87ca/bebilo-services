# Implementación — Onboarding autoritativo y despliegue local

Estado: completado. Fecha: 8 de octubre de 2026. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

- Agregador: `8a8468c5971a4f6f04a14dc3a5d1d766cd0f509b`, con cambios sin commit en documentación y gitlinks sucios de los servicios modificados.
- Auth: base `d1a2877b8a1779d68789a16c9d87f8181838dee7`, con cambios sin commit de esta entrega.
- AI Configuration: base `42e6f6e6421ac411b3d64f4fe105b121b3206b32`, con cambios sin commit de esta entrega.
- Baby `77ce6b6123cc319ef8e66eedc2f1d71190711376` y Profile `a11fe27efc8dfd92792506f2bcb4142f52d93d76`, sin cambios.
- App `0cb3427346fcaefc7f66caf1feec5e6b28666e1c`, usada solo como fuente de requisitos y no modificada por esta entrega.
- Entorno: macOS, Docker y Kubernetes context `rancher-desktop`, red externa `bebilo-gateway`, Firebase real `bebilo-dev`. No se registran secretos.

## Cambios realizados

- Auth obtiene `localId` del login email/password, lee `isFirstTimeAccess` desde custom claims y considera la ausencia como primer acceso. Registro y login serializan el valor de dominio sin forzarlo.
- `PUT /v1/onboarding` extrae el UID del bearer token, conserva claims ajenos, escribe `false` una sola vez y devuelve la respuesta canónica en llamadas repetidas.
- Auth distingue token inválido, cuenta eliminada, almacenamiento no disponible y error inesperado mediante 401, 404, 503 y 500.
- AI Configuration propaga el 404 de Auth y conserva el orden de autenticación previo a Firestore, defaults y reemplazo completo.
- OpenAPI, Swagger, README e instrucciones del servicio se alinearon con los contratos desplegados.

## Evidencia

| Fecha | Tarea / AC | Directorio y comando o comprobación | Resultado |
| --- | --- | --- | --- |
| 8-10-2026 | T-02 / AC-01–04 | `auth-services: ./gradlew test -Penv=local` | 41 tests, 0 fallos |
| 8-10-2026 | T-03 / AC-03, AC-05 | `aiconfig-services: ./gradlew test -Penv=local` | 9 tests, 0 fallos |
| 8-10-2026 | T-04 / AC-01–06 | `./gradlew build -Penv=local` y `./gradlew build -Penv=cloud` en Auth e IA | Cuatro builds correctos |
| 8-10-2026 | T-05 / AC-06 | `docker compose -p bebilo-auth up -d --build` y equivalente `bebilo-aiconfig` | Imágenes reconstruidas y contenedores recreados en Rancher Desktop |
| 8-10-2026 | AC-06 | Health directo 8081/8085 y `/auth`, `/aiconfig`, ambos OpenAPI y Swagger por Traefik | HTTP 200; contratos nuevos publicados |
| 8-10-2026 | AC-03, AC-05 | PUT Auth y GET IA sin bearer | HTTP 401 con `UNAUTHORIZED`, antes de resolver almacenamiento |
| 8-10-2026 | AC-01–05 | Smoke Firebase con dos cuentas temporales | Registro/login true, PUT repetido false, login posterior y tras reinicio false, aislamiento por UID, cuenta eliminada 404 y GET/POST/GET de IA correctos |
| 8-10-2026 | Limpieza | Identity Toolkit y REST Firestore | Dos cuentas y el documento temporal de IA eliminados |
| 8-10-2026 | Calidad | `git diff --check` en agregador, Auth e IA | Correcto |

## Pendientes y limitaciones

No quedan pendientes dentro del alcance. Google, Apple, refresh, despliegue cloud/Kubernetes, app, Baby, Profile y la limitación previa de Firebase Storage permanecen fuera de esta entrega.
