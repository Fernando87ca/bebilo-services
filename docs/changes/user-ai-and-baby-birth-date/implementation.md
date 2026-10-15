# Implementación

Estado: completado. [Spec](spec.md) · [Plan](plan.md) · [Tareas](tasks.md).

## Revisiones iniciales

### bebilo-services: 5a323a54985ac61c4b1c12896d184cab06c8d1f7

```text
 M docs/README.md
 M docs/changes/README.md
 m recipe-services
?? docs/changes/recipe-instruction-formats/
?? docs/changes/user-ai-and-baby-birth-date/
```

### aiconfig-services: 2445451cae8b9c8c86b705e157b916d54c0cc84e

```text
Limpio
```

### baby-services: 6fc97911e6eb813396f3fb19e8551ed99ad06cf8

```text
Limpio
```

### recipe-services: efd1bf14acb821fbaa9d87450376a844f939075a

```text
 M AGENTS.md
 M scripts/isolated-smoke.py
 M src/main/kotlin/com/elceller/bebilo/recipes/api/response/RecipeDto.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/data/generation/KoogRecipeGenerator.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/data/generation/PromptResources.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/data/mapper/FirestoreRecipeMapper.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/data/mapper/GenerationMappers.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/data/mapper/RecipeResponseMapper.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/domain/model/Recipe.kt
 M src/main/kotlin/com/elceller/bebilo/recipes/domain/model/RecipeValidation.kt
 M src/main/resources/openapi.yaml
 M src/main/resources/prompts/composition.txt
 M src/main/resources/prompts/recipe-steps.schema.json
 M src/test/kotlin/com/elceller/bebilo/recipes/FirestoreRecipeMapperTest.kt
 M src/test/kotlin/com/elceller/bebilo/recipes/Fixtures.kt
 M src/test/kotlin/com/elceller/bebilo/recipes/KoogRecipeGeneratorTest.kt
 M src/test/kotlin/com/elceller/bebilo/recipes/RecipeGenerationTest.kt
 M src/test/kotlin/com/elceller/bebilo/recipes/RecipeOptionalImagesTest.kt
 M src/test/kotlin/com/elceller/bebilo/recipes/RecipeSafetyAgentsTest.kt
?? src/main/resources/prompts/composition-narrative.txt
?? src/main/resources/prompts/composition-steps.txt
?? src/test/kotlin/com/elceller/bebilo/recipes/RecipeInstructionFormatsTest.kt
```

### bebilo: 6028483ac9e6d1f5d2c4ebe246d454ecfe02b1f7

