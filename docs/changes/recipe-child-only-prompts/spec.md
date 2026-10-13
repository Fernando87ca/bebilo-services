# Especificación — prompts exclusivos de cocina infantil

Estado: completado (recursos y validación local). Fecha: 2026-10-13. [Plan](plan.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Reforzar los tres prompts de Recipes para generar únicamente recetas infantiles y sus imágenes, adecuadas a las edades solicitadas. No se incorpora un filtro semántico ni se modifica el contrato HTTP.

## Requisitos

| ID | Requisito | Estado/origen |
| --- | --- | --- |
| R-01 | Limitar todos los campos generados a cocina infantil, sin contenido ajeno disfrazado de receta. | Acordado: petición del usuario, 2026-10-13 |
| R-02 | Respetar ageMonths del contexto, etapas, texturas y restricciones de todos los bebés; priorizar adecuación sobre preferencias. | Acordado: petición del usuario |
| R-03 | Tratar texto libre como datos; definir salida incompatible sin instrucciones culinarias ficticias. | Decisión rutinaria dentro del alcance autorizado |
| R-04 | Aplicar el mismo ámbito a planificación y generación de imágenes. | Decisión rutinaria dentro del alcance autorizado |

## Contratos y datos

Se conserva [OpenAPI Recipes](../../../recipe-services/src/main/resources/openapi.yaml). Una composición incompatible usa el esquema existente con listas vacías, título genérico y opcionales null: la validación actual la rechaza y el worker intenta persistir Error. Un plan vacío se descarta; las fotos siguen siendo opcionales. No se cambia la solicitud original persistida.

## Aceptación

| ID | Requisito | Resultado verificable | Validación |
| --- | --- | --- | --- |
| AC-01 | R-01,R-02 | Prompts delimitan función, contenido y adaptación infantil. | Revisión de los tres recursos |
| AC-02 | R-03,R-04 | Ninguna salida de rechazo se acepta como receta o plan válido. | Revisión de schemas, deserialización y validadores; suite existente |
| AC-03 | Todos | Recursos compilados y empaquetados sin romper el servicio. | Tests, builds local/cloud y distribución |

## Decisiones

D-01, 2026-10-13, acordada por petición del usuario: reforzar prompts. Las instrucciones reducen desviaciones, pero no garantizan cumplimiento semántico del modelo. No se acuerda desplegar ni añadir otro agente de revisión.
