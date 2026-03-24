# Troubleshooting

## Symptom: binary says debug_info but runtime symbols missing

Likely cause: KT-75806 fix not applied.

Checks:

- `llvmFunction.scope(0, ...)` still exists in `IrToBitcode.kt`.
- `LlvmCallable.scope(...)` helper still exists.

Fix:

- Apply patch from [patch-info.md](patch-info.md), rebuild compiler + target dist.

## Symptom: only a few compile units (for example ~4)

Likely cause: module-level DWARF got dropped.

Checks:

```bash
llvm-dwarfdump --debug-info app.kexe | grep -c 'DW_TAG_compile_unit'
```

Fix:

- Re-check patch + clean rebuild + correct compiler dist usage.

## Symptom: no `Exceptions.cpp` in output

Checks:

- `kotlin.native.isNativeRuntimeDebugInfoEnabled=true` is in `/kotlin/repo/local.properties`.
- App/compiler includes `-Xbinary=stripDebugInfoFromNativeLibs=false`.
- You are using the intended compiler (`kotlin.native.home`).
- You run `llvm-dwarfdump` from Konan/LLVM toolchain, not a mismatched system tool.

Extra checks:

- App was rebuilt after wiring changes (no stale artifact).
- You are probing the artifact actually packaged/run by the app.
- `IrToBitcode.kt` really has the KT-75806 shape (no fallback/helper).

## Symptom: `invalid range list offset ...` from llvm-dwarfdump

Interpretation:

- This can be toolchain/version mismatch noise and does not always mean runtime DWARF is unusable.

Decision rule:

1. If `Exceptions.cpp` entries are present and compile unit count is healthy, proceed (warning only).
2. If both probes fail, treat as blocking and re-check compiler/toolchain pairing.

Actions:

- Try a different `llvm-dwarfdump` from `~/.konan/dependencies/llvm-*/bin`.
- Re-run probe with the same toolchain used by the compiler distribution.

## Useful probes

```bash
file <binary>
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info <binary> | rg "Exceptions\\.cpp"
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info <binary> | rg -c "DW_TAG_compile_unit"
rg "kotlin\\.native\\.home=|kotlin\\.native\\.isNativeRuntimeDebugInfoEnabled=true" /application/repo/gradle.properties /kotlin/repo/local.properties
```
