# Especificación — SSE de estados de Recipes

Estado: completado. Fecha: 2026-10-13. [Plan](plan.md) · [Tareas](tasks.md).

## Objetivo y alcance

Observación backend por cuenta de estados persistidos; sin cambios móviles, historial o migración. El despliegue habitual, excluido inicialmente, fue autorizado posteriormente por el usuario.

## Requisitos

| ID | Requisito | Estado/origen |
| --- | --- | --- |
| R-01 | Bearer mediante Auth antes de resolver Firestore; colección limitada al UID autenticado | Acordado, petición y plan aceptado |
| R-02 | Snapshot inicial completo y nuevas recetas/cambios Created/Loading/Error/Ready | Acordado |
| R-03 | Evento recipe-status con exclusivamente id público y status | Acordado |
| R-04 | Validación íntegra, sin eventos por contenido, imágenes o eliminaciones | Acordado |
| R-05 | Heartbeat 30 s; liberar listener al terminar; cola 64 lotes, cierre al desbordar | Acordado |
| R-06 | Errores JSON antes del stream; timeout inicial 10 s; cierre después de abrir | Acordado |

## Contratos y datos

GET interno `/v1/recipes/events`, público `/recipes/v1/recipes/events`. Sin body, filtros ni UID del cliente.
200 `text/event-stream`; `Cache-Control: private, no-store`. Cada evento `recipe-status` lleva
`data: {"id":"recipe-id","status":"Loading"}`. ID = `summary.id`, independiente del documentId.
Primera observación de `users/{uid}/recipes` produce un evento por receta; vacía permanece abierta.
Sin ID SSE ni replay de Last-Event-ID. Ready/Error no cierran conexión. Comentario heartbeat cada 30 s.
Errores iniciales: 401 UNAUTHORIZED, 403 FORBIDDEN, 500 INTERNAL_ERROR, 503 SERVICE_UNAVAILABLE.
Errores posteriores cierran sin segundo cuerpo JSON. No se escriben datos ni añaden índices.
Referencia ejecutable: [OpenAPI](../../../recipe-services/src/main/resources/openapi.yaml).

## Aceptación

| ID | Requisitos | Escenario | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01 | Credenciales inválidas no resuelven almacenamiento; cuentas aisladas | HTTP y emulador |
| AC-02 | R-02, R-03 | Inicial, altas, transiciones y reconexión; ID opaco, DTO exacto | Unitarias/HTTP/emulador |
| AC-03 | R-04 | Loading repetido no emite; duplicados/documentos inválidos fallan íntegramente | Unitarias/emulador |
| AC-04 | R-05, R-06 | Vacía abierta, heartbeat, cancelación, fallos y consumidor lento | HTTP/repositorio |
| AC-05 | R-02, R-03 | Streaming progresivo por Traefik | Smoke aislado |

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 2026-10-13 | Acordada | Todas las recetas de la cuenta y snapshot inicial, elección del usuario |
| D-02 | 2026-10-13 | Acordada | Sincronizar estado actual; pueden perderse estados intermedios, elección del usuario |
| D-03 | 2026-10-13 | Acordada | Listener Firestore por conexión, contrato y límites del plan aceptado |

| D-04 | 2026-10-13 | Acordada | Redesplegar Recipes local y verificar Swagger, petición posterior del usuario; sustituye la exclusión inicial de despliegue |
