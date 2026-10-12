# Evidencia — recipes-post-koog

Estado: pendiente de validación real de Storage. Fecha: 2026-10-12. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Resultado implementado

Implementación autorizada por el usuario. POST devuelve una copia de la receta inicial con Created tras validar Auth, AI Configuration y Babies y persistir el contexto. La generación usa un scope de aplicación con SupervisorJob y semáforo configurable; persiste Loading antes del agente, texto antes de imágenes y Ready/Error al terminar. La cancelación HTTP no cancela una creación aceptada. Los cuatro estados son legibles por los GET existentes.

Koog 1.3.0 ejecuta agentes separados de composición y planificación visual con el modelId exacto del contexto, esquemas estrictos y recursos españoles empaquetados. El transporte Responses incorpora un límite explícito de caché al final del bloque fijo, clave por agente/prompt/esquema, TTL de 30 minutos y métricas de tokens sin contexto personal. No hay reintentos, reparación de respuestas, checkpoints ni recuperación.

Las imágenes usan OpenAI Images con parámetros configurables, JPEG validado y subida privada mediante el SDK Firebase Storage. Solo tras subir se guardan referencias y URLs autenticadas del servicio. El GET verifica propietario y ruta interna; las imágenes fallidas se omiten. Los GET no necesitan resolver el LLM ni su credencial.

El Secret local está ignorado y tiene permisos 0600; Compose lo monta para OPENAI_API_KEY_FILE. Su contenido no se registra en documentación, logs, Git ni distribución. Se desactiva el logging interno de Koog que podría incluir datos de entrada.

## Validación ejecutada

Todos los comandos Gradle se ejecutaron desde recipe-services. No se llamó al LLM real ni se escribieron fixtures en Firebase real.

| Comprobación | Resultado y alcance |
| --- | --- |
| `./gradlew test -Penv=local` | 146 pruebas unitarias/HTTP, cero fallos, errores u omisiones. Cuatro estados, dos estilos, tres modos, validación, aislamiento, desconexión/cancelación, concurrencia, errores independientes y fotos opcionales. |
| `./gradlew integrationTest test build installDist distTar -Penv=local` | Correcto con Firestore Emulator en 127.0.0.1:18080 y proyecto demo. Cuatro pruebas de integración ejecutadas, cero fallos u omisiones: creación/contexto, estados, actualizaciones null y acceso por propietario. Emulador eliminado al acabar. |
| `./gradlew build -Penv=cloud` | Correcto. No despliega ni accede al LLM. |
| Payload Koog y Images con MockEngine | Modelo exacto, esquema nativo estricto, bloque fijo/cache key/breakpoint/options, métricas, salida incompleta rechazada y una sola llamada ante fallo. Parámetros y JPEG de imágenes verificados. |
| OpenAPI con openapi-spec-validator | Contrato 3.0.3 válido, incluido Ready con instrucciones completas y pendientes vacíos. |
| Inspección de distTar y JARs | Los seis recursos de prompts/esquemas están empaquetados. No aparecen la clave, directorio de secretos ni local.properties. |
| `docker build -t recipes-service:local .` | Imagen final construida desde la distribución verificada. Digest local: `sha256:d953e90948608fd8ec9a648e72803ed5e02a4b3546b391f971ffcdb042f0659e`. |
| `python3 scripts/isolated-smoke.py` | 83 comprobaciones con Recipes, Koog, SDKs Firebase, Firestore Emulator y Traefik reales; dobles de Auth, AI Configuration, Babies, OpenAI y servidor Storage. Incluye creación/lectura, fotos selectivas, subida/descarga por SDK, acceso ajeno, fallos de texto/fotos, contexto y ausencia de reintentos. Limpia contenedores y red. |
| `docker compose -p bebilo-recipes up -d --no-build --force-recreate recipes-service` | Servicio local actualizado y en ejecución con la imagen final y Secret montado. |
| `python3 scripts/local-smoke.py` | 37 comprobaciones de lectura/contrato/autenticación en Rancher y gateway. Ninguna cuenta válida ni receta de prueba creada. |
| Diff, enlaces y secretos | `git diff --check` en agregador y servicio; enlaces relativos de SDD comprobados; Secret ignorado y permisos 0600. |

