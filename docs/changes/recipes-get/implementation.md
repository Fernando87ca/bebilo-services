# Implementación — Consulta de recetas

Estado: backend implementado, validado y desplegado; **pendiente de validación móvil real**.
Fecha: 12 de octubre de 2026. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

- Agregador base/final HEAD: `07b5e89941a9fba554e321e7be5b469f1c8ae7d5`, con cambios sin commit en SDD, índices, Swagger central y submódulo Recipes.
- Recipes base/final HEAD y gitlink: `889fe7f40be666138f3c6b0a19e7997d6f8b72bb`, con implementación sin commit. El SHA identifica la base, no el nuevo código.
- Remoto Recipes conservado: `git@github.com:Fernando87ca/bebilo-recipes-services.git`; `.gitmodules` e `includeBuild` ya eran correctos y no se modificaron.
- Auth `31668b90befd9abe381b0ecb504ffcd614009a44`, Babies `6fc97911e6eb813396f3fb19e8551ed99ad06cf8`, Profile `a11fe27efc8dfd92792506f2bcb4142f52d93d76`, IA `2445451cae8b9c8c86b705e157b916d54c0cc84e`: sin cambios.
- App de referencia: `f34f092f36dfe4c93969a1f0f63f818b6af7176e`, con cambios locales preexistentes en requisitos y capas Data/Domain/MVI de recetas. Se leyó su estado y contrato vigente, incluidos los estados obligatorios. No se modificó la app.
- Entorno: macOS, Docker context `rancher-desktop`, red habitual `bebilo-gateway`, Traefik 3.7.13, JRE 23 en imagen Recipes.
- Imagen desplegada: `recipes-service:local`, ID `sha256:7ba4b6f287493e5e1f3bcf770534ff78cb031bb79d5bff9c38ccfaa51b99d06d`.
- Firestore Emulator: imagen fijada por digest `gcr.io/google.com/cloudsdktool/google-cloud-cli@sha256:be2f0e582a94c9e06e7a384fe2b3b39d590eeb8da65a110eda991598c100fe80`, proyectos exclusivamente `demo-bebilo-recipes` y `demo-bebilo-recipes-smoke`.

## Cambios realizados

- Servicio Ktor con GET internos `/v1/recipes` y `/v1/recipe/{recipeId}`, publicados bajo `/recipes`.
- DTOs compatibles con el cliente, Domain separado, casos de uso, mapper de Firestore y DI nativa. Auth `/v1/user-info` resuelve UID antes de resolver Firestore, también con bearer inválido.
- Lectura de `users/{uid}/recipes/{documentId}`; listado completo e individual filtrado por `summary.id` dentro de la cuenta. ID público separado del físico y decodificado una vez mediante los parámetros de path de Ktor.
- Validación estricta del contenido persistido, estados explícitos, alérgenos, IDs únicos, valores numéricos, fechas e imágenes. No defaults para ocultar corrupción, no recorte de resultados ni reparación/escritura.
- StatusPages devuelve los errores acordados sin detalles internos y propaga cancelación. Fallos temporales de Firestore/Auth son 503; fallos permanentes, corrupción y duplicados son 500.
- OpenAPI, README, AGENTS y plantilla del servicio; Dockerfile/distribución, Compose en puerto 8084 y prefijo Traefik con límite de segmento. `.dockerignore` restringe el contexto a la distribución, excluyendo secretos y configuración local.
- Swagger central incluye la quinta fuente Recipes; únicamente se recreó Swagger UI del proyecto gateway. Los demás servicios y Traefik siguieron activos.
- Scripts reproducibles [smoke aislado](../../../recipe-services/scripts/isolated-smoke.py) y [smoke local de lectura](../../../recipe-services/scripts/local-smoke.py).

## Evidencia

| Fecha | Tarea / AC | Comprobación ejecutada | Resultado |
| --- | --- | --- | --- |
| 2026-10-12 | T-02/T-04 / AC-01–05 | `./gradlew test -Penv=local` | 58 pruebas, 0 fallos/errores/omisiones |
| 2026-10-12 | T-04 / AC-01–05 | `FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local` | 2 pruebas con SDK real y emulador, 0 fallos/errores/omisiones |
| 2026-10-12 | T-04 / AC-06 | `./gradlew build -Penv=local` y `./gradlew build -Penv=cloud` | Ambos BUILD SUCCESSFUL; build cloud final reutiliza tareas correctas |
| 2026-10-12 | T-04 / AC-06 | `./gradlew test build installDist distTar -Penv=local`; tras ajuste de OpenAPI `./gradlew test build distTar -Penv=local` | Distribución e imagen con código/contrato finales; BUILD SUCCESSFUL |
| 2026-10-12 | T-05 / AC-01–05 | `python3 scripts/isolated-smoke.py` contra imagen Docker final | 41 comprobaciones correctas; Firestore Emulator, Auth simulado, Recipes real y Traefik aislado |
| 2026-10-12 | T-06 / AC-06 | `docker --context rancher-desktop compose -p bebilo-recipes up -d --build` desde Recipes | Proyecto `bebilo-recipes`, contenedor activo, puerto directo 8084 |
| 2026-10-12 | T-06 / AC-06 | `docker --context rancher-desktop compose -f docker-compose.traefik.yml -p bebilo-gateway up -d --no-deps swagger-ui` | Swagger UI recreado con Recipes añadido |
| 2026-10-12 | T-06 / AC-06 | `python3 scripts/local-smoke.py` | 29 comprobaciones de lectura correctas, incluidos los otros cuatro servicios |
| 2026-10-12 | T-07 | YAML de OpenAPI/Compose/plantilla y comparación de propiedades con DTOs | YAML válido; los nueve modelos concretos coinciden en propiedades; variantes/discriminadores cubiertos por tests |
| 2026-10-12 | T-07 | Enlaces locales, sintaxis de scripts y `git diff --check` en agregador/Recipes | Correctos |

