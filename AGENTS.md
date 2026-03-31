# Repository Guidelines

## Project Structure & Module Organization

- This is a Unity project (Unity `6000.4.0a5`, see `ProjectSettings/ProjectVersion.txt`).
- Game/project content lives in `Assets/` (scenes, materials, sample content).
- Custom render pipeline work lives in `Packages/`:
  - `Packages/Custom_URP/`: "Vivid RP", a customized fork of URP `17.4.0` (`Runtime/`, `Editor/`, `ShaderLibrary/`, `Shaders/`, `Tests/`, `Samples~/`, `Documentation~/`).
  - `Packages/CoreRP/`: SRP Core `17.4.0` (shared rendering utilities + tests/samples).
  - `Packages/com.deltation.unity-pix/`: PIX tooling integration (Editor-only).
- Unity package boundaries are defined by `*.asmdef` and `package.json` files.

## Build, Test, and Development Commands

- Open locally: use Unity Hub and select the repo root (`D:\URP_Extension`).
- Dependency management: edit `Packages/manifest.json` via Unity Package Manager; avoid hand-editing `packages-lock.json` (often regenerated).
- Run tests (headless example):
  - `"<UnityEditorPath>\\Unity.exe" -batchmode -projectPath . -runTests -testPlatform EditMode -logFile Logs/editmode.log -quit`
- Run all tests:
  - `"<UnityEditorPath>\\Unity.exe" -batchmode -projectPath . -runTests -testPlatform PlayMode -logFile Logs/playmode.log -quit`
- Run specific test class:
  - `"<UnityEditorPath>\\Unity.exe" -batchmode -projectPath . -runTests -testPlatform EditMode -filter "BuddyAllocatorTests" -logFile Logs/test.log -quit`
- Run specific test method:
  - `"<UnityEditorPath>\\Unity.exe" -batchmode -projectPath . -runTests -testPlatform EditMode -filter "BuddyAllocatorTests.Allocate1" -logFile Logs/test.log -quit`
- Build players: use Unity Editor "Build Settings…", or a dedicated CI script if one is added later.

## Coding Style & Naming Conventions

- C#: 4-space indentation, braces on new lines (Allman style), one type per file, namespaces match folder intent.
- Naming follows SRP/Unity conventions: `PascalCase` types/methods, `camelCase` locals/params, `m_` member fields, `k_` constants.
- Keep code in the correct assembly folder (`Runtime/` vs `Editor/`) and update/keep `*.meta` files with any asset change.
- Imports: standard `using` statements (System namespaces first, then Unity namespaces), typically sorted alphabetically by namespace.

## Testing Guidelines

- Uses Unity Test Framework (`com.unity.test-framework` 1.6.0).
- Place tests under `Packages/*/Tests/Editor` (EditMode) or `Packages/*/Tests/Runtime` (PlayMode/Runtime).
- Test class attributes: `[TestFixture]` for NUnit-style tests.
- Test method attributes: `[Test]` for simple tests, `[UnityTest]` for coroutine-based tests requiring frame updates, `[TestCaseSource]` for parameterized tests.
- Use `using var` for disposable test resources; cleanup with `[TearDown]` or `Object.DestroyImmediate` for Unity objects.
- Prefer deterministic tests; avoid GPU/driver-dependent assertions unless explicitly scoped and documented.
- Test naming: `[MethodName]_Should_[ExpectedBehavior]` or descriptive action-based names (e.g., `TestMainLightRenderingLayerMaskSync...`).

## Error Handling & Code Quality

- Use `Debug.LogWarning` for non-critical issues (e.g., missing assets, invalid configurations).
- Avoid empty catch blocks; handle or log exceptions appropriately.
- Assert preconditions and invariants for critical rendering paths.
- No automated linting/formatter configured; follow Unity SRP codebase conventions manually.

## Documentation Style

- Use XML documentation comments (`/// <summary>`) for public APIs, types, and complex methods.
- Inline comments for non-obvious implementation details or workarounds.
- TODO comments are acceptable for tracking known issues or future improvements; mark with `TODO:` prefix.

## Commit & Pull Request Guidelines

- Commit messages commonly use a lightweight Conventional Commits style (examples from history: `feat: ...`, `refactor: ...`).
- PRs should include: purpose, affected package path(s) (e.g., `Packages/Custom_URP/...`), reproduction steps, and before/after screenshots for rendering changes.
- Keep diffs focused; Unity can reserialize assets—avoid unrelated scene/material churn when possible.
