# Especificación — Consulta de recetas

Estado: acordado; implementación autorizada el 12 de octubre de 2026.
[Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Implementar y desplegar los dos GET que necesita la biblioteca móvil, leyendo exclusivamente
recetas completas persistidas para el usuario autenticado. Módulo existente `recipe-services`,
submódulo del repositorio GitHub `Fernando87ca/bebilo-recipes-services`.

Contrato de origen: [requisitos móviles](../../../../../AndroidStudioProjects/bebilo/docs/changes/app-recipes-implementation/backend-requirements.md).
La fuente local vigente incluye cambios sin commit y `status` obligatorio; el SHA por sí solo
no representa todos sus requisitos. [OpenAPI del servicio](../../../recipe-services/src/main/resources/openapi.yaml)
es la referencia ejecutable del contrato implementado.

## Requisitos

| ID | Requisito | Origen |
| --- | --- | --- |
| R-01 | GET público `/recipes/v1/recipes`: todas las recetas completas, envelope `recipes`, sin filtros/límite/paginación | Contrato móvil y petición |
| R-02 | GET público `/recipes/v1/recipe/{recipeId}`: objeto directo e ID exacto, opaco, decodificado una vez | Contrato móvil |
| R-03 | Verificar bearer con Auth antes de acceder/resolver Firestore; aislar todas las consultas por UID | Contrato móvil |
| R-04 | Preservar contenido persistido, opcionales, imágenes e instrucciones; estado explícito Loading/Ready/Error | Contrato móvil vigente |
| R-05 | Errores JSON definidos, sin defaults, reparaciones ni listados parciales | Contrato móvil |
| R-06 | Subcolección `users/{uid}/recipes` y pruebas con datos solo aislados | Respuestas del usuario al plan |
| R-07 | Despliegue Compose/Rancher, Traefik, OpenAPI y Swagger central como los servicios existentes | Petición y plan autorizado |

## Contratos y datos

Las rutas internas son `/v1/recipes` y `/v1/recipe/{recipeId}`. Traefik retira únicamente el
prefijo `/recipes`. Bearer obligatorio, sin body ni parámetros requeridos. Los parámetros de
query no alteran las consultas. Una biblioteca vacía es 200 `{"recipes":[]}`.

El esquema y las reglas de la sección 4 del documento móvil son obligatorios, incluidos
`status`, todos los arrays requeridos y los discriminadores de instrucciones e imagen.
No se regeneran datos ni se consultan AIconfig, Profile o Babies. Las URLs se conservan;
no se descargan ni se firman durante GET. Fechas Timestamp se convierten conservando el instante.

Persistencia: `users/{uid}/recipes/{documentId}`, mapa completo con forma de `RecipeDto`.
`summary.id` es la identidad pública única por cuenta, independiente del ID físico.
El listado lee toda la subcolección; el individual filtra por igualdad sobre `summary.id`
en esa misma subcolección. Se usa el índice simple automático de ese campo, sin índice compuesto.
No se requiere que exista el documento padre `users/{uid}` para leer su subcolección.
Los lectores rechazan IDs duplicados en sus resultados; Firestore no impone unicidad de campos.
La coherencia de alérgenos exige que cada alérgeno de ingredientes esté declarado con el mismo
código/nombre en el resumen, sin duplicados de código en cada lista; no se deduce ni completa.

| HTTP | Código | Caso |
| --- | --- | --- |
| 400 | VALIDATION_ERROR | ID blanco o codificación inválida que llegue al servicio |
| 401 | UNAUTHORIZED | Bearer ausente/inválido/expirado, Auth rechaza token o cuenta eliminada |
| 403 | FORBIDDEN | Auth deniega permiso general |
| 404 | NOT_FOUND | Receta inexistente/ajena; idéntica respuesta |
| 500 | INTERNAL_ERROR | Documento inválido, duplicados, error interno/configuración o fallo permanente de almacenamiento |
| 503 | SERVICE_UNAVAILABLE | Auth indisponible o Firestore temporalmente indisponible |

Todos contienen `code` y `message` no vacíos. La cancelación se propaga sin convertirla en
respuesta vacía ni error recuperable. No crear colecciones, cuentas, recetas, migraciones ni
estados por defecto en Firebase real durante esta entrega.

## Aceptación

| ID | Requisito | Escenario | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01, R-02 | Rutas, JSON completos, 25 recetas y biblioteca vacía | Tests HTTP, emulador y gateway aislado |
| AC-02 | R-03 | UID A no lista B; receta ajena/inexistente mismo 404; autenticación previa a almacenamiento | Tests y emulador |
| AC-03 | R-02 | ID reservado, porcentajes literales, Unicode y espacios conservados | Tests HTTP y Traefik aislado |
| AC-04 | R-04 | Estados × instrucciones × imágenes, opcionales/decimales/timestamps y códigos nuevos | Tests de contrato/mapper y emulador |
| AC-05 | R-05 | 500/503, corrupción/duplicados, cancelación, sin respuestas parciales ni datos inventados | Tests y smoke aislado |
| AC-06 | R-07 | Builds, Docker, health/OpenAPI/Swagger, 401 real y regresión gateway | Builds y smoke local |
| AC-07 | R-04 | Recorrido autenticado en app móvil contra backend | Pendiente; sin cambios móviles ni fixtures reales |

## Decisiones

| ID | Fecha | Estado | Decisión y origen |
| --- | --- | --- | --- |
| D-01 | 2026-10-12 | Confirmada | Reutilizar submódulo/remoto existentes; inspección Git |
| D-02 | 2026-10-12 | Confirmada | `users/{uid}/recipes`; respuesta del usuario |
| D-03 | 2026-10-12 | Confirmada | Datos de prueba únicamente aislados; respuesta del usuario |
| D-04 | 2026-10-12 | Confirmada | Separar summary.id e ID físico y filtrar por campo; plan autorizado |
| D-05 | 2026-10-12 | Confirmada | Puertos directo 8084/interno 8080, prefijo recipes y Compose Rancher; plan autorizado |
