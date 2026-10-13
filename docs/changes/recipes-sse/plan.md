# Plan — SSE de estados de Recipes

Estado: completado. Fecha: 2026-10-13. [Spec](spec.md) · [Tareas](tasks.md).

## Estado actual y evidencia

Agregador inicial `64f2485bf55f3b485a92a625ac458e4713257697`.
Recipes inicial `a28d55e93e2c95be0c1a189d7894b590d6996e90`, submódulo Git.
Cambios anteriores preservados: tres prompts de Recipes e índice/documentación de recipe-child-only-prompts.
Worker persiste Created, Loading y Ready/Error; guarda también texto/fotos con Loading.
Ktor 3.5.1, DI nativa, Firestore por cuenta y Auth vía user-info ya existentes.

## Diseño

Modelo/domain repository de observación y use case autenticado con almacenamiento diferido.
Listener Firestore callbackFlow y cola 64; valida snapshots íntegros y compara por summary.id.
Primer snapshot del mismo listener actúa como preflight (10 s), evitando read/listen gap.
Ktor SSEServerContent permite autenticar/validar antes de responder. Heartbeat 30 s.
Cancelación estructurada y awaitClose retiran listener; errores posteriores cierran sin JSON.
Sin cambios al worker, persistencia o app. El despliegue habitual, excluido inicialmente, se añade por petición posterior del usuario. No hay replay/historial.

## Fases y validación

1. Contratos/domain/listener/DI/ruta y OpenAPI.
2. Pruebas unitarias, HTTP con dobles, SDK/emulador y smoke progresivo por Traefik.
3. Checks de AGENTS de Recipes, documentación/evidencia/revisiones finales.

Consultar [tareas](tasks.md) y [evidencia](implementation.md); no considerar pruebas previstas como ejecutadas.

4. Redesplegar únicamente Recipes en Rancher Desktop, comprobar OpenAPI/Swagger y smoke local de lectura; completado según [evidencia](implementation.md).
