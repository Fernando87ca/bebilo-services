# Plan — prompts exclusivos de cocina infantil

Estado: completado. Fecha: 2026-10-13. [Spec](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Estado inicial

Checkouts limpios. Los prompts existentes ya aludían a bebés y restricciones, pero no delimitaban explícitamente todo el contenido ni resolvían el rechazo dentro del esquema. Koog carga recursos empaquetados y calcula la clave de caché a partir del prompt y schema.

## Alcance y diseño

Modificar composition.txt, visual-planning.txt e image-rendering.txt. Usar edad resuelta por el servidor en composición; conservar adaptaciones ya presentes en la receta para imágenes (su entrada no incluye el contexto completo de edades). Explicitar prioridad sobre texto libre y preferencias incompatibles. Usar listas vacías del esquema existente para que los validadores rechacen composición o plan, sin alterar DTOs ni HTTP.

## Validación y entrega

Revisar consistencia con esquemas y validadores, ejecutar tests y builds local/cloud, verificar empaquetado de recursos y enlaces/diff. No ejecutar emuladores, Docker ni smoke de rutas para este cambio de instrucciones sin modificaciones de infraestructura, persistencia o rutas. Registrar que los dobles del proveedor no prueban obediencia del LLM real. No hay decisiones bloqueantes.
