# Implementación — SSE de Recipes

Fecha: 2026-10-13. Estado: completado. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y cambios previos

Agregador inicial: `64f2485bf55f3b485a92a625ac458e4713257697`.
Recipes inicial: `a28d55e93e2c95be0c1a189d7894b590d6996e90`.
Sin commit previo: prompts composition, image-rendering, visual-planning; docs/changes/README.md y recipe-child-only-prompts.
Todos preservados; no se crean commits, remotos ni se cambia el gitlink.

## Cambios ejecutados

Observación Firestore con validación íntegra, filtrado de estados y cancelación; use case autenticado;
DTO mínimo y ruta SSE con preflight; dependency Ktor SSE y OpenAPI.

## Comprobaciones

Primer test de Recipes antes de añadir tests nuevos: `./gradlew test -Penv=local`, éxito.
Dos invocaciones accidentales desde el agregador fallaron porque no tiene tarea test;
la validación funcional continúa desde el directorio del servicio.

Validación final desde `recipe-services`, usando JDK 23 de Gradle, Docker Rancher Desktop,
proyectos `demo-bebilo-recipes`/`demo-bebilo-recipes-smoke` y emuladores locales:

| Comando | Resultado final | Cobertura |
| --- | --- | --- |
| `./gradlew test build installDist distTar -Penv=local` | Éxito; 163 tests, 0 fallos/errores/omitidos; build y distribución generados | AC-01–AC-04 con dobles y reglas de domain |
| `./gradlew build -Penv=cloud` | Éxito; última ejecución UP-TO-DATE tras build local; ejecución previa recompiló y ejecutó tests | Compilación/packaging del servicio con ambos parámetros |
| `FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local` | Éxito; 5 tests, 0 fallos/errores/omitidos | SDK real con emulador; AC-01–AC-03, escritores externos, varias suscripciones y reconexión |
| `docker build -t recipes-service:local .` | Éxito; imagen final `sha256:2769100ee349b65b962535da3ce19871f26c7076c9853dae46dba18be33673ba` | Packaging JRE 23 |
| `python3 scripts/isolated-smoke.py` | PASS: 101 checks | AC-05; SSE progresivo real por Traefik, autenticación, aislamiento, estados, filtrado y reconexión; regresión GET/POST/generación/imágenes |
| `python3 -m py_compile scripts/isolated-smoke.py` | Éxito | Sintaxis del smoke |
| `git diff --check` en servicio y agregador; comprobación de enlaces SDD | Éxito | Whitespace, enlaces y contrato documentado |

El smoke usa Recipes, Koog y SDK Firebase reales contra Firestore Emulator; Auth, AI Configuration,
Babies, OpenAI y GCS son dobles. No se realizan llamadas a Firebase/LLM reales ni recorrido móvil.
Los contenedores y red del smoke se limpian automáticamente; el emulador de integrationTest se
arrancó con nombre `bebilo-recipes-sse-test-firestore` y se paró/eliminó al acabar.

## Evidencia de comportamiento y ajustes

- HTTP verifica DTO con exclusivamente id/status, IDs opacos, errores JSON iniciales 401/403/500/503,
  timeout, cierre sin JSON después de abrir, cuenta vacía, heartbeat y limpieza por desconexión.
- El heartbeat y timeout se prueban con intervalos de 30 ms; producción mantiene 30 s y 10 s.
- Repo con dobles verifica snapshot inicial vacío, filtrado, validación íntegra, duplicados,
  fallos temporales/permanentes, error al registrar, cancelación y desbordamiento de 64 lotes.
- SDK/emulador verifica escrituras desde un segundo cliente, dos listeners independientes de la
  misma cuenta, otra cuenta aislada y reconexión que devuelve el estado actual.
- Gateway verifica dos streams del mismo usuario, otro usuario, snapshot inicial de 25 recetas,
  altas y Created → Loading → Ready → Loading → Error, filtrado de contenido y reconexión.
- Las pruebas detectaron inicialmente el cierre prematuro del observer: algunos engines devuelven
  de respond antes de terminar WriteChannelContent. La suscripción ahora pertenece a la sesión,
  con el Job de Application como padre para cancelación al parar, y cleanup explícito al terminar.
- El heartbeat de Ktor 3.5.1 se enlaza al Job de la ruta; se implementó en el scope de la sesión
  para que sobreviva al handler. Se siguen usando plugin SSE y SSEServerContent de Ktor.
- FirestoreException de listeners no es ApiException; se clasifica por su estado gRPC.
- Callback ejecutado inline en el dispatch serial de Watch para preservar orden sin añadir una
  cola de ejecutor; trySend no bloquea al SDK esperando al consumidor SSE.
- Los fallos de compilación/pruebas durante estas iteraciones quedaron corregidos; los resultados
  finales anteriores corresponden al código entregado.

## Entrega y límites

Revisiones finales sin commits nuevos: agregador `64f2485bf55f3b485a92a625ac458e4713257697`,
Recipes `a28d55e93e2c95be0c1a189d7894b590d6996e90`. Implementación sin commit en el submódulo;
gitlink conservado porque no hay una revisión nueva que integrar. Otros submódulos intactos.

Archivos del cambio: configuración Gradle/Ktor/DI, modelo/domain contract/case de observación,
DTO/ruta SSE, repository/tracker Firestore, tres clases de pruebas, OpenAPI, smoke, README/AGENTS
de Recipes e índices/SDD del agregador. Los tres prompts previos y recipe-child-only-prompts
se preservaron sin ediciones por este cambio.

No hay migración, historial, replay ni garantía de todos los estados intermedios. Reconectar
sincroniza estados actuales; eliminaciones no se notifican. Cada conexión abre su listener y valida
la colección completa. En conexiones inactivas, un fallo del siguiente
heartbeat/escritura permite detectar la desconexión y retirar el listener. La entrega inicial no modificó el despliegue habitual ni la app; el despliegue posterior autorizado se registra a continuación.



## Redespliegue local autorizado — 2026-10-13

Petición posterior del usuario: «redesplega en swagger los servicios de recipe».

- `./gradlew installDist distTar -Penv=local`: éxito (UP-TO-DATE).
- `docker --context rancher-desktop compose -p bebilo-recipes up -d --build --no-deps recipes-service`:
  éxito; recreado `bebilo-recipes-recipes-service-1`, estado running, imagen
  `sha256:eb7fef772cf368f4f4bbea0e99c4c08f9ee50a61c8f3b42e4cb0a9fab53266bf`.
- Health de Recipes por gateway: 200.
- `python3 scripts/local-smoke.py`: PASS, 37 comprobaciones del despliegue, sin fixtures Firebase.
- Checks adicionales: OpenAPI directo y por gateway idéntico al archivo local, con
  `/v1/recipes/events` y `RecipeStatusEventDto`; SSE sin Bearer y con Bearer inválido devuelve
  401 UNAUTHORIZED en ambas rutas.
- Swagger central sirve `/recipes/openapi.yaml` dinámicamente y su initializer contiene Recipes;
  no requiere recrear Swagger ni los demás servicios para actualizar ese contrato.
- Swagger central: http://api.bebilo.localhost:8088/swagger/ (seleccionar Recipes Service).
  Swagger específico: http://api.bebilo.localhost:8088/recipes/swagger.

Este redespliegue no realiza pruebas autenticadas SSE con una cuenta real. Esas transiciones
quedaron validadas en el stack aislado; aquí se verifica contrato/routing/autenticación inicial.
Revisiones Git conservadas; cambios permanecen sin commit.
