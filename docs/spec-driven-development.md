# Spec-driven development

Convención ligera para `bebilo-services`, adoptada el 8 de octubre de 2026. No requiere herramientas adicionales.

## Estructura

```text
docs/
  README.md
  spec-driven-development.md
  changes/
    README.md
    _template/
      spec.md
      plan.md
      tasks.md
      implementation.md
    <tema>/
      spec.md
      plan.md
      tasks.md
      review.md          # opcional: revisión/procedencia
      implementation.md # crear al comenzar ejecución
```

- `spec.md`: objetivo funcional, requisitos R-xx, contratos, invariantes, aceptación AC-xx y decisiones D-xx fechadas. Separar confirmado, propuesto y sustituido.
- `plan.md`: evidencia del estado actual, alcance/exclusiones, diseño propuesto, fases, dependencias, validación y preguntas al final.
- `tasks.md`: tareas T-xx, estado, dependencias, requisitos/aceptación y entregables verificables.
- `implementation.md`: registro de cambios realmente ejecutados, revisiones, comandos/resultados y limitaciones. No presentar pruebas previstas como realizadas.

## Ciclo

1. Inspeccionar código, instrucciones y documentación vigente; registrar repositorios y revisiones, incluidos cambios sin commit.
2. Copiar la plantilla a `changes/<tema>` y registrar el cambio en el índice como **borrador**.
3. Resolver decisiones bloqueantes e incorporarlas a la especificación con fecha y origen. Marcar **acordado** cuando el alcance tenga contratos y aceptación suficientes. Un plan no autoriza por sí solo implementación.
4. Con implementación autorizada, pasar a **en curso** y actualizar tareas y evidencia conforme se ejecutan. No pedir aprobación adicional para decisiones rutinarias cubiertas por la petición.
5. Usar **pendiente de validación** cuando falten comprobaciones requeridas; detallar cuáles y por qué.
6. Marcar **completado** únicamente con evidencia de aceptación y contratos/código alineados. Para cambios reemplazados, usar **sustituido** y enlazar al sucesor.

Las tareas usan pendiente / en curso / bloqueada / completada. Un bloqueo debe identificar la decisión o dependencia que falta. Las revisiones de alcance actualizan spec, plan, tareas y pruebas afectadas; las decisiones previas se conservan como sustituidas.

## Contratos y múltiples repositorios

El código inspeccionado describe lo existente; la especificación acordada describe el objetivo. OpenAPI de cada microservicio será la referencia HTTP ejecutable y se actualizará junto al código; no duplicarlo de forma permanente en el agregador. Durante el diseño, especificar método, ruta pública/interna, autenticación, request/response, estados, errores, límites e idempotencia. Para Firebase: propietario, ruta, clave, campos, invariantes, consultas/índices y ciclo de vida.

El plan transversal canónico vive aquí. Enlazar documentación móvil desde el repositorio de la app sin copiar sus historiales completos. Los enlaces absolutos a la app son referencias locales del checkout actual y deben ajustarse si cambia su ubicación. El plan original trasladado queda como antecedente; las futuras respuestas se incorporan aquí.

Cada entrega entre repositorios registra SHA del agregador, de cada servicio afectado y de la app, más cambios sin commit relevantes. Respetar submódulos y actualizar gitlinks al integrar commits disponibles. Un nuevo servicio requiere decidir su organización Git además de `includeBuild`, configuración, gateway y OpenAPI.

## Validación

Seguir `AGENTS.md` del servicio afectado y ejecutar desde su directorio. Distinguir pruebas unitarias, HTTP con dobles, integración Firebase y recorrido móvil real. No declarar integración por pasar mocks, ni análisis efectivo con NO-SOURCE. Registrar entorno sin secretos, comando, resultado, fecha y requisitos cubiertos. Para documentación, revisar enlaces, trazabilidad y whitespace; no son necesarios builds.
