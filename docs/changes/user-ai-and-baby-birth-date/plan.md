# Plan — IA por usuario y nacimiento

Estado: completado; ver [evidencia](implementation.md). [Spec](spec.md) · [Tareas](tasks.md).

## Estado actual

IA distinguía UID en colección raíz; Babies almacenaba ageMonths y Recipes lo consumía. App /Users/foca/AndroidStudioProjects/bebilo ya implementa birthDate. Recipes, docs y móvil contienen cambios locales que se conservan.

## Implementación y validación

1. Mover documento IA, adaptar pruebas e instrucciones.
2. Sustituir edad por nacimiento en Babies; validar ISO/fechas, actualizar OpenAPI/tests.
3. Consumir nacimiento y calcular snapshot en Recipes con referencia única y reloj inyectable.
4. Verificar contrato móvil y enlazar documentación existente.
5. Checks de AGENTS.md, emulador y smoke aislado; construir distribuciones/imágenes y desplegar proyectos bebilo-aiconfig, bebilo-baby y bebilo-recipes en rancher-desktop.

No fixtures en Firebase real. Registrar revisiones, resultados y limitaciones en [evidencia](implementation.md). Sin decisiones abiertas.