```text
 M BebiloIOS/BebiloIOS/Localizable.xcstrings
 M BebiloIOS/BebiloIOS/router/ProfileAddBabyRouter.swift
 M BebiloIOS/BebiloIOS/router/ProfileRouter.swift
 M BebiloIOS/BebiloIOS/view/profile/ProfilePage.swift
 M BebiloIOS/BebiloIOS/view/profile/components/ProfileBabySelectorSheet.swift
 M BebiloIOS/BebiloIOS/view/welcomeflow/BabyDataPage.swift
 M BebiloIOS/BebiloIOS/viewmodel/profileflow/ProfileAddBabyIosViewModel.swift
 M BebiloIOS/BebiloIOS/viewmodel/profileflow/ProfileIosViewModels.swift
 M BebiloIOS/BebiloIOS/viewmodel/welcomeflow/WelcomeIosViewModels.swift
 M docs/changes/README.md
 M docs/changes/app-recipes-implementation/backend-requirements.md
 M docs/changes/app-recipes-implementation/implementation.md
 M docs/changes/app-recipes-implementation/plan.md
 M docs/changes/app-recipes-implementation/spec.md
 M docs/changes/app-recipes-implementation/tasks.md
 M features/data/babies/src/commonMain/kotlin/com/elceller/bebilo/data/babies/model/BabyRequest.kt
 M features/data/babies/src/commonMain/kotlin/com/elceller/bebilo/data/babies/model/BabyResponse.kt
 M features/data/babies/src/commonTest/kotlin/com/elceller/bebilo/data/babies/api/BabiesApiContractTest.kt
 M features/data/babies/src/commonTest/kotlin/com/elceller/bebilo/data/babies/api/BabyApiMapperTest.kt
 M features/data/babies/src/commonTest/kotlin/com/elceller/bebilo/data/babies/repository/BabiesLocalRepositoryImplTest.kt
 M features/data/babies/src/commonTest/kotlin/com/elceller/bebilo/data/babies/repository/SessionBabySelectionTest.kt
 M features/data/recipes/src/commonMain/kotlin/com/elceller/bebilo/data/recipes/mapper/RecipeMapper.kt
 M features/data/recipes/src/commonMain/kotlin/com/elceller/bebilo/data/recipes/model/RecipeDto.kt
 M features/data/recipes/src/commonTest/kotlin/com/elceller/bebilo/data/recipes/RecipesApiTest.kt
 M features/domain/babies/src/commonMain/kotlin/com/elceller/bebilo/domain/babies/model/Baby.kt
 M features/domain/babies/src/commonMain/kotlin/com/elceller/bebilo/domain/babies/usecase/GetBabiesUseCase.kt
 M features/domain/recipes/src/androidHostTest/kotlin/com/elceller/bebilo/domain/recipes/RecipeStatusObservationTest.kt
 M features/domain/recipes/src/commonMain/kotlin/com/elceller/bebilo/domain/recipes/model/Recipe.kt
 M features/mvi/profile/src/androidDeviceTest/kotlin/com/elceller/bebilo/features/mvi/profile/AboutMePageTest.kt
 M features/mvi/profile/src/androidDeviceTest/kotlin/com/elceller/bebilo/features/mvi/profile/ProfileBabySelectorPageTest.kt
 M features/mvi/profile/src/androidHostTest/kotlin/com/elceller/bebilo/features/mvi/profile/ProfileBabySelectorViewModelTest.kt
 M features/mvi/profile/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/profile/composables/components/ProfileActiveRulesCard.kt
 M features/mvi/profile/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/profile/composables/components/ProfileBabySelectorSheet.kt
 M features/mvi/profile/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/profile/composables/pages/ProfilePage.kt
 M features/mvi/profile/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/profile/mapper/BabyPresentationMapper.kt
 M features/mvi/profile/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/profile/model/ProfileBabyItem.kt
 M features/mvi/recipes/src/androidDeviceTest/kotlin/com/elceller/bebilo/features/mvi/recipes/RecipesAccessibilityTest.kt
 M features/mvi/recipes/src/androidDeviceTest/kotlin/com/elceller/bebilo/features/mvi/recipes/RecipesRouterTestHost.kt
 M features/mvi/recipes/src/androidHostTest/kotlin/com/elceller/bebilo/features/mvi/recipes/RecipeScreenViewModelsTest.kt
 M features/mvi/recipes/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/recipes/composables/pages/RecipeDetailPage.kt
 M features/mvi/recipes/src/androidMain/res/values/strings.xml
 M features/mvi/recipes/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/recipes/mapper/RecipePresentationMapper.kt
 M features/mvi/recipes/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/recipes/model/RecipeUi.kt
 M features/mvi/recipes/src/commonTest/kotlin/com/elceller/bebilo/features/mvi/recipes/RecipePresentationMapperTest.kt
 M features/mvi/welcome/src/androidHostTest/kotlin/com/elceller/bebilo/features/mvi/welcome/AddBabyFlowViewModelTest.kt
 M features/mvi/welcome/src/androidHostTest/kotlin/com/elceller/bebilo/features/mvi/welcome/GeneralErrorRecoveryTest.kt
 M features/mvi/welcome/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/welcome/composables/navigator/AddBabyRouter.kt
 M features/mvi/welcome/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/welcome/composables/pages/BabyDataPage.kt
 M features/mvi/welcome/src/androidMain/kotlin/com/elceller/bebilo/features/mvi/welcome/composables/pages/BabyListPage.kt
 M features/mvi/welcome/src/androidMain/res/values/strings.xml
 M features/mvi/welcome/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/welcome/mapper/BabyMapper.kt
 M features/mvi/welcome/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/welcome/model/BabyInfoData.kt
 M features/mvi/welcome/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/welcome/model/BabyItem.kt
 M features/mvi/welcome/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/welcome/viewmodel/AddBabyFlowViewModel.kt
 M features/mvi/welcome/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/welcome/viewmodel/BabyDataViewModel.kt
 M features/mvi/welcome/src/commonMain/kotlin/com/elceller/bebilo/features/mvi/welcome/viewmodel/WelcomeMainViewModel.kt
 M multiplatform-components/src/androidMain/kotlin/com/elceller/bebilo/multiplatform_components/composables/BebiloRecipeInstruction.kt
 M shared/src/iosMain/kotlin/com/elceller/bebilo/shared/MainIosFactoryDI.kt
 M shared/src/iosMain/kotlin/com/elceller/bebilo/shared/ProfileIosFactoryDI.kt
?? BebiloIOS/BebiloIOS/view/welcomeflow/components/BabyBirthDateField.swift
?? docs/changes/baby-birth-date/
?? docs/design/baby-birth-date/
?? features/domain/babies/src/androidHostTest/
?? features/domain/babies/src/commonMain/kotlin/com/elceller/bebilo/domain/babies/model/BabyBirthDate.kt
?? features/domain/babies/src/commonMain/kotlin/com/elceller/bebilo/domain/babies/model/CurrentDate.kt
?? features/domain/babies/src/commonTest/
?? features/mvi/profile/src/androidDeviceTest/kotlin/com/elceller/bebilo/features/mvi/profile/BabyBirthDatePageTest.kt
?? features/mvi/welcome/src/commonTest/
?? multiplatform-components/src/androidMain/kotlin/com/elceller/bebilo/multiplatform_components/composables/BebiloBirthDateField.kt
```

