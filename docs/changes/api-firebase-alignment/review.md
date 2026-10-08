# Revisión del traslado — 8 de octubre de 2026

Entrega exclusivamente documental. [Plan canónico](plan.md) · [Spec](spec.md) · [Tareas](tasks.md).

## Procedencia

Importados `spec.md`, `plan.md` y `tasks.md` desde `/Users/foca/AndroidStudioProjects/bebilo/docs/changes/api-firebase-alignment`. Se conservó el contenido de contratos, fases, aceptación y preguntas, adaptando propiedad del plan, enlaces y hallazgos. El original de la app permanece sin modificar; las próximas decisiones se registran en esta carpeta de `bebilo-services`.

## Base inspeccionada

| Repositorio | HEAD al revisar |
| --- | --- |
| bebilo-services | `271bd1a3366721ac5f78f86955ac34236d8a965f` |
| auth-services | `9c5b42fc934e76eb37439855c501458d1b25c173` |
| baby-services | `4d800666b5e77b0bd8bb6d7a7217f6a3d6194842` |
| profile-services | `c5fbe95c286033e39086023e83e2bdc9acb2d130` |
| recipe-services | `889fe7f40be666138f3c6b0a19e7997d6f8b72bb` |
| App bebilo | `0cb3427346fcaefc7f66caf1feec5e6b28666e1c` |

El backend estaba limpio antes de este traslado. La app contiene numerosos cambios sin commit y archivos nuevos: su HEAD no representa por sí solo el código revisado. Se inspeccionó su árbol de trabajo; no se modificó ni se revirtieron cambios.

## Hallazgos confirmados por lectura

- `ProfileApi.kt` en la app usa GET/POST/PUT `/user`; `ProfileRoutes.kt` en backend conserva `/get-user`, `/create-user` y `/edit-user`. `FirestoreUserProfileRepository.kt` persiste nombre bajo email y todavía no identificación ni foto.
- La app ya tiene `AiConfigurationApi.kt` en su módulo propio. `BackendEndpoint.kt` aún incluye `/profile/v1`, por lo que IA sigue bajo Profile en la URL. `settings.gradle.kts` del backend no incluye `aiconfig-services`.
- `BabiesApi.kt` aún selecciona mediante GET `/set-initial-baby/{babyId}`. `FirestoreBabyRepository.kt` cambia indicadores en transacción, crea IDs del servidor y borra bebés sin reasignar selección. `BabyRequest.kt` envía ID, pero `BabyItem.kt` del servidor no declara ese campo. Deben cerrarse contrato e idempotencia.
- `FirebaseAdminAuthClient.kt` obtiene UID del token verificado, pero `UserInfoResponse.kt` solo expone email. Adoptar UID afecta el contrato de Auth y sus consumidores, no solo las rutas Firestore.
- `FirebaseIdentityToolkitClient.kt` fija `isFirstTimeAccess=false` para email/password y usa `isNewUser` para Google. Ninguno comprueba si queda perfil en Firestore. Se amplía F0 para resolver onboarding con Auth existente y Firestore vacío sin crear dependencia circular.
- `.gitmodules` registra los cuatro servicios. Se amplía F4 y la pregunta 14 para decidir la organización Git de IA; Gradle por sí solo no resuelve su incorporación al agregador.
- `docker-compose.traefik.yml` agrega Swagger de Auth, Baby y Profile. Las etiquetas de enrutamiento de Profile viven en su Compose. La integración IA debe cubrir ambos lugares.
- Se leyeron los registros recientes de Mi perfil, selector e IA. La selección local inmediata y las revisiones 13–15 de IA siguen siendo requisitos de integración; el antiguo `/get-user` del registro del selector es histórico.

## Cambios documentales

- La ubicación canónica queda confirmada en este backend; se retira la pregunta redundante sobre qué checkout usar.
- Se mantienen abiertas las decisiones funcionales que el traslado no resuelve: identidad, esquema, rutas, fotos, IDs, selección, onboarding y entornos.
- Se añaden proceso SDD, índice, plantillas y guía raíz para futuros agentes. Las instrucciones existentes de cada servicio siguen aplicándose.
- No se crea `implementation.md` del cambio funcional hasta comenzar su ejecución. Este registro acredita revisión documental, no aceptación AC-01–10.

## Validación y límites

Lectura de documentación y código, revisión de enlaces Markdown locales y `git diff --check`. No se ejecutaron builds, tests funcionales ni consultas a Firebase. No se verificó el borrado comunicado de Firebase ni la disponibilidad cloud. Los resultados históricos de pruebas de la app no se presentan como repetidos en esta entrega.

## Seguimiento: PUT de ProfileApiImpl

Revisado de nuevo el árbol actual de la app a petición del usuario: `ProfileApi.kt`, DTO/mapper/repositorio de perfil, `CreateUserUseCase`, `UpdateUserProfileUseCase`, `FetchUserProfileUseCase`, `ProfileSync`, `UserProfileSnapshot`, `UserProfile`, `AboutMeViewModel` y `ProfileApiTest`. Contrastados con rutas, DTOs, validación y serialización actuales de Profile en backend.

El plan ya incluía PUT desde el traslado. Se precisa ahora que es una escritura completa, que el servidor ignora actualmente los campos nuevos y que la validación del cliente rechazaría su respuesta incompleta. Se registra además la falta de reconciliación automática tras 409 y de redirección a onboarding tras 404. La caché actual usa un snapshot de perfil/estado/error y la carga inicial pertenece a `FetchUserProfileUseCase`.

Actualizados plan y entregable T-03. Lectura estática y revisión documental; las pruebas HTTP existentes en app se inspeccionaron pero no se ejecutaron. No se modificaron servicios ni app.
