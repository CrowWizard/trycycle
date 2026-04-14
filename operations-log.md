## 2026-04-14

- Created worktree `.worktrees/add-kilo-native-support` from `main` for isolated Kilo support changes.
- Updated `SKILL.md` to document Kilo native subagent dispatch through `Task` and transcript injection via `--transcript-file`.
- Updated `orchestrator/run_phase.py` to accept transcript placeholders from existing files without transcript CLI lookup.
- Extended `orchestrator/run_phase.py --transcript-cli auto` to detect Kilo via `KILO=1` and resolve transcript files from `TRYCYCLE_TRANSCRIPT_FILE_<PLACEHOLDER>` or `TRYCYCLE_TRANSCRIPT_FILE`.
- Updated `README.md` to add Kilo Code installation and usage guidance.
- Added `tests/test_run_phase.py` coverage for explicit transcript file injection and Kilo auto transcript bindings.
- Added concrete Kilo `Task` orchestration examples to `SKILL.md`, `README.md`, and the explorer intro mirror.
- Added `orchestrator/user-request-transcript/kilo_cli.py` to read Kilo transcripts from `~/.local/share/kilo/kilo.db` with direct current-session lookup and canary fallback.
- Updated `orchestrator/user-request-transcript/build.py` and `orchestrator/run_phase.py` so Kilo auto-detection now prefers native transcript lookup while keeping explicit transcript-file overrides.
- Extended transcript and phase tests to cover Kilo native lookup from the local SQLite session store.
