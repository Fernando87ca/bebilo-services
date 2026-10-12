# Plan — Consulta de recetas

Estado: backend implementado/desplegado; pendiente de validación móvil real. Fecha: 2026-10-12.
[Spec](spec.md) · [Tareas](tasks.md) · [Evidencia](implementation.md).

## Objetivo y alcance

Servicio de lectura de recetas completas, por cuenta Firebase, con las rutas y modelos del
cliente móvil. No incluye creación, generación, edición, eliminación, migraciones ni cambios
Android/iOS. El usuario autorizó el plan completo, incluido despliegue local y pruebas aisladas.

## Estado inicial y evidencia

El agregador incluye `recipe-services`, gitlink `889fe7f40be666138f3c6b0a19e7997d6f8b72bb`, con
remoto correcto y solo base Gradle/Kotlin JVM 23. Agregador de partida
`07b5e89941a9fba554e321e7be5b469f1c8ae7d5`; ambos checkouts limpios. El contrato móvil vigente
proviene del checkout `f34f092f36dfe4c93969a1f0f63f818b6af7176e` y sus cambios locales.

Servicios existentes: Ktor 3.5.1, Kotlin 2.4.0, Firebase Admin 9.10.0 y DI de Ktor.
Despliegue: contexto Docker `rancher-desktop`, red externa `bebilo-gateway`, proyectos Compose
separados, Traefik 3.7.13 y Swagger central. Se reutilizan estas convenciones.

## Diseño

Capas API/Domain/Data/DI/Config/Plugins. DTOs separados de Domain, mapper explícito desde tipos
nativos de Firestore, validación de integridad en Domain y errores en StatusPages. Auth resuelve
UID antes de resolver la dependencia Firestore; los GET no dependen de AIconfig.

Persistencia por UID, con identidad pública `summary.id` separada del documentId. Consultas
sin filtros de pantalla, paginación o truncamiento. Cualquier corrupción o duplicado en los
resultados falla; no hay escritura o reparación durante lectura.

## Fases

1. Registrar SDD y contrato, reutilizar organización Git existente.
2. Implementar servicio, modelos, identidad, repositorio, validación y OpenAPI.
3. Probar dominio/HTTP/mapper/Auth; probar SDK real en Firestore Emulator.
4. Preparar distribución e imagen; smoke con Auth simulado y Traefik aislado.
5. Desplegar Recipes en Rancher y añadir Swagger central; comprobar gateway y documentar evidencia.

## Validación y entrega

Ejecutar desde `recipe-services` tests, build local y cloud, distTar/installDist e integrationTest
con FIRESTORE_EMULATOR_HOST. Smoke reproducible en `scripts/isolated-smoke.py` con el mismo
contenedor Recipes de despliegue y sin credenciales reales. Contenedores/red de prueba se limpian.
Comprobar health/OpenAPI/Swagger y 401 con bearer ausente/inválido en el despliegue habitual,
y health/OpenAPI de los demás servicios por gateway. Revisar YAML, enlaces y diff.

Registrar SHAs y cambios locales; conservar submódulo/remoto sin crear otro repositorio.
El gitlink solo se actualizará al integrar un commit del servicio; no representar código sin
commit mediante un SHA inexistente. Integración móvil real continúa pendiente de evidencia.
