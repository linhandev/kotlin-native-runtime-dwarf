# Compiler Workflow

## 1) Enable runtime debug emission

Add to `/kotlin/repo/local.properties`:

```properties
kotlin.native.isNativeRuntimeDebugInfoEnabled=true
```

Reference: `kotlin-native/HACKING.md` (runtime CLion debugging section).

## 2) Clean rebuild for target

From `/kotlin/repo`:

```bash
./gradlew --stop
./gradlew clean
./gradlew :kotlin-native:ohos_arm64CrossDist :kotlin-native:ohos_arm64PlatformLibs
```

## 3) Smoke test with konanc

Create a simple `test.kt` then run:

```bash
kotlin-native/dist/bin/konanc   -target ohos_arm64   -g   -Xbinary=stripDebugInfoFromNativeLibs=false   -o test_ohos   test.kt
```

## 4) Verify DWARF contains runtime C++

Use LLVM dwarfdump (preferred):

```bash
~/.konan/dependencies/llvm-*/bin/llvm-dwarfdump --debug-info test_ohos.kexe | grep -F Exceptions.cpp
```

Pass condition: multiple lines for `Exceptions.cpp` (or other runtime `.cpp`).

## 5) Full dist and re-test

```bash
./gradlew :kotlin-native:crossDist :kotlin-native:crossDistPlatformLibs
```

Then repeat the konanc + dwarfdump verification.
