# TurtleSilicon

macOS Apple Silicon launcher for WoW WotLK 3.3.5a via CrossOver + DXVK + rosettax87.

## Commands

Use `mise` to run tasks (not `go` directly, not `mise run`):

- `mise build` — development build with debug symbols
- `mise release` — optimized release build
- `mise clean` — remove build artifacts
- `mise run` — build and open

## Architecture

- Go + Fyne UI framework
- Launches WoW.exe through: rosettax87 → wineloader2 (CrossOver) → WoW.exe
- DXVK d3d9.dll translates D3D9 → Vulkan → MoltenVK → Metal
- Key env vars: `WINEDLLOVERRIDES="d3d9=n,b"`, `CX_ROOT`, `DXVK_ASYNC=1`
- WoW Config.wtf must have `gxApi "d3d9"` — OpenGL mode rejects Apple GPUs

## Key Paths

- `pkg/launcher/launcher.go` — main game launch logic
- `pkg/launcher/version_launcher.go` — alternate version launcher
- `pkg/ui/` — Fyne UI components
- `pkg/paths/` — path resolution
- `rosettax87/` — bundled x87 FPU emulation binaries
- `winerosetta/` — bundled winerosetta DLL

## Commits

- Keep the subject line to 50 characters or fewer
- Capitalize the subject: `Fix bundled icon switching` not `fix bundled icon switching`
- Blank line, then 1-3 sentence description of "why" (wrap at 72 chars)
- No bullet points, NEVER add "Co-Authored-By" or other footers
- Check `git log -n 5` first to match existing style
- Never use `--oneline` — commit bodies carry important context

## WoW Graphics Pipeline

- WoW Config.wtf `gxApi "d3d9"` is critical — without it, falls back to wined3d OpenGL which is broken/slow on Apple Silicon
- WoW strips gxApi from Config.wtf on launch — `EnsureGxApiD3d9()` re-writes it before every launch
- `gxApi "OpenGL"` → WoW's own OpenGL renderer rejects Apple GPUs entirely
- Rendering path: WoW D3D9 → DXVK d3d9.dll → Vulkan → winevulkan → MoltenVK → Metal
- `WINEDLLOVERRIDES="d3d9=n,b"` tells Wine to use bundled DXVK instead of builtin wined3d

## Known Issues

- Game launches and renders at ~1fps — root cause unknown (see memory/wotlk-debugging.md)
- DivxDecoder.dll is a PE import dependency of WoW.exe — cannot be removed

## Scratch Files

Use `./tmp/` for scratch files (gitignored).
