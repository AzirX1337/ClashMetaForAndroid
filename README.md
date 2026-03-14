## Clash Meta for Android

A Graphical user interface of [Clash.Meta](https://github.com/MetaCubeX/Clash.Meta) for Android

### Feature

Feature of [Clash.Meta](https://github.com/MetaCubeX/Clash.Meta)

[<img src="https://fdroid.gitlab.io/artwork/badge/get-it-on.png"
     alt="Get it on F-Droid"
     height="80">](https://f-droid.org/packages/com.github.metacubex.clash.meta/)

### Requirement

- Android 5.0+ (minimum)
- Android 7.0+ (recommend)
- `armeabi-v7a` , `arm64-v8a`, `x86` or `x86_64` Architecture

### Build

#### Prerequisites

- **JDK 21** (Temurin recommended)
- **Android SDK** with platform API 35, build-tools 35.0.0
- **Android NDK** 29.0.14206865
- **CMake** 3.22.1 (installed via Android SDK Manager)
- **Go** 1.26+

#### Steps

1. Update submodules

   ```bash
   git submodule update --init --recursive
   ```

2. Apply Go patches (required for Android compatibility)

   ```bash
   cd $(go env GOROOT)
   for p in /path/to/project/.github/patch/*.patch; do patch --verbose -p 1 < "$p"; done
   ```

3. Update CA certificates in the clash core

   ```bash
   cp -f /etc/ssl/certs/ca-certificates.crt core/src/foss/golang/clash/component/ca/ca-certificates.crt
   ```

4. Create `local.properties` in project root with

   ```properties
   sdk.dir=/path/to/android-sdk
   ```

5. (Optional) Custom app package name. Add the following configuration to `local.properties`.

   ```properties
   # config your own applicationId, or it will be 'com.github.metacubex.clash'
   custom.application.id=com.my.compile.clash
   # remove application id suffix, or the application id will be 'com.github.metacubex.clash.alpha'
   remove.suffix=true
   ```

6. (Optional) Create `signing.properties` in project root for release signing

   ```properties
   keystore.path=/path/to/keystore/file
   keystore.password=<key store password>
   key.alias=<key alias>
   key.password=<key password>
   ```

7. Build

   ```bash
   ./gradlew app:assembleAlphaRelease
   ```

   APKs will be generated in `app/build/outputs/apk/alpha/release/` for each architecture (arm64-v8a, armeabi-v7a, x86, x86_64, universal).

### Automation

APP package name is `com.github.metacubex.clash.meta`

- Toggle Clash.Meta service status
  - Send intent to activity `com.github.kr328.clash.ExternalControlActivity` with action `com.github.metacubex.clash.meta.action.TOGGLE_CLASH`
- Start Clash.Meta service
  - Send intent to activity `com.github.kr328.clash.ExternalControlActivity` with action `com.github.metacubex.clash.meta.action.START_CLASH`
- Stop Clash.Meta service
  - Send intent to activity `com.github.kr328.clash.ExternalControlActivity` with action `com.github.metacubex.clash.meta.action.STOP_CLASH`
- Import a profile
  - URL Scheme `clash://install-config?url=<encoded URI>` or `clashmeta://install-config?url=<encoded URI>`

### Contribution and Project Maintenance

#### Meta Kernel

- CMFA uses the kernel from `android-real` branch under `MetaCubeX/Clash.Meta`, which is a merge of the main `Alpha` branch and `android-open`.
  - If you want to contribute to the kernel, make PRs to `Alpha` branch of the Meta kernel repository.
  - If you want to contribute Android-specific patches to the kernel, make PRs to  `android-open` branch of the Meta kernel repository.

#### Maintenance

- When `MetaCubeX/Clash.Meta` kernel is updated to a new version, the `Update Dependencies` actions in this repo will be triggered automatically.
  - It will pull the new version of the meta kernel, update all the golang dependencies, and create a PR without manual intervention.
  - If there is any compile error in PR, you need to fix it before merging. Alternatively, you may merge the PR directly.
- Manually triggering `Build Pre-Release` actions will compile and publish a `PreRelease` version.
- Manually triggering `Build Release` actions will compile, tag and publish a `Release` version.
  - You must fill the blank `Release Tag` with the tag you want to release in the format of `v1.2.3`.
  - `versionName` and `versionCode` in `build.gradle.kts` will be automatically bumped to the tag you filled above.
