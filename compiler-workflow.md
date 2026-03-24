# Compiler Workflow

## 1) Enable runtime debug emission

Add to `/kotlin/repo/local.properties` (create file if missing):

```properties
kotlin.native.isNativeRuntimeDebugInfoEnabled=true
```

Reference: `kotlin-native/HACKING.md` (runtime CLion debugging section).

Quick verification:

```bash
rg "kotlin.native.isNativeRuntimeDebugInfoEnabled=true" /kotlin/repo/local.properties
```

## 2) Verify KT-75806 fix is present

Check `IrToBitcode.kt` in `/kotlin/repo`:

```bash
rg "llvmFunction\\.scope\\(0,|private fun LlvmCallable\\.scope\\(" /kotlin/repo/kotlin-native/backend.native/compiler/ir/backend.native/src/org/jetbrains/kotlin/backend/konan/llvm/IrToBitcode.kt
```

Pass condition: no matches.

If matches exist, apply patch from `patch-info.md` first.

## 3) Compiler smoke rebuild

```bash
cd /kotlin/repo
./gradlew :kotlin-native:backend.native:compileKotlin
```

## 4) Clean rebuild for target

From `/kotlin/repo`:

```bash
./gradlew --stop
./gradlew clean
./gradlew :kotlin-native:ohos_arm64CrossDist :kotlin-native:ohos_arm64PlatformLibs
```

## 5) Smoke test with konanc

Create a simple `test.kt` then run:

```bash
kotlin-native/dist/bin/konanc   -target ohos_arm64   -g   -Xbinary=stripDebugInfoFromNativeLibs=false   -o test_ohos   test.kt
```

## 6) Verify DWARF contains runtime C++

Use LLVM dwarfdump (preferred):

```bash
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info test_ohos.kexe | rg "Exceptions\\.cpp"
```

Pass condition: multiple lines for `Exceptions.cpp` (or other runtime `.cpp`).

Also check compile unit count:

```bash
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info test_ohos.kexe | rg -c "DW_TAG_compile_unit"
```

Pass condition: count is non-trivial (not just a few units).

Note: `llvm-dwarfdump` can emit `invalid range list offset ...` while still printing valid debug entries. Treat this as a warning unless `Exceptions.cpp` and compile units are both missing.

## 7) Full dist and re-test

```bash
./gradlew :kotlin-native:crossDist :kotlin-native:crossDistPlatformLibs
```

Then repeat the konanc + dwarfdump verification.
