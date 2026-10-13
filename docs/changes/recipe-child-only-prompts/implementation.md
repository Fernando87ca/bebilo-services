# Evidencia — prompts exclusivos de cocina infantil

Estado: completado (recursos y validación local). Fecha: 2026-10-13. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones y entorno

Agregador HEAD `64f2485bf55f3b485a92a625ac458e4713257697`; Recipes HEAD `a28d55e93e2c95be0c1a189d7894b590d6996e90`. Checkouts inicialmente limpios. Cambios sin commit: tres prompts en Recipes y documentación SDD/índice en el agregador. Sin commits nuevos ni cambios de gitlinks; otros servicios intactos.

## Cambios realizados

Composición restringe todos los campos a recetas infantiles, toma ageMonths del contexto servidor, adapta cocción/textura/tamaño/presentación a todos los bebés y prioriza restricciones sobre preferencias. Texto libre no puede cambiar su función. Una solicitud incompatible devuelve JSON sin ingredientes, métodos ni instrucciones; validateComposition/validateStoredContent lo rechazan y el worker intenta persistir Error. No se introduce un nuevo DTO de rechazo ni se elimina la solicitud inicial guardada.

Planificación visual solo describe plato/procesos compatibles con las adaptaciones existentes; un plan incompatible usa images vacío y validateImagePlan lo descarta. Rendering limita la escena a cocina infantil y pide no generar una imagen incompatible. No cambia la semántica de fotos opcionales.

## Evidencia

| Fecha | Tarea / AC | Directorio y comprobación | Resultado |
| --- | --- | --- | --- |
| 2026-10-13 | T-01,T-02 / AC-01,AC-02 | Revisión de prompts, schemas, KoogRecipeGenerator, RecipeGenerationService y RecipeValidation | Salidas vacías permitidas por esquemas pero rechazadas por validadores. Planificación recibe receta con adaptaciones, sin edades individuales. |
| 2026-10-13 | T-03 / AC-03 | Recipes: `./gradlew test build -Penv=local`, después de editar los recursos | Correcto; 148 pruebas, cero fallos, errores u omisiones. Proveedor simulado. |
| 2026-10-13 | T-03 / AC-03 | Recipes: `./gradlew build installDist distTar -Penv=cloud` | Correcto; tareas de test/build reutilizan resultados locales vigentes; installDist ejecutado. |
| 2026-10-13 | T-03 / AC-03 | Comparación byte a byte de los tres prompts dentro del JAR con los recursos fuente | Correcto; recursos nuevos empaquetados. |
| 2026-10-13 | T-03 / AC-03 | Enlaces relativos del cambio y `git diff --check` en agregador y Recipes | Correcto. |

## Límites

No se ha llamado al LLM real ni se ha evaluado un corpus de solicitudes adversarias o adecuación alimentaria real. Los prompts no son una garantía de filtrado semántico; el servidor valida estructura e invariantes, no todo el significado del texto. El rechazo de composición depende de que el modelo siga el prompt. No se ejecutaron emuladores/Docker/smoke: no se cambian rutas, persistencia ni infraestructura. No se desplegó: el servicio en ejecución necesita reconstrucción y despliegue para cargar los nuevos recursos.
