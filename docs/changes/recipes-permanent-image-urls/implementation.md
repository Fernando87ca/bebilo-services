# Implementación — URLs permanentes de imágenes

Estado: pendiente de validación real de Storage. Fecha: 2026-10-13.
[Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

Agregador: 9d10bdc11080dafeaa39a4a374ec5d84931a1e8a.
Recipes: cc147cf7a14d707f6c3580f51bacecdf5ad5384c. Ambos limpios al iniciar.
Entrega sin commits: documentación nueva/actualizada del agregador; código, contrato,
prompts, pruebas, scripts y documentación de Recipes modificados dentro del submódulo.
Gitlinks y remotos conservados. No cambios en otros servicios ni en la app.
Docker Rancher Desktop, proyecto Compose bebilo-recipes, red/gateway bebilo-gateway.
Imagen validada/desplegada: sha256:b5a2418d30fbd0bc816258e290577aa228cb9e59e050b020c1ae47aca8e1d355.

## Cambios realizados

- Upload privado JPEG con UUID en firebaseStorageDownloadTokens; devuelve referencia y URL
  HTTPS Firebase con bucket/objeto/token codificados. Worker persiste URL y generationImages
  únicamente después de subir. No hay ACL pública ni firma temporal.
- Se elimina GET de imágenes, su caso de uso, lectura binaria, resolución de referencias
  exclusiva del endpoint y DI. Los GET de receta/listado siguen autenticados y no escriben
  ni consultan Storage. DTO conserva portada/imagen por paso/párrafo opcionales y altText.
- La IA puede planificar cero imágenes, solo proceso sin portada, o portada opcional.
  none/single/multiple conservan límites. Errores de fotos permiten Ready con texto válido.
- CLI --inventory-image-urls y --migrate-image-urls: valida documentos/identidades, rutas
  históricas del origen configurado, propietario y referencias exactas; verifica objeto JPEG.
  Conserva tokens y metadatos, usa metageneration para Storage y updateTime para Firestore.
  Solo cambia summary/instructions; preserva estado/timestamps. Repetición sin cambios.
  Logs contienen contadores/categorías de error, nunca URLs, tokens ni identidad de cuenta.
- OpenAPI, README, AGENTS y SDD actualizados. Se sustituye diseño previo de GET autenticado
  y propuesta conversacional de enlaces firmados de una hora.

## Evidencia

Todos los comandos del servicio se ejecutaron desde recipe-services (o equivalente -p).

| Fecha | Tarea / AC | Comando o comprobación | Resultado |
| --- | --- | --- | --- |
| 2026-10-13 | T-01/T-02 / AC-01/02/03 | ./gradlew test installDist distTar -Penv=local | Correcto; 173 pruebas unitarias/HTTP, 0 fallos/omisiones |
| 2026-10-13 | T-03 / AC-04 | ./gradlew build installDist distTar -Penv=local | Correcto |
| 2026-10-13 | T-03 / AC-04 | ./gradlew build -Penv=cloud | Correcto; 173 pruebas, no despliega cloud |
| 2026-10-13 | T-03 / AC-04 | FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest installDist distTar -Penv=local | 5 pruebas, 0 fallos/omisiones; emulador demo iniciado y eliminado para este cambio |
| 2026-10-13 | T-03 / AC-04 | docker build -t recipes-service:local . | Correcto; distribución sin credenciales locales |
| 2026-10-13 | T-02 / AC-03 | python3 scripts/isolated-smoke.py | 112 comprobaciones correctas, limpieza de contenedores/red propia |
| 2026-10-13 | T-02/T-04 | docker --context rancher-desktop compose -p bebilo-recipes run --rm --no-deps -T recipes-service --inventory-image-urls | bucketAvailable=false; scanned=0 legacyRecipes=0 changed=0 issues=0; no escrituras reales |
| 2026-10-13 | T-03 / AC-04 | docker --context rancher-desktop compose -p bebilo-recipes up -d --no-build recipes-service | Contenedor recreado con imagen reconstruida y validada; running |
| 2026-10-13 | T-03 / AC-04 | curl http://localhost:8084/health; python3 scripts/local-smoke.py | OK y 37 comprobaciones de lectura correctas |
| 2026-10-13 | Documentación | Enlaces locales de documentos modificados y git diff --check en ambos repositorios | Correcto |

El smoke aislado usa Recipes, Koog, SDK Firebase y Traefik reales; Firestore Emulator demo;
Auth/AI Configuration/Babies/OpenAI y Firebase/GCS HTTP simulados. Verifica descarga sin
Bearer, token incorrecto rechazado, 404 de ruta retirada, selección de fotos y estados/SSE.
Convierte una receta fixture al contrato antiguo: inventario sin escrituras, conservación de
token existente, creación de token ausente conservando metadatos ajenos, cambio de URLs
Firestore y segunda ejecución sin cambios. No usa Firebase real ni llamadas LLM reales.
Se ajustó el doble GCS a PATCH mediante POST y mezcla de claves de metadata, conforme a
[la semántica oficial](https://docs.cloud.google.com/storage/docs/json_api#partial-updates).
Los fallos iniciales de compilación/fixtures y del doble se corrigieron antes de los resultados finales.

## Pendientes y limitaciones

El SDK no encontró accesible el bucket configurado bebilo-dev.firebasestorage.app
(bucketAvailable=false). No se ha validado subida/descarga en ese bucket ni recorrido móvil.
No se atribuye esta observación a una causa nueva de facturación o permisos sin comprobarla.
La integración con dobles/emulador no equivale a validación de Storage real.

El inventario Firestore real devolvió 0 documentos recipes: no había enlaces que migrar,
por lo que no se ejecutó --migrate-image-urls en Firebase real ni se crearon datos de prueba.
Cuando el bucket esté disponible, falta comprobar el flujo real con uso autorizado.
No se han cambiado facturación, permisos/IAM, reglas de Storage ni configuración de la app.
La app recibe URLs directas y no requiere renovación temporal; debe omitir imágenes ausentes
sin enviar su Bearer a Firebase. El cambio permanece pendiente de esa validación real.
