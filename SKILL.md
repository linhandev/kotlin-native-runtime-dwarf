---
name: kotlin-native-runtime-dwarf
description: Enables and validates DWARF debug info for the Kotlin/Native runtime in linked binaries (KT-75806 LLVM strip). Use when debugging K/N runtime in CLion/DevEco Studio, when kotlin.native.isNativeRuntimeDebugInfoEnabled or stripDebugInfoFromNativeLibs is discussed, or when runtime symbols are missing from native executables/frameworks.
---

# Kotlin/Native Runtime DWARF

## Use when

- Runtime C++ symbols (for example `Exceptions.cpp`) are missing from final binaries.
- You need to enable runtime DWARF for `ohos_arm64` or similar K/N targets.
- You need to wire an application to a locally built Kotlin/Native compiler.

## Required inputs

- `/kotlin/repo`: Kotlin repository root (must contain `kotlin-native/`).
- `/application/repo`: optional application repository.

If the current workspace is not clearly `/kotlin/repo`, ask for it first.
Always ask whether app wiring is needed; if yes, ask for `/application/repo`.

## Fast path

1. Verify/apply KT-75806 patch.
2. Set runtime debug flag in `/kotlin/repo/local.properties`.
3. Rebuild OHOS runtime/compiler artifacts.
4. Validate with `konanc` + `llvm-dwarfdump` for `Exceptions.cpp`.
5. Build full `crossDist` and re-validate.
6. If requested, wire `/application/repo` and validate app binary.

## Agent loop guidance

When running this skill, create and maintain a detailed todo list.

- Use one todo per concrete operation (patch check/apply, flag set, rebuild, smoke test, full dist, app wiring, final validation).
- Keep exactly one task `in_progress` at a time.
- Add explicit validation subtasks (CU count probe and `Exceptions.cpp` probe).
- If any step fails, add diagnosis/fix subtasks before resuming the main flow.
- Mark optional branches as completed or skipped with a reason (for example app wiring not requested).

## Details

- Patch details: [patch-info.md](patch-info.md) - Step 1
- Compiler workflow: [compiler-workflow.md](compiler-workflow.md) - Step 2 3 4 5
- Application wiring: [application-wiring.md](application-wiring.md) - Step 6
- Troubleshooting: [troubleshooting.md](troubleshooting.md)

## Files included in this skill

- `IrToBitcode-KT-75806-fix-only.patch` (fix payload)
- `patch-info.md` (how/when to apply patch)
- `compiler-workflow.md` (compiler-side steps)
- `application-wiring.md` (optional app integration)
- `troubleshooting.md` (diagnostics and recovery)
