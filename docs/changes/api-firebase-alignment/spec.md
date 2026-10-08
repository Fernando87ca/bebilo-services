# Especificación — Alineación API y Firebase

Estado: implementada y pendiente de validación real completa. Fecha: 8 de octubre de 2026. [Plan](plan.md) · [Tareas](tasks.md) · [Implementación](implementation.md).

## Requisitos

- R-01: Auth devuelve UID y email verificados; los servicios usan UID como propietario.
- R-02: Profile posee solo identidad parental y expone GET/POST/PUT `/user` con nombre, identificación y foto nullable.
- R-03: Babies conserva sus rutas, usa UUID del cliente, elimina `parentName` y mantiene una selección única.
- R-04: IA vive en `aiconfig-services`, con GET/POST y una configuración completa por UID.
- R-05: Firestore vacío arranca sin migración; una cuenta Auth sin perfil recibe `PROFILE_NOT_FOUND`.
- R-06: OpenAPI, gateway, persistencia y errores coinciden con los contratos implementados.

## Contratos

### Auth

`POST /auth/v1/user-info` responde `{"uid":"...","email":"..."}`. Ambos valores proceden del token Firebase verificado.

### Profile

GET/POST/PUT públicos `/profile/v1/user`, internos `/v1/user`. POST devuelve 201 o 409; GET/PUT, 200 o 404. POST y PUT requieren un objeto completo:

```json
{"name":"Alex","identification":"prefer_not_to_say","photo":null}
```

Nombre tras trim: 1–50 caracteres. Identificación: `mother | father | prefer_not_to_say`. Foto: Base64 puro de JPEG cuadrado, máximo 1024 × 1024 y 1 MiB decodificado. Body máximo 2 MiB; exceso → 413. Respuestas de éxito incluyen los tres campos y nunca usan 204.

Persistencia: `users/{uid}` con nombre, identificación y referencia nullable. Objetos privados versionados `profile-photos/{uid}/<id>.jpg`. La referencia cambia solo tras subir la imagen; después se elimina la anterior. Fallos previos compensan la subida y fallos de limpieza se reintentan de forma segura con objetos antiguos.

### Babies

Se conservan `/get-babies`, `/register-baby`, `/update-baby/{babyId}`, `/delete-baby/{babyId}` y `/set-initial-baby/{babyId}` bajo `/baby/v1`. Request/response no contienen `parentName`. El request completo incluye `id` UUID.

Persistencia: `users/{uid}/babies/{babyId}`, campos funcionales, `isInitial` y `createdAt` de servidor. Un UUID nuevo devuelve 201; un reintento idéntico devuelve 200; contenido diferente con el mismo UUID devuelve 409. El primer bebé se activa. Selección: última transacción aceptada. Al borrar el activo se elige menor `createdAt` e ID como desempate.

### IA

GET/POST públicos `/aiconfig/v1/ai-configuration`, internos `/v1/ai-configuration`. GET sin documento devuelve 200 con defaults sin escribir. POST reemplaza los tres campos y devuelve 200.

```json
{"instructionStyle":"steps","photoMode":"none","modelId":"gpt-6-luna"}
```

Valores: `steps | narrative`, `none | single | multiple`, `gpt-6-luna | gpt-6.1-sol | gpt-6-astra`. Persistencia `aiConfigurations/{uid}` sin metadatos.

## Errores y aceptación

Todos los errores usan `{"code":"...","message":"..."}` y estados 400, 401, 404, 409, 413, 502, 503 o 500 según contrato. Ningún body acepta UID o email.

- AC-01: cuenta Auth sin perfil recibe 404 `PROFILE_NOT_FOUND` y puede crear un perfil completo.
- AC-02: Profile crea, lee y reemplaza nombre, identificación y fotografía; null elimina foto.
- AC-03: fotos inválidas o demasiado grandes no modifican datos; reemplazos fallidos no dejan referencias rotas.
- AC-04: Babies crea idempotentemente, lista, actualiza y borra por UID sin `parentName`.
- AC-05: existe como máximo un bebé activo; primer bebé y sustitución tras borrado son deterministas.
- AC-06: IA devuelve defaults sin documento y persiste reemplazos completos sin tocar Profile.
- AC-07: tokens inválidos y cuentas distintas no leen ni modifican recursos ajenos.
- AC-08: gateway, rutas directas y OpenAPI publican el mismo contrato.

## Decisiones

UID como clave; corte inmediato de rutas Profile; rutas Babies conservadas; ID Baby del cliente; IA como submódulo SSH en `main`; fotos en Storage versionado; validación local contra Firebase real y sin despliegue cloud. La app queda fuera de esta entrega.
