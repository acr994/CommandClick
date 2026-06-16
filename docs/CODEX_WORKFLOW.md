# Workflow recomendado para Codex

Este documento define comandos seguros para trabajar en este repositorio Android sin cambiar comportamiento de la app.

## Preparación del entorno

1. Confirmar JDK 17:

   ```bash
   java -version
   ```

2. Confirmar Android SDK mediante una de estas opciones:

   ```bash
   echo "$ANDROID_HOME"
   echo "$ANDROID_SDK_ROOT"
   ```

   o crear localmente, sin commitear, `local.properties`:

   ```properties
   sdk.dir=/ruta/al/android/sdk
   ```

3. Confirmar que el wrapper responde:

   ```bash
   ./gradlew --version
   ```

## Inspección rápida

```bash
git status --short
find . -maxdepth 2 -not -path './.git*' -not -path './.gradle*' -not -path '*/build*' -print | sort
rg --files -g '*.gradle' -g '*.gradle.kts' -g 'gradle-wrapper.properties' -g 'gradle.properties' -g 'settings.gradle.kts'
./gradlew tasks --all --console=plain
```

No usar `ls -R` ni `grep -R`; preferir `rg` y `find` con profundidad limitada.

## Build

Compilación recomendada para cambios generales:

```bash
./gradlew :app:assembleDebug
```

Build completo del módulo cuando el cambio afecta código, recursos, Gradle o manifiesto:

```bash
./gradlew :app:build
```

Release solo si la tarea lo requiere:

```bash
./gradlew :app:assembleRelease
```

## Pruebas y verificación

Pruebas unitarias debug:

```bash
./gradlew :app:testDebugUnitTest
```

Lint debug:

```bash
./gradlew :app:lintDebug
```

Pruebas instrumentadas, solo con dispositivo/emulador disponible:

```bash
./gradlew :app:connectedDebugAndroidTest
```

Verificación de cambios esperados:

```bash
git diff --check
git diff --stat
git status --short
```

## Limpieza

Limpiar outputs de Gradle:

```bash
./gradlew clean
```

No commitear:

- `.gradle/`
- `build/`
- `app/build/`
- `local.properties`
- APK/AAB generados
- rutas absolutas locales

## Mantenimiento seguro

- Para cambios solo de documentación, basta validar `git diff --check` y revisar `git diff --stat`.
- Para cambios Kotlin/XML, ejecutar al menos `./gradlew :app:assembleDebug`.
- Para cambios de tests, ejecutar la tarea de test correspondiente.
- Para cambios en manifiesto, permisos, servicios, storage, intents, foreground services o dependencias, ejecutar `./gradlew :app:build` y documentar riesgos.
- No actualizar Gradle, AGP, Kotlin, SDK, targetSdk ni dependencias sin aprobación explícita.

## Checklist antes de entregar

```bash
git status --short
git diff --check
```

Confirmar:

- Solo están modificados los archivos esperados.
- No hay secretos ni archivos locales.
- No hay cambios funcionales no solicitados.
- La respuesta final enumera archivos cambiados, comandos ejecutados con resultado real, riesgos y checks manuales si aplica.
