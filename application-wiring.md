# Application Wiring Workflow

Run this only if the user asks to enable runtime DWARF in an application too.

## 1) Add linker-preserving debug flag

In `/application/repo`, add for OHOS target:

`-Xbinary=stripDebugInfoFromNativeLibs=false`

Use the project's existing Gradle DSL (`compilerOptions`, binary options, or free compiler args).

## 2) Point app to local compiler dist

In `/application/repo/gradle.properties`:

```properties
kotlin.native.home=/kotlin/repo/kotlin-native/dist
```

Use an absolute path.

## 3) Rebuild app

Use the README command; if unclear, ask the user.

## 4) Validate app artifact

```bash
llvm-dwarfdump --debug-info <app-binary> | grep -F Exceptions.cpp
```

Pass condition: runtime C++ entries are present.
