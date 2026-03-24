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

## Useful probes

```bash
file <binary>
llvm-dwarfdump --debug-info <binary> | grep -F Exceptions.cpp
llvm-dwarfdump --debug-info <binary> | grep -c 'DW_TAG_compile_unit'
```
