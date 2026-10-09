# Tareas — Onboarding autoritativo y despliegue local

[Spec](spec.md) · [Plan](plan.md) · [Implementación](implementation.md).

| ID | Tarea | Estado | Dependencias | Requisitos / aceptación | Entregable |
| --- | --- | --- | --- | --- | --- |
| T-01 | Registrar contratos y decisión de persistencia | Completada | Ninguna | R-01–R-05 | Spec y plan |
| T-02 | Implementar claim y endpoint en Auth | Completada | T-01 | AC-01–04 | Código, OpenAPI y tests |
| T-03 | Propagar cuenta eliminada en AI Configuration | Completada | T-01 | AC-03, AC-05 | Código, OpenAPI y tests |
| T-04 | Validar builds y contratos | Completada | T-02–03 | AC-01–06 | Evidencia automatizada |
| T-05 | Desplegar y ejecutar smoke local | Completada | T-04 | AC-01–06 | Contenedores y evidencia Rancher |