## Cambios realizados

- T-01 / AC-01: FirestoreAiConfigurationRepository lee/reemplaza users/{uid}/aiConfigurations/default. API/defaults conservados; prueba SDK real verifica aislamiento, reemplazo completo y perfil intacto.
- T-02 / AC-02: Baby/NewBaby/DTOs/mappers/OpenAPI/template usan birthDate string ISO. Validación dominio rechaza ausencia/formato/fecha imposible/futura según Europe/Madrid. Documento corrupto produce BabyStorageException (503). Pruebas HTTP de alta/edición rechazan payload antiguo sin nacimiento; SDK real verifica idempotencia, edición, selección y roundtrip sin ageMonths.
- T-03 / AC-03: CreationBaby solo representa nacimiento de dependencia. CreateRecipeUseCase calcula meses completos con Clock inyectable y una fecha de referencia por solicitud. GenerationBaby y generationContext conservan nacimiento y edad derivada; referenceDate queda persistida. Agentes consumen ageMonths calculado; GET sin cambios. Pruebas verifican límites calendario, referencia Madrid en medianoche, recalculado entre solicitudes, snapshot fijo y rechazo previo a persistencia.
- T-04 / AC-04: Cliente inspeccionado ya usa birthDate y babyIds. IA se consume por HTTP y no conoce ubicación Firestore. Sin cambios de código móvil necesarios; enlazado plan y decisión Madrid desde documentación baby-birth-date. Pruebas móviles dirigidas pasan.

## Validación ejecutada

Entorno macOS, JDK 23, Docker contexto rancher-desktop. Gradle 9.6.0 en Babies/IA y 9.3.0 en Recipes. Firestore demo-bebilo-recipes en emulador desechable 127.0.0.1:18080; smoke usa su propio emulador y dependencias/proveedores simulados. Sin fixtures ni llamadas LLM en Firebase real.

| Comando desde cada servicio | Resultado |
| --- | --- |
| ./gradlew test build installDist distTar -Penv=local | PASS en IA, Babies y Recipes |
| ./gradlew build -Penv=cloud | PASS en los tres; no valida infraestructura cloud |
| FIRESTORE_EMULATOR_HOST=127.0.0.1:18080 ./gradlew integrationTest -Penv=local | PASS en los tres, SDK real y Firebase emulado |
| docker --context rancher-desktop build -t <servicio>:local . | PASS en los tres |
| DOCKER_CONTEXT=rancher-desktop python3 scripts/isolated-smoke.py desde Recipes | PASS: 120 checks |
| python3 scripts/local-smoke.py desde Recipes tras despliegue | PASS: 37 checks de solo lectura |
| Tests host móviles domain:babies, data:babies, data:ai-configuration | PASS: 21; Babies up-to-date, IA ejecutado |
| Enlaces del cambio y git diff --check | PASS |

