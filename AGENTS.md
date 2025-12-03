# Always respond in Chinese-simplified.

# Repository Guidelines

## Project Structure & Module Organization
- `src/` holds the addon code; `_bastion.lua` is the entry that wires together class, spell, aura, and module helpers.
- Subfolders such as `Class/`, `Unit/`, `Spell/`, `Aura/`, and `Module/` contain reusable primitives; keep new logic grouped by feature.
- `scripts/` hosts example and work-in-progress modules (e.g., `HunterModule.lua`); use this for quick experiments before moving stable code into `src/`.
- `README.md` is the landing page—update it when adding major features or setup changes.

## Build, Test, and Development Commands
- No build step is required; the addon runs directly from this directory inside your WoW installation.
- Quick syntax scan (Lua 5.1-friendly): `find src -name '*.lua' -print0 | xargs -0 -n1 lua -p`.
- In-game reload after changes: type `/reload` in chat; watch the Lua error frame for stack traces.
- For iterative testing of hunter changes, toggle `scripts/HunterModule.lua` and re-run `/reload`.

## Coding Style & Naming Conventions
- Lua style: 2-space indentation, no tabs; prefer `local` for all non-global bindings.
- Files: descriptive CamelCase matching the class/module (e.g., `UnitManager`, `SpellBook`). Avoid spaces in filenames.
- Functions: use verb-based names (`Create`, `Update`, `HandleEvent`); tables that act like classes use PascalCase.
- Keep side effects isolated; favor small helpers in `Module/` or `Library/` over copy-pasting logic.

## Testing Guidelines
- There are no automated tests; validate changes in-game. Reproduce scenarios for rotations, aura handling, and cache updates.
- When fixing bugs, add minimal repro steps as Lua comments near the change and verify with `/reload`.
- Log-only checks: use `print` guarded by feature flags and remove or gate them before merging.

## Commit & Pull Request Guidelines
- Commits: short imperative subjects ("Add hunter focus drain check"); include scope when helpful (`UnitManager: ...`). Group related Lua changes together.
- Before opening a PR: ensure `/reload` passes without Lua errors and update `README.md` if behavior or setup changes.
- PR description: summarize intent, list manual test scenarios (character, spec, encounter), and link any tracked issues or forum threads. Add screenshots of UI elements if visuals change.