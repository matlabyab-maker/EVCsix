# EVCsix — Android Video Editor

- **GitHub repository:** `EVCsix`
- **Application label:** `EVCsix`
- **Project version:** `0.2.196` (`versionCode 396`)
- **Application ID / Kotlin package:** `com.evc.app` (kept unchanged to preserve app identity)
- **Native ABI:** `armeabi-v7a` only (32-bit ARM / ARMv7)

## Project configuration

- Android module: `app`
- Gradle root project: `EVCsix`
- Minimum Android SDK: 24
- Compile / target SDK: 35
- Java and Kotlin JVM target: 17
- Debug shrinking: disabled

## Important: this is a clean repository import

This ZIP intentionally contains **no `.git` directory**, no old EVCfive remote, no old branches, and no old commit history. This avoids carrying the previous repository's Git state into EVCsix. Create the new empty GitHub repository first, then initialize and push this project as a fresh repository.

### First upload using MGit

1. On GitHub, create a new **empty** repository named `EVCsix` under `matlabyab-maker`. Do not add a README, license, or `.gitignore` on GitHub.
2. Extract this ZIP. Open the single extracted folder named `EVCsix` in MGit (the folder containing `settings.gradle.kts`). Do not open its parent folder or a nested copy.
3. In MGit, initialize a new Git repository in this folder and use branch name `main`.
4. Stage **all** project files, including the hidden `.github/workflows/android.yml` file, then create the first commit. Suggested message: `Initial EVCsix import`.
5. Add one remote named `origin` with this exact URL: `https://github.com/matlabyab-maker/EVCsix.git`.
6. Push the local `main` branch to `origin/main`. If MGit asks to set upstream, accept it.
7. Open the GitHub repository's **Actions** tab. The workflow is named `EVCsix Android Build` and is configured to run on pushes to all branches and on manual dispatch.

If MGit does not show the workflow after the push, first check GitHub's **Code** tab: confirm that the latest commit is on the `main` branch and that `.github/workflows/android.yml` is present. Actions cannot start if the workflow file was not included in the pushed commit or if GitHub Actions are disabled for the repository.

## ARMv7 FFmpeg build

The maintained Maven FFmpegKit artifacts used previously do not provide the required 32-bit ARM native library. The workflow therefore builds FFmpegKit from the maintained source tag `v6.0.4-lts-android` on GitHub Actions, using Android NDK `27.2.12479018`. In this tag, the Android build script is located at `android-6.0-lts/android.sh` and emits an archive named `ffmpeg-kit.aar`; the workflow now uses those exact paths, copies the generated archive to the app's local dependency name, and verifies that it contains `jni/armeabi-v7a/` without 64-bit or x86 ABIs.

The FFmpeg command is passed as a Bash argument array. If the native build fails, the workflow prints the native build log to the Actions log. The workflow also checks that the nested build script and expected NDK are present before starting.

## Split behavior retained

- The project retains the Split-original-video implementation from EVCfive 0.2.193.
- Repeated Split requests are guarded while an earlier Split operation is running.
- The status text reports the part being processed.
- Incomplete MediaStore outputs are removed on failure.
- Original streams are copied with `-c copy`; no re-encoding is requested. Stream-copy cut points may move to nearby keyframes.

## Scope and verification

This cleanup renames the project/repository-facing metadata, removes the old Git history and remote configuration, increments the project version, and corrects the FFmpeg workflow command construction. The application ID and existing app source/features are preserved. A successful GitHub Actions build and on-device Split test have **not** yet been confirmed; use the Actions result to verify the native build and APK before installing it.


## FFmpegKit ARMv7 toolchain note
The FFmpegKit LTS script defaults to Android API 16, but modern Android NDK releases no longer provide
the `armv7a-linux-androideabi16-clang` wrapper. This project has `minSdk 24`, so CI explicitly uses
`--api-level=24`, aligns `ANDROID_NDK_HOME`, `ANDROID_NDK_ROOT`, and `NDK_HOME` to NDK 27.2.12479018,
and runs an ARMv7 compiler smoke test before starting the full FFmpegKit build.
