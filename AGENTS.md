# Guía del repositorio bebilo-services

Este repositorio agrega servicios Kotlin/Ktor mediante Gradle `includeBuild`. Los servicios actuales son submódulos Git; revisar su estado e instrucciones `AGENTS.md` antes de modificarlos.

- Consultar [docs/README.md](docs/README.md) y [el proceso SDD](docs/spec-driven-development.md) para planes y cambios transversales.
- Mantener especificación, plan, tareas y evidencia en `docs/changes/<tema>/`. Usar las plantillas para nuevos cambios y actualizar el índice.
- Distinguir comportamiento existente, objetivo acordado y propuestas pendientes. No implementar decisiones abiertas como si estuvieran confirmadas.
- Respetar la autorización de la conversación; las decisiones rutinarias dentro de un alcance autorizado no requieren nuevas confirmaciones.
- Los contratos HTTP ejecutables pertenecen a `src/main/resources/openapi.yaml` de cada servicio. Actualizarlos con los cambios de rutas, DTOs, estados y errores.
- Conservar las fronteras de negocio y las capas api/domain/data/di/config/plugins. La configuración de IA tendrá servicio propio según el plan vigente.
- Registrar revisiones de servicios y agregador al entregar cambios que crucen submódulos. No crear remotos ni sustituir submódulos por carpetas implícitamente.
- No versionar credenciales, tokens, imágenes personales ni configuración local.
- Seguir las comprobaciones funcionales de cada servicio. Para cambios exclusivamente documentales, comprobar enlaces y diff sin ejecutar builds innecesarios.