Una revisión de los informes detectó que una prueba de integración con runBlocking devolvía un valor y JUnit no la descubría. Al corregir su firma se detectó que la serialización omitía nulls y Firestore retenía campos opcionales anteriores. Se corrigió guardando defaults y nulls explícitamente y se repitieron los checks sobre el código final; la prueba ya figura entre las cuatro ejecutadas.

## Revisiones y cambios sin commit

| Repositorio | HEAD al entregar |
| --- | --- |
| Agregador | `916eaa7646e5b665b6c1080224a1e6fbe5f60832` |
| Recipes | `afd18b61c4918bd60a3cc9b7e07e73ea154eefd2` |
| AI Configuration | `2445451cae8b9c8c86b705e157b916d54c0cc84e` |
| Auth | `31668b90befd9abe381b0ecb504ffcd614009a44` |
| Babies | `6fc97911e6eb813396f3fb19e8551ed99ad06cf8` |
| Profile | `a11fe27efc8dfd92792506f2bcb4142f52d93d76` |

Sin commits nuevos ni cambios de gitlinks. Agregador: documentación e índices. Recipes: API/domain/data/DI/config, OpenAPI, recursos, Gradle, Compose, guías, pruebas y scripts. Los otros submódulos no se modificaron.

## Validación pendiente y límites

El bucket bebilo-dev.firebasestorage.app continúa pendiente de habilitación; T-07 requiere comprobar subida/descarga real cuando esté disponible. Mientras tanto, un fallo de Storage permite Ready con el texto y sin esas fotos. La integración Storage se ha probado aislada mediante el SDK real contra un doble HTTP, no contra el bucket real.

La aceptación por OpenAI real del payload de caché y el ahorro de tokens no se han medido: las pruebas inspeccionan el payload nativo de Koog y simulan respuestas del proveedor. Los prompts pueden no alcanzar el umbral mínimo de caché en solicitudes pequeñas. No se ha ejecutado un recorrido móvil real.

Los reinicios pueden dejar Created/Loading pendientes y una caída de Firestore puede impedir persistir Error; no se reintenta ni recupera, según el alcance acordado.

## Actualización de configuración local — 2026-10-12

Por instrucción explícita del usuario, la clave se trasladó a OPENAI_API_KEY en local.properties ignorado, con permisos 0600, y se eliminó el archivo anterior y el Secret de Compose. Compose usa su env_file existente. Se conserva OPENAI_API_KEY_FILE para un Secret montado en producción; tiene prioridad sobre la clave directa. OPENAI_API_KEY también admite inyección por entorno desde Google Secret Manager. AppConfig oculta sus valores sensibles en toString. Esta decisión sustituye la configuración local descrita en la evidencia inicial.

Validación de esta actualización: 148 pruebas unitarias/HTTP sin fallos ni omisiones; builds local/cloud, installDist y distTar correctos; imagen Docker reconstruida y servicio local recreado. Las 37 comprobaciones locales pasan. Se comprobó sin imprimir la clave que el contenedor recibe OPENAI_API_KEY desde local.properties y ya no monta el Secret anterior. La distribución no contiene la clave; local.properties sigue ignorado y con permisos 0600. No se desplegó ni configuró Google Secret Manager en esta entrega; se conserva su mecanismo de inyección.

También se repitió isolated-smoke.py sobre la imagen actualizada: 83 comprobaciones correctas, incluido el soporte de archivo Secret para producción. Imagen actualizada: sha256:316e8cf3394c625fb4c0ded7cfa935e4f6778d70f470d5705dfe77d1c4f1abf8.
