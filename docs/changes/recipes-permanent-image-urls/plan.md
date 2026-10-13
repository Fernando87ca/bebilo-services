# Plan — URLs permanentes de imágenes

Estado: pendiente de validación real de Storage. Fecha: 2026-10-13. [Spec](spec.md) · [Tareas](tasks.md).

## Objetivo y alcance

Implementar el plan aprobado en Recipes y documentar en agregador. App excluida.

## Estado actual y evidencia

Anteriormente el DTO ya incluía fotos opcionales, pero sus URLs apuntaban al GET autenticado
de Recipes. El plan visual exigía portada. Firestore conserva generationImages.
Revisiones y estado del bucket en [implementation.md](implementation.md).

## Diseño

Upload retorna referencia y URL Firebase con token UUID en metadata. URL codifica bucket,
objeto y token. Worker persiste tras subida. Sin plan o foto no hay imagen. Retirar ruta,
lectura binaria y DI exclusiva. CLI independiente de HTTP inventaría collectionGroup recipes,
valida identidad/opacidad/propietario, objetos y enlaces históricos; aplica con precondiciones.
No registrar URLs/tokens. No escribir desde GET. No migrar fixtures en Firebase real.

## Fases y dependencias

Contrato/generación → pruebas → inventario real → migración si procede → redespliegue.
Si bucket no disponible y hay enlaces antiguos, mantener despliegue anterior hasta resolverlos.

## Validación y entrega

Checks del AGENTS de Recipes, smoke aislado con Firebase HTTP simulado y Firestore real
emulado, smoke local sin cuentas válidas ni datos de prueba reales. Registrar la diferencia
entre dobles/emulador y bucket real. No hay decisiones abiertas.