- aiconfig-services test: 9 pruebas, 0 fallos, 0 errores, 0 omitidas.
- aiconfig-services integrationTest: 1 pruebas, 0 fallos, 0 errores, 0 omitidas.
- baby-services test: 42 pruebas, 0 fallos, 0 errores, 0 omitidas.
- baby-services integrationTest: 1 pruebas, 0 fallos, 0 errores, 0 omitidas.
- recipe-services test: 273 pruebas, 0 fallos, 0 errores, 0 omitidas.
- recipe-services integrationTest: 9 pruebas, 0 fallos, 0 errores, 0 omitidas.

Fallos intermedios: fixtures antiguos de Babies sin nacimiento y dos tipos/argumentos de tests quedaron desalineados durante la sustitución; corregidos antes de validar builds finales. La primera preparación documental falló por sintaxis Python y no escribió archivos; ejecución corregida. Resultados arriba corresponden al código final.

## Despliegue Rancher Desktop

Autorizado por petición del usuario. Desplegados con `docker --context rancher-desktop compose -p <proyecto> up -d --no-build --no-deps <servicio>` desde cada servicio: bebilo-aiconfig/aiconfig-service, bebilo-baby/baby-service, bebilo-recipes/recipes-service.

Verificación final: 2026-10-14T18:55:37.294858+02:00 Europe/Madrid. Los tres running, cero reinicios, imagen construida idéntica a desplegada y JAR SHA-256 idéntico a local. Health directo y por gateway 200; OpenAPI directo/gateway idéntico byte a byte al fuente; Swagger central 200.

| Servicio | Imagen | JAR SHA-256 |
| --- | --- | --- |
| aiconfig-service | sha256:4eeb86e041542ad879cf458f39b10b884a0970f2f199ade6ae38f1b2b030b197 | 97f59ed97e9f281a6d89dd6deb0cda049a035f36d26cde6eb7c651eaf19eaae5 |
| baby-service | sha256:76a3484c47fff82821ff0c26a9808caea3ea27d2a7bca2004515053444ec5315 | 349a4d87b396fe8f72a21e3aa746d49b9f182ce85444079696840061de66406b |
| recipes-service | sha256:dfda86ccaf2c0fb395401324e81480ebf79c3b570e8fb67472f8d4a914be1b6f | a2c7f5b10a63c96abbe2fadd89886689727c0bb3d26f6e929998c8949881443d |

## Revisiones finales y límites

Cambios permanecen sin commit; SHAs base arriba no cambiaron y gitlinks no se avanzan sin commits disponibles. Se conservan submódulos y trabajo previo de Recipes/app/agregador; no se crean remotos.

- AC-01–AC-03 validados con unitarias/HTTP con dobles y SDK Firestore Emulator. AC-04 mediante contratos y tests cliente, sin recorrido móvil autenticado contra Firebase real.
- AC-05 validado en Rancher Desktop local; no despliegue cloud.
- No llamadas LLM reales, cambios de datos reales, migración, compatibilidad ni recalculado de recetas históricas.
- La app mantiene los pendientes previos de recorrido interactivo iOS y análisis estático global; no se amplía la UI en esta entrega.
- /Users/foca/IdeaProjects/bebilo-services: 5a323a54985ac61c4b1c12896d184cab06c8d1f7 + cambios locales documentados.
- /Users/foca/IdeaProjects/bebilo-services/aiconfig-services: 2445451cae8b9c8c86b705e157b916d54c0cc84e + cambios locales documentados.
- /Users/foca/IdeaProjects/bebilo-services/baby-services: 6fc97911e6eb813396f3fb19e8551ed99ad06cf8 + cambios locales documentados.
- /Users/foca/IdeaProjects/bebilo-services/recipe-services: efd1bf14acb821fbaa9d87450376a844f939075a + cambios locales documentados.
- /Users/foca/AndroidStudioProjects/bebilo: 6028483ac9e6d1f5d2c4ebe246d454ecfe02b1f7 + cambios locales documentados.
