# Especificación — Onboarding autoritativo en Auth

Estado: implementada y validada localmente. Fecha: 8 de octubre de 2026. [Plan](plan.md) · [Tareas](tasks.md) · [Implementación](implementation.md).

## Objetivo y alcance

Auth será la única fuente de `isFirstTimeAccess`; AI Configuration propagará correctamente una cuenta eliminada. Se despliega solo con Docker Compose en Rancher Desktop. Android, iOS, cloud, Google, Apple, refresh, Baby y Profile quedan fuera.

## Requisitos

| ID | Requisito | Estado/origen |
| --- | --- | --- |
| R-01 | Registro y login email/password devuelven el estado autoritativo. | Acordado; requisitos de la app del 8-10-2026 |
| R-02 | `PUT /auth/v1/onboarding`, bearer y sin body, fija `false` idempotentemente. | Acordado |
| R-03 | La ausencia del claim equivale a `true`; un valor inválido o almacenamiento inaccesible no se oculta. | Acordado; Firebase Auth custom claims elegido por el usuario |
| R-04 | Una cuenta eliminada devuelve 404 y AI Configuration lo propaga. | Acordado |
| R-05 | AI Configuration conserva autenticación antes de Firestore, defaults y reemplazo completo. | Acordado |

## Contratos y datos

El claim `isFirstTimeAccess` pertenece al usuario Firebase Auth. Ausente significa `true`; el PUT conserva los demás claims y escribe `false`. Respuesta: `200 {"isFirstTimeAccess":false}`. Errores: 401 `UNAUTHORIZED`, 404 `ACCOUNT_NOT_FOUND`, 503 `AUTH_STORAGE_UNAVAILABLE` y 500 `INTERNAL_ERROR`, siempre con `code` y `message`.

El login usa el `localId` autenticado por Identity Toolkit para leer el usuario con Firebase Admin. Ningún endpoint acepta UID. AI Configuration mantiene `/aiconfig/v1/ai-configuration` y añade 404 a su contrato.

## Aceptación

| ID | Requisito | Escenario y resultado esperado | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01, R-03 | Cuenta nueva o sin claim devuelve true; tras completar devuelve false incluso después de reiniciar. | Unitarias y smoke Firebase |
| AC-02 | R-02 | PUT repetido responde 200 false y no reescribe el claim. | Unitarias y smoke HTTP |
| AC-03 | R-02, R-04 | Token inválido, cuenta eliminada y fallo de almacenamiento devuelven 401, 404 y 503. | Rutas, adaptador y smoke |
| AC-04 | R-02, R-03 | El UID procede del token y se conservan claims ajenos. | Test de FirebaseAdminAuthClient |
| AC-05 | R-05 | AI devuelve defaults, reemplaza configuración y autentica antes de Firestore. | Tests y smoke Firebase |
| AC-06 | R-01–R-05 | OpenAPI, gateway y contenedores locales coinciden con el comportamiento. | Builds, OpenAPI y Rancher Desktop |

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 8-10-2026 | Confirmada | Persistir en Firebase Auth custom claims; elección explícita del usuario. |
| D-02 | 8-10-2026 | Confirmada | Desplegar con Docker Compose en el contexto `rancher-desktop`, sin cloud ni Kubernetes. |
