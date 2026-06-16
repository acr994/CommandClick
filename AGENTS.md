# AGENTS.md

Instrucciones para futuros agentes que trabajen en este fork de CommandClick.

## Layout del repositorio
- `settings.gradle.kts`: proyecto Gradle `CommandClick`; incluye solo el módulo `:app`.
- `build.gradle.kts`: versiones de plugins Android/Kotlin y configuración JVM común.
- `app/build.gradle.kts`: configuración Android, dependencias y variantes `debug`/`release`.
- `app/src/main/java/com/puutaro/commandclick/`: código Kotlin principal de la app.
- `app/src/main/res/`: recursos Android.
- `app/src/main/assets/`: assets web, scripts y recursos empaquetados.
- `app/src/main/jniLibs/`: bibliotecas nativas por ABI; no sustituir sin validar en dispositivo.
- `app/src/test/` y `app/src/androidTest/`: pruebas unitarias e instrumentadas.
- `docs/`: documentación técnica para agentes.
- `fastlane/`, `md/`, `statistics/`, `typescript/`: material auxiliar/publicación/documentación; tratar como fuera del flujo Android salvo petición explícita.

## Stack y comandos exactos
- Gradle Wrapper: `./gradlew` con Gradle 8.13.
- Android Gradle Plugin: 8.12.3.
- Kotlin Android plugin: 2.2.20.
- JDK esperado: Java 17.
- Android SDK: `compileSdk = 36`, `minSdk = 27`, `targetSdk = 33`.
- Módulo principal: `:app`.

Comandos recomendados:
- Compilar debug: `./gradlew :app:assembleDebug`
- Compilar release sin firmar/local según configuración: `./gradlew :app:assembleRelease`
- Build completo del módulo: `./gradlew :app:build`
- Pruebas unitarias debug: `./gradlew :app:testDebugUnitTest`
- Lint debug: `./gradlew :app:lintDebug`
- Limpiar: `./gradlew clean`
- Ver tareas: `./gradlew tasks --all --console=plain`

## Variables Android SDK necesarias
- Preferido: `ANDROID_HOME` apuntando al Android SDK.
- Aceptado por herramientas modernas: `ANDROID_SDK_ROOT` apuntando al Android SDK.
- Alternativa local: `local.properties` con `sdk.dir=/ruta/al/sdk`.
- No commitear `local.properties` ni rutas absolutas del SDK.

## Reglas de edición
- Hacer cambios mínimos y quirúrgicos; no refactorizar sin necesidad.
- Reutilizar patrones existentes antes de introducir nuevas abstracciones.
- No cambiar comportamiento, permisos, manifiesto, servicios, assets o dependencias sin petición explícita.
- No añadir dependencias ni actualizar versiones salvo instrucción explícita.
- No envolver imports en `try/catch`.
- Mantener compatibilidad con Java/Kotlin JVM target 17.
- Para cambios Android sensibles, validar permisos, almacenamiento, servicios en foreground/background, intents y compatibilidad por SDK.

## Qué no debe tocar Codex sin permiso
- Versiones de Gradle, Android Gradle Plugin, Kotlin, SDK o dependencias.
- `app/src/main/AndroidManifest.xml`, permisos, providers, services e intent-filters.
- `app/src/main/jniLibs/`, keystores en `key/`, configuración de firma o release.
- Assets empaquetados, scripts de usuario, fastlane, estadísticas y documentación generada.
- Identificadores públicos: `applicationId`, `namespace`, versionCode/versionName.

## Criterio de done
- El cambio cumple exactamente la tarea y no introduce modificaciones no relacionadas.
- Se ejecutaron las verificaciones relevantes o se documentó claramente por qué no pudieron ejecutarse.
- `git status --short` solo muestra archivos esperados.
- No se commitearon secretos, artefactos locales, `local.properties`, `.gradle/` ni outputs de build.
- La respuesta final lista archivos cambiados, validación real y riesgos/limitaciones.

## Convenciones para futuras tareas
- Responder en español salvo código, logs o nombres técnicos.
- Citar rutas y líneas cuando se expliquen cambios.
- Preferir `rg`/`find` sobre búsquedas recursivas lentas; no usar `ls -R` ni `grep -R`.
- En cambios de código, ejecutar la prueba o build más estrecho posible.
- En cambios solo de documentación, no es necesario compilar; validar existencia/diff de archivos.