Cobertura automatizada: 18 combinaciones estado × instrucciones × modo de imagen en ambos GET;
25 recetas sin truncamiento, biblioteca vacía, aislamiento A/B, mismo 404 para ajena/inexistente,
IDs `one/two ?#%`, porcentajes literales, Unicode, espacios y `+`, autenticación antes de almacenamiento,
campos opcionales/null/tags ausentes, cantidades decimales, códigos nuevos, timestamps,
contenido idéntico entre ambos GET, ausencia de AIconfig, duplicados/corrupción, errores y cancelación.

El smoke local verificó health/OpenAPI de los cinco servicios, Swagger de Recipes directo,
Swagger central y su quinta fuente, ambos GET sin token/con token inválido directo y por gateway,
IDs reservados llegando a Auth y rechazo de un prefijo ajeno `/recipes-other`.
No se hizo ninguna petición autenticada válida que consultara recetas reales.

Informes locales: `recipe-services/build/reports/tests/test/` y `recipe-services/build/reports/tests/integrationTest/`.
Los fixtures SDK se eliminan en finally; el smoke limpia sus cuatro contenedores y red.
El emulador auxiliar de la validación Gradle también se elimina al cerrar la entrega.

### Ajustes durante validación

La primera compilación detectó un import de logger faltante; se corrigió antes de los resultados
finales. La primera prueba de timestamp esperaba nanosegundos que Firestore ya había truncado
a microsegundos al persistir; se ajustó el fixture al instante realmente guardado. El mapper
conserva el Timestamp leído y su prueba unitaria comprueba además precisión de nanosegundos.
El primer smoke con distribución en el host detectó Java 21 local frente al bytecode Java 23;
el smoke final ejecuta la imagen Docker con JRE 23, igual que el despliegue. Estos primeros
intentos no se consideran evidencia de aceptación; los resultados finales anteriores sí.

## Pendientes y limitaciones

- AC-07: recorrido móvil autenticado real pendiente. Los tests con dobles, emulador y gateway
  no acreditan integración de Android/iOS con una cuenta y recetas reales.
- No se crearon cuentas o recetas en Firebase real ni se ejecutaron migraciones. Si aún no hay
  recetas, la consulta autenticada válida devolverá una biblioteca vacía. Quien persista futuras
  recetas debe guardar el esquema completo, estado explícito y summary.id único por cuenta.
- No se hicieron commits ni push. Los cambios revisables están en los checkouts actuales; el
  remoto/submódulo existentes se conservan. El gitlink mantiene la base hasta integrar un commit.

## Redespliegue del catálogo Swagger — 12 de octubre de 2026

Petición posterior del usuario: redesplegar los servicios y Traefik para actualizar el catálogo.
Se conservaron las revisiones del agregador y los cinco servicios registradas arriba, incluidos
los cambios sin commit preexistentes. No se modificó código ni contratos HTTP.

- Se ejecutó `./gradlew distTar -Penv=local --console=plain` desde cada uno de los cinco
  servicios: todos finalizaron con `BUILD SUCCESSFUL`.
- En el contexto `rancher-desktop`, se ejecutó `docker compose -f <servicio>/docker-compose.yml
  -p <proyecto> up -d --build --force-recreate` para `bebilo-auth`, `bebilo-baby`,
  `bebilo-profile`, `bebilo-aiconfig` y `bebilo-recipes`.
- Se ejecutó `docker --context rancher-desktop compose -f docker-compose.traefik.yml
  -p bebilo-gateway up -d --force-recreate`: Traefik y Swagger UI recreados.
- Los siete contenedores quedaron activos, sin reinicios, entre las 12:55:34 y 12:55:56
  (Europe/Madrid). La imagen Recipes activa tras este redespliegue es
  `sha256:a9df419a1db359c438c8fe20dbea8d75e4c469839544f24a61c6d41e212d7ca4`.
- `python3 recipe-services/scripts/local-smoke.py`: 29 comprobaciones correctas.
- Comprobación adicional de health directo: HTTP 200 en los cinco servicios. Los diez
  OpenAPI servidos directamente y por gateway coinciden byte a byte con los contratos locales.
- El inicializador de Swagger publica exactamente las cinco fuentes Auth, Baby, Profile,
  AI Configuration y Recipes. La API del dashboard de Traefik confirma los seis routers Docker
  habilitados, incluidos Swagger UI y los cinco servicios.

Catálogo verificado: `http://api.bebilo.localhost:8088/swagger/`.
