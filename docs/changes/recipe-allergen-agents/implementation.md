# Evidencia — Agentes de alérgenos y alternativas

Fecha: 2026-10-14. Estado: completado; validación funcional con proveedor LLM simulado.

## Revisiones iniciales

- Agregador: 2ea74cde6a094207996cf9ebabd2c94c39aaadd8.
- Recipes: 2019e2cec224275bce43815c9dc0f09b13a826ca.
- Cambios locales previos: índices docs, recipe-custom-ingredients, recipe-image-limit y modificaciones de custom/imágenes en Recipes. Preservados; sin commits ni sustitución del submódulo.

## Cambios implementados

Agentes especializados como herramientas nativas, fases obligatorias, alérgenos canónicos, alternativas/omisiones, verificación efímera, historial estructurado, OpenAPI y compatibilidad histórica.

## Validación

Entorno local macOS/JDK 23/Gradle 9.3.0 y Docker Rancher Desktop. Firestore usa proyecto demo exclusivamente, con emulador desechable en 127.0.0.1:18080; retirado al terminar. El smoke usa su propio stack desechable y lo limpia.

| Comprobación desde recipe-services | Resultado |
| --- | --- |
| `./gradlew test -Penv=local` | PASS: 255 pruebas, 0 fallos/errores/omitidas |
| `./gradlew build -Penv=local` | PASS |
| `./gradlew build -Penv=cloud` | PASS; tareas up-to-date tras build local, sin validación de infraestructura cloud |
| `FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local` | PASS: 9 pruebas, 0 fallos/errores/omitidas |
| `./gradlew installDist distTar -Penv=local` | PASS |
| `docker build -t recipes-service:local .` | PASS: imagen final 216dbff4177170db68a2a4a1ec9db13bfef6ca716cc39e3cf825e48262d434ac |
| `python3 scripts/isolated-smoke.py` | PASS: 120 checks sobre la imagen final; stack aislado retirado automáticamente |
| Schemas JSON estrictos, referencias OpenAPI/YAML, enlaces documentales y diff whitespace | PASS |

Pruebas de agentes: Koog real, agentes como herramientas nativas, transporte Responses y serialización reales; solo el proveedor HTTP se simula. Cubren ingredientes ordinarios/custom/añadidos, sin restricciones, varios bebés, sustitución, omisión, conflictos en alternativas, fallo del proveedor sin reintentos, identificación/cobertura incompletas, verificación final negativa, cambios de título y alérgenos declarados con etiquetas propias.

Integración: SDK Firestore real contra emulador, trazas estructuradas persistidas, snapshots originales, reemplazos finales y GET de detalle/lista sin escrituras. La verificación efímera no se serializa. El smoke añade HTTP público mediante Traefik, estados, aislamiento de cuentas e imágenes opcionales, con Auth/AI Configuration/Babies/OpenAI/GCS simulados.

Durante la implementación, el generador de esquemas de herramientas Koog rechazó JsonObject anidado: se sustituyó por DTO culinario tipado. El harness de nuevas pruebas espera explícitamente el estado terminal, porque Koog utiliza su propio dispatcher; las comprobaciones finales pasan.

## Límites de esta entrega

- No se hicieron llamadas a un LLM real: las pruebas demuestran orquestación, validación y contratos; no verifican la calidad del razonamiento del proveedor sobre alimentos reales.
- No se escribieron datos en Firebase real ni se modificó el histórico.
- Imagen y distribución generadas; Recipes redesplegado en Rancher Desktop el 2026-10-14 a las 15:30 Europe/Madrid.
- Sin commits nuevos: revisiones finales iguales a las iniciales, con cambios de trabajo en Recipes y docs. No se alteran remotos ni gitlinks.

## Redespliegue local — 2026-10-14

Autorizado explícitamente por el usuario. `docker --context rancher-desktop compose -p bebilo-recipes up -d --no-build --no-deps recipes-service` recreó únicamente Recipes con la imagen final verificada `sha256:216dbff4177170db68a2a4a1ec9db13bfef6ca716cc39e3cf825e48262d434ac`.

Contenedor running, cero reinicios. Health directo OK. `python3 scripts/local-smoke.py`: PASS, 37 comprobaciones de despliegue sin cuentas válidas, generación LLM ni datos de prueba en Firebase. OpenAPI servido idéntico al fuente directo y por gateway; Swagger Recipes y central accesibles. El primer smoke se ejecutó durante el arranque y se repitió satisfactoriamente tras esperar health.

SHA-256 del JAR local y desplegado idéntico: `5d7f8b3137e72882d7c6b6ed7761a7b4fc5a18dcbb380bdc2f3c1deb44e03c31`. El razonamiento LLM real y el recorrido móvil autenticado siguen sin validación en esta entrega.
