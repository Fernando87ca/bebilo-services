# Plan — Categorías de ingredientes

Estado: completado; backend validado y desplegado. Fecha: 2026-10-12.
[Spec](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Implementar el contrato obligatorio de category en lectura de Recipes y desplegarlo localmente.
Sin migración por ausencia de datos confirmada por el usuario, sin endpoints de creación ni
cambios móviles. Conservar los cambios sin commit del servicio y agregador.

## Estado actual y evidencia

Recipes tiene ambos GET implementados, Firestore por UID, estado obligatorio y OpenAPI;
ninguna capa lee/publica category. La app ya exige ocho categorías sin fallback.
Agregador HEAD `07b5e89941a9fba554e321e7be5b469f1c8ae7d5`, Recipes HEAD/gitlink
`889fe7f40be666138f3c6b0a19e7997d6f8b72bb`, app HEAD
`f34f092f36dfe4c93969a1f0f63f818b6af7176e`, todos con cambios locales relevantes.
El código actual de Recipes aún está sin commit; su SHA identifica la base.

## Diseño propuesto

Enums propios Domain/DTO, field requerido y mapper exhaustivo entre ellos. Mapper de Firestore
reconoce solo los ocho strings y usa el error de contenido inválido existente para cualquier
otro valor/tipo/ausencia. No cambia repositorio, autenticación ni consultas.
OpenAPI incluye enum y category en required, sin nullable/default. Fixtures válidos siempre
incluyen category; corrupción prueba ambos endpoints con SDK real en emulador aislado.

## Fases y dependencias

1. Registrar contrato/decisiones/tareas y estado inicial.
2. Modelos, mappers, OpenAPI y documentación del servicio.
3. Pruebas unitarias/HTTP, SDK/emulador, distribución, imagen y smoke aislado.
4. Redesplegar solo Recipes y verificar health, OpenAPI directo/gateway y Swagger central.
5. Registrar evidencia, revisiones y pendientes; revisar enlaces y whitespace.

## Validación y entrega

Desde Recipes: test local, build local/cloud, integrationTest con emulador local,
installDist/distTar, docker build y scripts/isolated-smoke.py. El smoke prueba gateway sin
AIconfig y limpia contenedores/red. Ejecutar scripts/local-smoke.py tras redespliegue,
comparar OpenAPI servido con el local y comprobar Swagger central. Datos solo aislados;
no crear recetas/cuentas en Firebase real. Integración móvil real es validación independiente.
