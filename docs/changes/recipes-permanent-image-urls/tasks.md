# Tareas — URLs permanentes de imágenes

[Spec](spec.md) · [Plan](plan.md).

| ID | Tarea | Estado | Dependencias | Requisitos / aceptación | Entregable |
| --- | --- | --- | --- | --- | --- |
| T-01 | Upload y DTO directo; retirar ruta; opcionalidad | Completada | Plan aprobado | R-01/R-02/R-03 / AC-01/AC-02 | Código/contrato |
| T-02 | Migración segura e inventario previo | Completada | T-01 | R-04 / AC-03 | CLI y pruebas |
| T-03 | Checks del servicio y despliegue local | Completada | T-01/T-02 | R-05 / AC-04 | Evidencia |
| T-04 | Descarga real y migración si hay referencias antiguas | Pendiente | Bucket accesible | R-01/R-04 | Estado real sin fixtures |

Inventario real: 0 recetas, 0 enlaces antiguos; no fue necesaria ninguna escritura de migración. T-04 queda pendiente exclusivamente por disponibilidad y descarga real de Storage. Ver [evidencia](implementation.md).
