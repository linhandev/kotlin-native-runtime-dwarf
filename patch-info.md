# KT-75806 Patch Details

Issue: LLVM may drop debug info for the whole module when Kotlin/Native emits incomplete function debug scopes for IR functions with `UNDEFINED_OFFSET`.

## Upstream patch identity

- Subject: `[K/N] Drop debug info from functions with startOffset = UNDEFINED_OFFSET`
- YouTrack: [KT-75806](https://youtrack.jetbrains.com/issue/KT-75806)

## Expected code shape (fixed)

In `kotlin-native/backend.native/compiler/ir/backend.native/src/org/jetbrains/kotlin/backend/konan/llvm/IrToBitcode.kt`:

- `FunctionScope.scope` uses:

```kotlin
if (context.shouldContainLocationDebugInfo()) {
    declaration?.scope()
} else {
    null
}
```

- No `llvmFunction.scope(0, ...)` fallback.
- No private helper:
  `private fun LlvmCallable.scope(startLine: Int, subroutineType: DISubroutineTypeRef, nodebug: Boolean)`

## Local patch application

This skill ships the patch file in the same directory as this document:

- `IrToBitcode-KT-75806-fix-only.patch`

From `/kotlin/repo`, apply it with either:

```bash
git apply /path/to/skill/IrToBitcode-KT-75806-fix-only.patch
```

or after copying/symlinking the file into `/kotlin/repo`:

```bash
git apply IrToBitcode-KT-75806-fix-only.patch
```

If `git apply` fails due to context drift, apply the same change manually and verify with:

```bash
./gradlew :kotlin-native:backend.native:compileKotlin
```
