## 2026-04-14

- Created worktree `.worktrees/add-kilo-native-support` from `main` for isolated Kilo support changes.
- Updated `SKILL.md` to document Kilo native subagent dispatch through `Task` and transcript injection via `--transcript-file`.
- Updated `orchestrator/run_phase.py` to accept transcript placeholders from existing files without transcript CLI lookup.
- Updated `README.md` to add Kilo Code installation and usage guidance.
- Added `tests/test_run_phase.py` coverage for explicit transcript file injection.
