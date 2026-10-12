# Tareas — Consulta de recetas

[Spec](spec.md) · [Plan](plan.md) · [Evidencia](implementation.md).

| ID | Tarea | Estado | Dependencias | Requisitos / aceptación | Entregable |
| --- | --- | --- | --- | --- | --- |
| T-01 | Contrato, decisiones, organización Git y SDD | Completada | — | R-01–07 | Spec/plan, submódulo existente conservado |
| T-02 | Ktor, DTOs, dominio, identidad, consultas y errores | Completada | T-01 | R-01–06 / AC-01–05 | Servicio GET sin escrituras |
| T-03 | OpenAPI, README, instrucciones, Docker/Compose y Swagger central | Completada | T-02 | R-07 / AC-06 | Contrato y configuración |
| T-04 | Pruebas automatizadas, builds y SDK con emulador | Completada | T-02 | AC-01–05 | Informes Gradle |
| T-05 | Smoke aislado por Traefik y limpieza | Completada | T-03, T-04 | AC-01–05 | Script reproducible y resultado |
| T-06 | Despliegue Rancher y regresión gateway | Completada | T-05 | AC-06 | Servicio accesible y Swagger central |
| T-07 | Registro final, revisiones, enlaces y diff | Completada | T-06 | R-07 | Evidencia y pendientes explícitos |
| T-08 | Integración móvil autenticada real | Pendiente | Servicio y cuenta con recetas válidas | AC-07 | Recorrido móvil observado |
