# Evidencia — Recetas narradas y pasos concisos

Fecha: 2026-10-14. Estado: completado; validación funcional con proveedor LLM simulado.

## Revisiones y cambios

Agregador inicial/final 5a323a54985ac61c4b1c12896d184cab06c8d1f7; Recipes inicial/final efd1bf14acb821fbaa9d87450376a844f939075a. Checkouts inicialmente limpios. Sin commits ni push: cambios de trabajo en Recipes (contrato, generación, prompts, pruebas, smoke e instrucciones) y agregador (documentación/índices). Gitlinks y remotos conservados. AI Configuration no se modifica; la app móvil queda fuera.

Contrato steps con objective/tasks obligatorio por bloque; narrativa conserva paragraphs con text. DTO, dominio, mappers, validación, schemas y OpenAPI alineados. Agentes composition-steps/composition-narrative con reglas comunes y especialización exclusiva; ejecutar solo el seleccionado. Orquestador también usa prompt/schema del estilo correspondiente. Seguridad, estados e imágenes conservados. Firestore rechaza bloques legacy; sin migración ni borrado, según colección vacía confirmada por el usuario.

## Validación

Entorno macOS, JDK 23, wrapper del servicio Gradle 9.3.0 y Docker Rancher Desktop. Emulador desechable demo-bebilo-recipes en 127.0.0.1:18080 retirado al terminar. El smoke aislado arranca y limpia su propio stack. No se escriben datos en Firebase real.

| Comprobación desde recipe-services | Resultado |
| --- | --- |
| ./gradlew test build installDist distTar -Penv=local | PASS: 270 pruebas, cero fallos/errores/omitidas; build y distribución generados |
| ./gradlew build -Penv=cloud | PASS; tareas up-to-date, sin validación de infraestructura cloud |
| FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local | PASS: 9 pruebas, cero fallos/errores/omitidas |
| docker --context rancher-desktop build -t recipes-service:local . | PASS |
| DOCKER_CONTEXT=rancher-desktop python3 scripts/isolated-smoke.py | PASS: 120 checks; Koog, SDK Firebase y Traefik reales con dependencias/proveedores simulados |
| Schemas JSON estrictos; OpenAPI YAML y referencias; enlaces documentales; git diff --check del servicio/agregador | PASS |

Se verificaron ambos estilos y modelos, selección exclusiva de prompt/schema, objetivo y lista de tareas, orden de tareas, IDs, imágenes, serialización HTTP y roundtrip Firestore. Casos negativos incluyen objetivos/tareas ausentes o en blanco, listas vacías, tipos no texto, legacy/mixed, IDs duplicados y estilo incorrecto. El worker termina Error antes de planificación visual y conserva la entrada. Narrativa mantiene prosa e imágenes; pruebas existentes de seguridad, estados y fotos opcionales pasan.

Las primeras ejecuciones locales usaron accidentalmente el wrapper del agregador (Gradle 9.6.0); la ejecución final de test/build/distribución se repitió desde el servicio con su wrapper 9.3.0 y pasó. El script inicial de documentación se ejecutó desde un directorio incorrecto y terminó sin escribir archivos; se corrigió antes de completar la documentación.

## Despliegue Rancher Desktop

Autorizado explícitamente en conversación. A las 17:08:26 Europe/Madrid del 2026-10-14 se recreó únicamente Recipes mediante:

```sh
docker --context rancher-desktop compose -p bebilo-recipes up -d --no-build --no-deps recipes-service
```

Imagen construida y desplegada idéntica: sha256:6085125f612b66afc904bf1ae44e03a1e7065568ab05cea6ce75646077ba6e6e. Contenedor running, cero reinicios; health PASS. python3 scripts/local-smoke.py PASS: 37 checks de solo lectura, sin cuentas válidas ni recetas de prueba en Firebase. OpenAPI directo/gateway coincide byte a byte con el fuente; Swagger accesible.

JAR local y desplegado SHA-256 idéntico: e397aedcf27f110b99d8c39e2b84a4d34a31cc147f636bd0555856c2333ea0c0.

## Límites

No se realizaron llamadas LLM reales ni recorrido móvil autenticado. Las pruebas verifican contratos y orquestación, pero no cuantifican ahorro real de tokens ni evalúan la calidad culinaria del texto producido por el proveedor. El móvil deberá consumir objective/tasks para steps y paragraphs/text para narrative mediante instructions.type.
