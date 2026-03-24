# kotlin-native-runtime-dwarf skill

Portable Cursor skill for enabling and validating Kotlin/Native runtime DWARF debug info (including KT-75806 patch handling) with OHOS-focused commands.

## Included files

- `SKILL.md` — concise entrypoint and trigger guidance
- `patch-info.md` — KT-75806 context and patch application instructions
- `IrToBitcode-KT-75806-fix-only.patch` — self-contained patch payload
- `compiler-workflow.md` — compiler/runtime rebuild and verification flow
- `application-wiring.md` — optional app integration steps
- `troubleshooting.md` — failure modes and diagnostics

## Notes

- This folder is self-contained so it can be hosted in a separate repository.
- Commands use placeholders `/kotlin/repo` and `/application/repo`.
