# Informe técnico del repositorio CommandClick

Fecha de inspección: 2026-06-16.

## Resumen

CommandClick es un proyecto Android de un solo módulo Gradle (`:app`) escrito principalmente en Kotlin. La app empaqueta una cantidad significativa de recursos Android, assets web/scripts y bibliotecas nativas por ABI. Esta inspección no modifica funcionalidad.

## Estructura observada

- `settings.gradle.kts`: define `rootProject.name = "CommandClick"` e incluye `:app`.
- `build.gradle.kts`: declara plugins Android/Kotlin y configura compilación Kotlin con JVM 17.
- `gradle/wrapper/gradle-wrapper.properties`: usa Gradle 8.13.
- `gradle.properties`: AndroidX habilitado, Jetifier habilitado, estilo Kotlin oficial y `android.nonTransitiveRClass=true`.
- `app/build.gradle.kts`: configuración Android, variantes, dependencias y opciones de compilación.
- `app/src/main/AndroidManifest.xml`: permisos, `MainActivity`, `FileProvider` y servicios de la app.
- `app/src/main/java/com/puutaro/commandclick/`: código principal Kotlin/Java.
- `app/src/main/res/`: recursos Android.
- `app/src/main/assets/`: HTML/CSS/JS, scripts y recursos empaquetados.
- `app/src/main/jniLibs/`: bibliotecas nativas para `armeabi-v7a`, `arm64-v8a`, `x86` y `x86_64`.
- `app/src/test/` y `app/src/androidTest/`: pruebas unitarias e instrumentadas.
- `fastlane/`, `md/`, `statistics/`, `typescript/`: material auxiliar no necesario para compilar el módulo Android básico.

## Stack real identificado

| Área | Valor |
| --- | --- |
| Build system | Gradle Wrapper |
| Gradle | 8.13 |
| Android Gradle Plugin | 8.12.3 |
| Kotlin Android plugin | 2.2.20 |
| Lenguaje principal | Kotlin |
| Java/JDK esperado | Java 17 |
| JVM target Kotlin/Java | 17 |
| Módulos Gradle | `:app` |
| `namespace` | `com.puutaro.commandclick` |
| `applicationId` | `com.puutaro.commandclick` |
| `compileSdk` | 36 |
| `minSdk` | 27 |
| `targetSdk` | 33 |
| `versionCode` | 86 |
| `versionName` | 1.4.5 |
| Data Binding | habilitado |
| BuildConfig | habilitado |
| Minify release | deshabilitado (`isMinifyEnabled = false`) |

## Comandos de build y verificación

- `./gradlew :app:assembleDebug`: compila APK debug.
- `./gradlew :app:assembleRelease`: compila APK release según la configuración local.
- `./gradlew :app:build`: ensambla y ejecuta verificaciones del módulo.
- `./gradlew :app:testDebugUnitTest`: ejecuta pruebas unitarias debug.
- `./gradlew :app:lintDebug`: ejecuta lint de la variante debug.
- `./gradlew clean`: limpia outputs de Gradle.
- `./gradlew tasks --all --console=plain`: lista tareas disponibles.

## Android SDK y entorno

- En esta inspección no existía `local.properties` en la raíz.
- El entorno debe proporcionar `ANDROID_HOME` o `ANDROID_SDK_ROOT`, o un `local.properties` local con `sdk.dir=/ruta/al/sdk`.
- `local.properties` no debe versionarse.
- La inspección local encontró Java 17 disponible (`openjdk version "17.0.2"`).

## Código y recursos

Conteo aproximado observado bajo `app/src`:

- Kotlin: 754 archivos.
- Java: 1 archivo.
- XML: 229 archivos.

Paquetes principales bajo `com.puutaro.commandclick`:

- `activity`, `activity_lib`: actividad principal y lógica asociada.
- `fragment`, `fragment_lib`: pantallas y lógica de fragmentos.
- `service`: servicios Android como descargas, TTS, música, Ubuntu y Git.
- `proccess`: lógica de procesos, terminal, importación, QR, shell macro y flujos relacionados.
- `util`: utilidades de archivos, shell, URLs, strings, diálogos, imágenes y estado.
- `view_model`: modelos de vista.
- `component`, `custom_view`, `custom_manager`: UI y componentes auxiliares.

## Permisos y superficies sensibles

El manifiesto declara permisos y capacidades sensibles, entre ellos:

- Red/Wi-Fi: `INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE`, `CHANGE_WIFI_STATE`.
- Ubicación: `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`.
- Almacenamiento: `READ_EXTERNAL_STORAGE`, `WRITE_EXTERNAL_STORAGE`, `MANAGE_EXTERNAL_STORAGE`, `requestLegacyExternalStorage=true`.
- Cámara: `CAMERA` y feature de cámara opcional.
- Notificaciones/servicios: `POST_NOTIFICATIONS`, `FOREGROUND_SERVICE`.
- `usesCleartextTraffic=true`.
- Activity exportada con intent-filters para launcher, browsable, http/https y búsqueda web.
- `FileProvider` con autoridad `com.puutaro.commandclick.provider`.

Estos puntos deben tratarse como de alto riesgo: cambios pequeños pueden afectar compatibilidad, privacidad, Play policy o comportamiento en segundo plano.

## Riesgos y deuda técnica observados

- `targetSdk = 33` mientras `compileSdk = 36`; futuras políticas de distribución pueden exigir target SDK más reciente.
- Dependencia de `MANAGE_EXTERNAL_STORAGE` y almacenamiento legacy, potencialmente sensible para distribución y privacidad.
- `usesCleartextTraffic=true` permite tráfico HTTP no cifrado.
- `android.enableJetifier=true` indica compatibilidad con dependencias antiguas; aumenta coste de build y puede ocultar deuda.
- `androidx.lifecycle:lifecycle-extensions:2.2.0` está obsoleto en ecosistema Android moderno.
- Hay repositorios externos como JitPack y snapshots Sonatype; pueden afectar reproducibilidad.
- Existe material sensible o release-adjacent (`key/`, jniLibs, fastlane) que no debe tocarse sin permiso explícito.
- El paquete contiene muchos assets/scripts; conviene validar manualmente flujos afectados si se modifican.

## Recomendación para Codex

Para tareas futuras, empezar por el archivo afectado y el flujo mínimo de Gradle. Evitar actualizaciones de versiones, cambios de manifiesto, dependencias, permisos, assets empaquetados o bibliotecas nativas salvo que la tarea lo pida de forma explícita.
