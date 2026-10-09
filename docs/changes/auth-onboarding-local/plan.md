# Plan — Onboarding autoritativo y despliegue local

Estado: completado. Fecha: 8 de octubre de 2026. [Spec](spec.md) · [Tareas](tasks.md) · [Implementación](implementation.md).

## Objetivo y alcance

Implementar el estado de Welcome en Auth y el 404 relacionado en AI Configuration, alinear OpenAPI y desplegar ambos servicios localmente. Baby y Profile permanecen fuera del cambio.

## Estado inicial

Los repositorios y submódulos estaban limpios. Auth forzaba `isFirstTimeAccess=true` en las respuestas y el cliente Identity Toolkit calculaba `false` en login email/password. AI Configuration ya validaba el bearer antes de resolver Firestore, tenía credencial privada montada y estaba desplegado junto con Auth mediante Rancher Desktop.

## Diseño y fases

1. Propagar `localId` desde Identity Toolkit y leer el custom claim mediante Firebase Admin.
2. Añadir el PUT idempotente, errores 401/404/503/500 y pruebas de claims, UID y persistencia.
3. Propagar 404 en AI Configuration y actualizar ambos OpenAPI.
4. Ejecutar tests/builds, reconstruir Auth e IA en Rancher Desktop y hacer smoke autenticado con limpieza.
5. Registrar comandos, resultados, revisiones y cualquier limitación real.

## Validación y entrega

Ejecutar `test`, `build -Penv=local`, `build -Penv=cloud` y `distTar` desde cada servicio afectado. Verificar health/OpenAPI directo y por Traefik, transición true→false, idempotencia, reinicio, dos UID, cuenta eliminada y GET/POST/GET de IA. No mostrar secretos ni publicar repositorios.
