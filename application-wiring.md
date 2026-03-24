# Application Wiring Workflow

Run this only if the user asks to enable runtime DWARF in an application too.

## 1) Add linker-preserving debug flag

In `/application/repo`, add for OHOS target:

`-Xbinary=stripDebugInfoFromNativeLibs=false`

Use the project's existing Gradle DSL (`compilerOptions`, binary options, or free compiler args).

Checklist:

- Prefer placing flag in the actual binary block used for the debug artifact (for example `sharedLib` under `ohosArm64`).
- If many modules produce native binaries, wire the one that is packaged into the app first.
- Confirm the task that generates the artifact (`linkDebug...`, `linkRelease...`, framework task, etc.).

## 2) Point app to local compiler dist

In `/application/repo/gradle.properties`:

```properties
kotlin.native.home=/kotlin/repo/kotlin-native/dist
```

Use an absolute path.

Verify:

```bash
rg "kotlin\\.native\\.home=" /application/repo/gradle.properties
```

## 3) Rebuild app

Use the README command; if unclear, ask the user.

If README is ambiguous, capture and report the exact build command selected.

## 4) Identify actual output binary

Determine the artifact path produced by the chosen task (examples):

- `.../build/bin/ohosArm64/debugShared/<name>.so`
- `.../build/bin/ohosArm64/releaseShared/<name>.so`
- final packaged native lib path in app output

Do not run DWARF probes until the exact artifact path is confirmed.

## 5) Validate app artifact

```bash
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info <app-binary> | rg "Exceptions\\.cpp"
```

Pass condition: runtime C++ entries are present.

Also run:

```bash
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info <app-binary> | rg -c "DW_TAG_compile_unit"
```

Report both probe outcomes in the final response.
