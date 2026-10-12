# Implementación — Categorías de ingredientes

Estado: completado; backend validado y desplegado. Fecha: 12 de octubre de 2026.
[Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

- Agregador HEAD: `07b5e89941a9fba554e321e7be5b469f1c8ae7d5`; cambios previos sin commit
  en docker-compose.traefik.yml, índices SDD, docs/changes/recipes-get y Recipes.
- Recipes HEAD/gitlink: `889fe7f40be666138f3c6b0a19e7997d6f8b72bb`; implementación GET previa
  sin commit (src, scripts, documentación, Docker/Compose y configuración Gradle). Se conserva.
- App HEAD: `f34f092f36dfe4c93969a1f0f63f818b6af7176e`; cambios locales de categorías,
  contrato y placeholders inspeccionados; no se modifica la app.
- Otros submódulos: Auth `31668b90befd9abe381b0ecb504ffcd614009a44`, Babies
  `6fc97911e6eb813396f3fb19e8551ed99ad06cf8`, Profile
  `a11fe27efc8dfd92792506f2bcb4142f52d93d76`, AIconfig
  `2445451cae8b9c8c86b705e157b916d54c0cc84e`; no requieren cambios.
- Entorno: macOS, Docker contexto rancher-desktop, servicio existente en proyecto bebilo-recipes.
  No se leen ni versionan secretos. Submódulos y remotos conservados; sin commits ni push.

## Cambios realizados

- T-01: registrado contrato obligatorio, autorización del plan, ausencia de datos y redespliegue local.
- T-02: enums separados Domain/DTO, category requerido sin default/null, reconocimiento de strings
  exactos en Firestore y mapeo exhaustivo a respuesta. OpenAPI incorpora enum y required; README y
  AGENTS del servicio documentan persistencia y rechazo. No cambia repositorio ni consultas.
- T-03: fixtures válidos incluyen category sin ocultar validaciones previas de cantidades/alérgenos.
  Nuevos casos HTTP cruzan ocho categorías × tres estados y cuatro variantes de catalogId.
  Pruebas SDK preservan los ocho valores, IDs opcionales y equivalencia entre GET. Corrupción
  cubre ausencia, null, valor desconocido, mayúsculas, blanco y número; ambos endpoints dan 500,
  sin resultado parcial. SDK verifica contenido y updateTime idénticos tras las lecturas.
- T-04: builds y distribución correctos; imagen Docker probada por gateway aislado con SDK real,
  Auth simulado y ausencia de AIconfig. Smoke aislado incorpora valores, corrupción y conservación
  del documento; smoke local compara byte a byte OpenAPI directo y por gateway con el archivo.
- T-05: reconstruido/redesplegado solo el proyecto bebilo-recipes en Rancher Desktop. Swagger
  central ya incluía Recipes; sirvió el contrato actualizado sin cambios de Traefik ni otros servicios.
- T-06: documentación/tareas/índices actualizados; revisiones, comandos y límites registrados.

## Evidencia

Comprobaciones del 12 de octubre de 2026. Comandos desde recipe-services salvo indicación.

| Tarea / aceptación | Comando o comprobación ejecutada | Resultado |
| --- | --- | --- |
| T-03–04 / AC-01–03, AC-05 | Desde agregador: `./recipe-services/gradlew -p recipe-services test -Penv=local --console=plain` | BUILD SUCCESSFUL; 88 pruebas, cero fallos/errores/omisiones |
| T-04 / AC-04 | `./gradlew test build installDist distTar -Penv=local --console=plain` | BUILD SUCCESSFUL; distribución tar/zip e installDist; test reutiliza resultado válido |
| T-04 / AC-04 | `./gradlew build -Penv=cloud --console=plain` | BUILD SUCCESSFUL; tareas de compilación/test reutilizadas, sin pruebas adicionales de entorno cloud |
| T-03–04 / AC-01–03, AC-05 | `FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local --console=plain` | 3 pruebas SDK/HTTP reales contra emulador demo-bebilo-recipes, cero fallos/errores/omisiones |
| T-04 / AC-04 | `docker --context rancher-desktop build -t recipes-service:local .` | Imagen construida con distribución final y JRE 23 |
| T-03–04 / AC-01–05 | `DOCKER_CONTEXT=rancher-desktop python3 scripts/isolated-smoke.py` | 60 comprobaciones correctas por Traefik; Firestore Emulator + Auth simulado + Recipes real; limpieza correcta |
| T-05 / AC-04 | `docker --context rancher-desktop compose -p bebilo-recipes up -d --build` | Recipes reconstruido y recreado; running, cero reinicios; inicio 13:33:57 Europe/Madrid |
| T-05 / AC-04–05 | `python3 scripts/local-smoke.py` | 31 comprobaciones correctas de lectura; incluye health/OpenAPI de otros servicios, autenticación, Swagger central y igualdad del contrato directo/gateway |
| T-05 / AC-04 | SHA-256 del JAR local y del JAR del contenedor desplegado | Ambos `64e4467d587038d6c66e5b89deaf1bde63b51d003a30cda3641727091a7c7975` |
| T-06 / AC-04 | Ruby YAML sobre OpenAPI; inspección de enum/required/type/nullable/default; AST Python, enlaces locales y diff | Contrato YAML válido, ocho valores exactos, campo requerido no nullable sin default; scripts/enlaces/whitespace correctos |

Imagen desplegada: `recipes-service:local`, identificador del contenedor
`sha256:7f3fb60b3a58b12c390cf3e6c373b499caf8a723bac589587822fe113f028767`.
Antes del despliegue usaba `sha256:a9df419a1db359c438c8fe20dbea8d75e4c469839544f24a61c6d41e212d7ca4`.
El build Compose reutiliza las capas del artefacto validado; el checksum del JAR confirma el
mismo código/contrato compilado. OpenAPI servido se compara íntegro con el archivo local.

Informes: [unitarias/HTTP](../../../recipe-services/build/reports/tests/test/index.html) y
[SDK/emulador](../../../recipe-services/build/reports/tests/integrationTest/index.html).
El smoke limpia sus cuatro contenedores/red; el contenedor adicional
bebilo-recipes-category-firestore se eliminó después de integrationTest.
No quedan recursos auxiliares de esta entrega. Datos exclusivamente en emuladores demo.

### Ajustes de validación

El primer test mostró una expresión Unit innecesaria en el helper; se retiró y la compilación
posterior fue correcta. La validación estática inicial con Python no encontró PyYAML;
se ejecutó con Ruby YAML disponible sin instalar dependencias. No hubo fallos de tests,
compilación, smoke ni despliegue. Las comprobaciones previstas no se cuentan como ejecutadas.

## Revisiones finales y limitaciones

- HEADs/gitlinks siguen siendo los registrados arriba. El cambio y el servicio previo permanecen
  sin commit; esos SHA identifican sus bases, no la nueva implementación. Sin commits/push,
  cambios de remoto ni sustitución de submódulos; modificaciones locales preexistentes conservadas.
- No se ejecutaron migración, borrado ni escrituras en Firebase real. El usuario confirmó que
  no había información almacenada. El futuro productor deberá guardar una categoría válida.
- Los GET no clasifican ni reparan categorías, no consultan IA/AIconfig y no calculan placeholders.
  Ausencia/null/desconocido/tipo incorrecto son errores de integridad con 500 INTERNAL_ERROR.
- Integración móvil autenticada real pendiente como en recipes-get. Dobles, emulador y gateway
  aislado acreditan backend/contrato, no un recorrido móvil real. No se añade creación de recetas.
