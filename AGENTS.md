# Repository Guidelines

## Project Structure & Module Organization
- Primary focus is `android/`, which contains the Gradle library module `ffmpeg-kit-android-lib` and JNI assets under `src/main/libs` once built. Unit tests live in `src/test/java`.
- Top-level scripts such as `android.sh`, `ios.sh`, `linux.sh`, `macos.sh`, and `tvos.sh` orchestrate native builds; this fork mainly uses `android.sh` to regenerate 16KB page-size Android binaries.
- `scripts/` holds shared bash helpers used by the platform scripts. `docs/` stores generated documentation. Platform folders (`apple/`, `linux/`, `flutter/`, `react-native/`) mirror upstream structure but are secondary here.
- Build outputs land in `prebuilt/` when using the shell scripts, and `android/ffmpeg-kit-android-lib/build/outputs/aar/` when publishing via Gradle.

## Build, Test, and Development Commands
- `./android.sh --help` — list build flags. Requires `ANDROID_SDK_ROOT` and `ANDROID_NDK_ROOT` (NDK r23/25 suggested for 16KB). Downloads FFmpeg/external libs and fills `prebuilt/`.
- `./android.sh --lts --enable-fontconfig` (example) — produce LTS binaries with an extra library; adjust `--disable-<abi>` to trim architectures.
- `cd android && ./gradlew assembleRelease` — package the Android AAR using the prebuilt native libs.
- `cd android && ./gradlew clean test` — clean and run unit tests in `ffmpeg-kit-android-lib`.
- Publishing to GitHub Packages reads credentials from `USERNAME` and `TOKEN` env vars (see `android/ffmpeg-kit-android-lib/build.gradle`).

## Coding Style & Naming Conventions
- Java sources use 4-space indentation, UpperCamelCase types, and camelCase members; keep public APIs aligned with upstream FFmpegKit naming and avoid silent breaking changes.
- Native/config scripts follow snake_case and bash-friendly naming; keep options consistent with FFmpeg/FFmpegKit flags. Do not check in generated outputs except when explicitly releasing binaries.
- Versions, SDK levels, and Maven coordinates are centralized in `android/gradle.properties`; bump related fields together.

## Testing Guidelines
- Unit tests sit in `android/ffmpeg-kit-android-lib/src/test/java` and follow the `*Test` naming. Run `./gradlew test` before submitting.
- For codec/command changes, also validate with the external `ffmpeg-kit-test` app and capture the command line plus log excerpts in the PR.
- Rebuild native libs with the intended flags/ABIs before testing Gradle artifacts to ensure Java wrapper and binaries stay in sync.

## Commit & Pull Request Guidelines
- Commit messages: short, imperative, scoped when helpful (e.g., `android: bump ndk`, `docs: clarify build flags`). Current history is terse; prefer clarity going forward.
- PRs should state the target platform (Android by default), build flags used, and resulting artifact paths. Include test commands run and any relevant console logs.
- Link issues or release goals; avoid cosmetic-only changes. For API or packaging tweaks, mention compatibility expectations and update documentation accordingly.

## Security & Configuration Notes
- Keep SDK/NDK paths and package credentials in your shell environment rather than checked-in files. Never commit tokens used for Maven/GitHub publishing.
- Build scripts fetch sources over the network; if working offline, mirror dependencies ahead of time or pin to previously downloaded archives in a local cache.
