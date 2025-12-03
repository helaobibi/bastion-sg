# Always respond in Chinese-simplified.

# Repository Guidelines

## Project Structure & Module Organization
- `src/` holds the addon code; `_bastion.lua` is the entry that wires together class, spell, aura, and module helpers.
- Subfolders such as `Class/`, `Unit/`, `Spell/`, `Aura/`, and `Module/` contain reusable primitives; keep new logic grouped by feature.
- `scripts/` hosts example and work-in-progress modules (e.g., `HunterModule.lua`); use this for quick experiments before moving stable code into `src/`.
- `README.md` is the landing page—update it when adding major features or setup changes.

## Loading & Module System
- `_bastion.lua` 定义了路径前缀：`@` 会从 `scripts/bastion/scripts/` 加载，`~` 会从 `scripts/bastion/` 加载；`Bastion.require("ClassName")` 会自动寻找 `src/ClassName/ClassName.lua`。
- 引导过程会执行 `Load("@Libraries/")`、`Load("@Modules/")`、`Load("@")`，会把对应目录下的所有 `.lua/.luac` 按文件名 `require`；实验脚本按需放在 `scripts/Modules/` 或 `scripts/`，命名用 CamelCase。
- 模块用 `Bastion.Module:New(name)` 创建后默认 `enabled=false`，注册前应显式 `:Enable()`（或设置 `enabled = true`），否则主循环不会执行；`/bastion module <name>` 使用实例上的 `name` 进行开关。
- 主 Ticker 每 0.1 秒刷新战斗状态、`ObjectManager:Refresh()` 并遍历模块 `:Tick()`；新逻辑要避免重计算，优先用 `Cacheable/Refreshable` 或一次取值多次复用。

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

## UI & Commands
- `src/herui/hunter.lua` 在初始化时注册 `/hunter` 命令并暴露 `HERUINormal/HERUIAOE/HERUISimple/HERUIAutoTarget/HERUIPetAttack/HERUIPetFollow/HERUIGrowl` 等全局函数；`scripts/HunterModule.lua` 直接依赖这些名字，新增/修改状态时必须同步更新全局导出。
- Slash 命令通过 `Bastion.Command:New('prefix')` 与 `:Register` 定义，当前已有 `/bastion`（debug、module、mplus、draw 等）和 `/hunter`，避免前缀冲突并复用该封装。
- UI 位置/图标会通过 `WriteFile` 持久化到 `src/StatusFrame/status_frame_position.lua`、`src/StatusFrame/status_frame_icon.lua` 与 `src/herui/herui_position.lua`，必须保持 `return { ... }` 格式和现有路径；移动或修改 UI 时记得兼容这些文件。
- `DrawLine` 默认在引导时调用 `:SetupDefaultSync()`，使用 `/bastion draw` 控制开关；如需调整样式请用 `SetConfig/SetColor/SetWidth/SetAlpha` 而不是直接改内部 `draw`。

## Testing Guidelines
- There are no automated tests; validate changes in-game. Reproduce scenarios for rotations, aura handling, and cache updates.
- When fixing bugs, add minimal repro steps as Lua comments near the change and verify with `/reload`.
- Log-only checks: use `print` guarded by feature flags and remove or gate them before merging.

## Commit & Pull Request Guidelines
- Commits: short imperative subjects ("Add hunter focus drain check"); include scope when helpful (`UnitManager: ...`). Group related Lua changes together.
- Before opening a PR: ensure `/reload` passes without Lua errors and update `README.md` if behavior or setup changes.
- PR description: summarize intent, list manual test scenarios (character, spec, encounter), and link any tracked issues or forum threads. Add screenshots of UI elements if visuals change.
