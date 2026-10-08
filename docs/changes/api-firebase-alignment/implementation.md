# Implementación — API y Firebase

Estado: **pendiente de validación**. Fecha: 8 de octubre de 2026. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

- Agregador de partida: `271bd1a3366721ac5f78f86955ac34236d8a965f`. La revisión que integra los gitlinks y esta evidencia se registra en el commit posterior de cierre documental.
- Auth: `d1a2877b8a1779d68789a16c9d87f8181838dee7`, publicado en `main`.
- Profile: `a11fe27efc8dfd92792506f2bcb4142f52d93d76`, publicado en `main`.
- Babies: `77ce6b6123cc319ef8e66eedc2f1d71190711376`, publicado en `main`.
- AI configuration: `42e6f6e6421ac411b3d64f4fe105b121b3206b32`, publicado en `main`.
- Recipe, sin cambios: `889fe7f40be666138f3c6b0a19e7997d6f8b72bb`.
- App usada solo como referencia: `0cb3427346fcaefc7f66caf1feec5e6b28666e1c`; no se modificó.
- Entorno: macOS, Rancher Desktop, contexto Docker y Kubernetes `rancher-desktop`, Firebase real `bebilo-dev`. No se registran secretos.

## Cambios realizados

- Auth devuelve `uid` y `email` verificados en `/v1/user-info`.
- Profile usa UID, expone únicamente GET/POST/PUT `/v1/user`, valida el perfil completo, limita el body a 2 MiB y gestiona referencias de fotos privadas versionadas con compensación y limpieza diferida.
- Babies usa `users/{uid}/babies/{babyId}`, UUID del cliente, creación idempotente, `createdAt` de servidor y selección/borrado transaccionales. `parentName` desapareció del contrato y la persistencia.
- `aiconfig-services` se creó como submódulo independiente y publica GET/POST `/v1/ai-configuration` con defaults sin escritura y persistencia por UID.
- El agregador incluye el nuevo build, ruta Traefik `/aiconfig`, puerto directo 8085 y la cuarta especificación en Swagger central.

## Evidencia

| Tarea / AC | Comprobación | Resultado |
| --- | --- | --- |
| T-02 / AC-07 | `auth-services: ./gradlew test -Penv=local` | 29 tests, 0 fallos |
| T-03 / AC-01–03, AC-07 | `profile-services: ./gradlew test -Penv=local` | 24 tests, 0 fallos |
| T-04 / AC-04–05, AC-07 | `baby-services: ./gradlew test -Penv=local` | 39 tests, 0 fallos |
| T-05 / AC-06–08 | `aiconfig-services: ./gradlew test -Penv=local` | 8 tests, 0 fallos |
| T-06 | `./gradlew build -Penv=local` en los cuatro servicios | Correcto |
| T-06 | `./gradlew build -Penv=cloud` en los cuatro servicios | Correcto |
| AC-08 | `docker compose ... up -d --build` sobre Rancher Desktop | Auth, Babies, Profile, IA, Traefik y Swagger activos |
| AC-08 | Health directo en 8081, 8082, 8083 y 8085 | HTTP 200 |
| AC-08 | `/auth/health`, `/baby/health`, `/profile/health`, `/aiconfig/health` por `api.bebilo.localhost:8088` | HTTP 200 |
| AC-08 | OpenAPI de Auth, Babies, Profile e IA por el gateway | HTTP 200; Swagger central carga las cuatro fuentes |
| AC-07 | Token Firebase inválido contra Profile, Babies e IA | HTTP 401 con `{code,message}` |
| Corte Profile | `/profile/v1/get-user` | HTTP 404; la ruta retirada no se publica |

Swagger central: `http://api.bebilo.localhost:8088/swagger/`. Dashboard de Traefik: `http://localhost:8090/`.

## Pendientes y limitaciones

- No se ejecutó el recorrido autenticado de creación, lectura y actualización contra Firebase porque no se proporcionaron las credenciales manuales de la cuenta de prueba. No se crearon datos de smoke test.
- Google Cloud rechazó la creación de `bebilo-dev.firebasestorage.app` con HTTP 403: la cuenta de facturación del proyecto está ausente. Profile funciona con `photo: null`; las operaciones con foto devolverán indisponibilidad de Storage hasta activar facturación y crear el bucket en `europe-west1`.
- Tras resolver ambos puntos deben repetirse los smoke tests autenticados de Auth, Profile, Babies e IA. Si fallan, sus datos se conservarán e identificarán aquí para diagnóstico.
