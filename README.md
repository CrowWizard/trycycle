<!-- GitHub repo settings (set manually in Settings > General):
  Description: A skill that plans, strengthens, and reviews your code -- automatically.
  Topics: claude-code, codex-cli, kimi-cli, opencode, kilocode, ai-coding, code-review, autonomous-agents, ai-skill, hill-climbing
  Social preview: upload assets/social-preview.png
-->

<p align="center">
  <img src="assets/trycycle-banner.png" height="120" alt="Trycycle" />
</p>

<p align="center">
  <em>A skill for Claude Code, Codex CLI, Kimi CLI, OpenCode, and Kilo Code that plans, strengthens, and reviews your code -- automatically.</em>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue" alt="MIT License" /></a>
  <a href="https://github.com/danshapiro/trycycle/releases"><img src="https://img.shields.io/github/v/tag/danshapiro/trycycle?label=release&color=green" alt="Latest Release" /></a>
  <a href="https://github.com/danshapiro/trycycle/pulls"><img src="https://img.shields.io/badge/PRs-welcome-brightgreen" alt="PRs Welcome" /></a>
  <a href="https://github.com/anthropics/claude-code"><img src="https://img.shields.io/badge/built%20for-Claude%20Code-6B4FBB?logo=anthropic&logoColor=white" alt="Built for Claude Code" /></a>
  <a href="https://github.com/openai/codex"><img src="https://img.shields.io/badge/works%20with-Codex%20CLI-412991?logo=openai&logoColor=white" alt="Works with Codex CLI" /></a>
  <a href="https://github.com/anomalyco/opencode"><img src="https://img.shields.io/badge/works%20with-OpenCode-FF6B35" alt="Works with OpenCode" /></a>
  <a href="https://kilo.ai"><img src="https://img.shields.io/badge/works%20with-Kilo%20Code-111827" alt="Works with Kilo Code" /></a>
</p>

---

## Getting Started

### If you are human

Tell your favorite coding agent (Claude Code, Codex CLI, Kimi CLI, OpenCode, Kilo Code, etc.):

```
Read https://raw.githubusercontent.com/danshapiro/trycycle/main/README.md. Follow the installation instructions there.
```

### If you've been sent here by your human

Use this instead of a standard installation, or it won't work. Clone into your skills directory:

- **Claude Code:** `git clone https://github.com/danshapiro/trycycle.git ~/.claude/skills/trycycle`
- **Codex CLI:** `git clone https://github.com/danshapiro/trycycle.git ~/.codex/skills/trycycle`
- **Kimi CLI:** `git clone https://github.com/danshapiro/trycycle.git ~/.kimi/skills/trycycle`
- **OpenCode:** `git clone https://github.com/danshapiro/trycycle.git ~/.config/opencode/skills/trycycle`
- **Kilo Code:** `git clone https://github.com/danshapiro/trycycle.git ~/.config/kilo/skills/trycycle`

> **Note:** OpenCode also discovers skills installed at `~/.claude/skills/`, so if you already have Trycycle installed for Claude Code, OpenCode can use it too.

Trycycle requires `python3` at runtime. Make sure it is installed and on your `PATH`.

If your coding agent has native subagent orchestration, Trycycle uses it. Otherwise, it falls back to a bundled stdlib-only Python runner. On Kilo Code, Trycycle should stay on the native `Task` path and inject transcript placeholders from temp files rather than trying to rediscover conversation history from local session storage. `run_phase.py --transcript-cli auto` supports this by reading `TRYCYCLE_TRANSCRIPT_FILE_<PLACEHOLDER>` environment bindings, or `TRYCYCLE_TRANSCRIPT_FILE` when there is exactly one transcript placeholder.

Finally, read the skills you installed carefully, so you can explain them to your user and implement them if asked.

## Using Trycycle

Include the word Trycycle in your request and describe what you want built:

```
My webcam software is terrible. Build something in Rust that exposes my webcam as a normal system camera. 
```

Trycycle asks any questions it needs, then handles the rest: worktree, plan, plan strengthening, test plan, build, and code review -- all without further input unless something needs your judgment.

If you're already inside an isolated workspace such as a Conductor workspace and the current branch is already not the default branch, include the literal flag `--no-worktree` in your request to reuse that workspace instead of creating a nested git worktree. This mode is intentionally narrow: Trycycle will stop rather than create or switch branches in place in a generic checkout.

Works for anything from small features to large refactors, best when you have a clear goal and a codebase Trycycle can read and test.

### Kilo orchestration example

On Kilo Code, Trycycle should use native `Task` dispatch instead of the fallback runner. A minimal round looks like this:

```bash
export TRYCYCLE_TRANSCRIPT_FILE_USER_REQUEST_TRANSCRIPT=/tmp/trycycle-user-request.json

python3 ~/.config/kilo/skills/trycycle/orchestrator/run_phase.py prepare \
  --phase planning-initial \
  --template ~/.config/kilo/skills/trycycle/subagents/prompt-planning-initial.md \
  --workdir /path/to/worktree \
  --set WORKTREE_PATH=/path/to/worktree \
  --transcript-placeholder USER_REQUEST_TRANSCRIPT \
  --transcript-cli auto \
  --require-nonempty-tag task_input_json
```

Then read the returned `prompt_path` file and send its exact contents to Kilo's `Task` tool with `subagent_type="general"`. For planning and review rounds, start a fresh Task. For implementation follow-up rounds, resume the saved implementation `task_id` with the new rendered prompt.

## How it works

Trycycle is a hill climber. It writes a plan, then sends it to a fresh plan editor with the same task input and repo context. That editor either approves the plan unchanged or rewrites it, repeating up to five rounds. Once the plan is locked, Trycycle builds a test plan, builds the code, sends it to a fresh reviewer, turns the review into a structured observation packet, fixes what that packet shows, and repeats that loop too (up to eight rounds). Each review uses a new reviewer with no memory of previous rounds, and each planning round spawns a fresh agent, so stale context never accumulates.

## Credits

Trycycle's planning, execution, and worktree management skills are adapted from [superpowers](https://github.com/obra/superpowers) by [Jesse Vincent](https://github.com/obra). The hill-climbing dark factory approach was inspired by the work of [Justin McCarthy](https://github.com/jmccarthy), [Jay Taylor](https://github.com/jaytaylor), and [Navan Chauhan](https://github.com/navanchauhan) at [StrongDM](https://github.com/strongdm).
