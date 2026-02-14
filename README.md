# Ralph

Autonomous bead worker powered by AI. Ralph picks up ready work from your [beads](https://github.com/sasha-incorporated/beads_rust) issue tracker, completes it, commits, and loops to the next bead.

Routes to the right CLI based on model:
- **Claude models** (haiku, sonnet, opus) → Claude CLI
- **Everything else** (gpt-5, chatgpt-5.3, ...) → Cursor agent

Beads with a `model:<name>` label automatically use that model.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/sasha-incorporated/ralph/main/ralph -o ~/.local/bin/ralph
chmod +x ~/.local/bin/ralph
```

Or with wget:

```bash
wget -qO ~/.local/bin/ralph https://raw.githubusercontent.com/sasha-incorporated/ralph/main/ralph
chmod +x ~/.local/bin/ralph
```

Make sure `~/.local/bin` is in your PATH.

## Requirements

- [Claude CLI](https://docs.anthropic.com/en/docs/claude-cli) for Claude models (haiku, sonnet, opus)
- [Cursor CLI](https://cursor.com/cli) for other models (gpt-5, chatgpt-5.3, etc.)
- [beads_rust](https://github.com/sasha-incorporated/beads_rust) (`br`) installed
- A project with `.beads/issues.jsonl`

## Usage

```bash
# Run from any project directory with beads
cd ~/code/my-project
ralph                              # Work on any ready beads
ralph --label bug                  # Only beads with 'bug' label
ralph --max 5                      # Stop after 5 beads
ralph --model sonnet               # Use sonnet instead of haiku
ralph --model chatgpt-5.3          # Use ChatGPT via Cursor agent

# Queue mode: specific beads with specific models
ralph --queue "bd-21cp:sonnet,bd-35y4:chatgpt-5.3,bd-1lzt:opus"
ralph --queue "bd-abc,bd-def" --model haiku

# Model labels: beads auto-select their model
br label add bd-21cp model:opus
ralph  # bd-21cp will use opus automatically
```

## Options

| Option | Description | Default |
|--------|-------------|---------|
| `--label <label>` | Filter beads by label | (all beads) |
| `--max <n>` | Maximum iterations | unlimited |
| `--model <model>` | Default model. Claude models use Claude CLI; others use Cursor agent. | haiku |
| `--retries <n>` | Max retries per bead | 5 |
| `--queue <spec>` | Explicit queue (id:model,...) | (auto-discover) |

## Model selection

Ralph picks the model for each bead using this priority:

1. **Queue explicit** — `--queue "bd-abc:opus"` overrides everything
2. **Bead label** — a `model:<name>` label on the bead (e.g. `model:chatgpt-5.3`)
3. **`--model` flag** — the default passed on the command line
4. **Built-in default** — `haiku`

## How it works

1. **Find work** - Uses `br ready` to find the next unblocked bead
2. **Pick model** - Checks bead labels for `model:<name>`, falls back to default
3. **Invoke agent** - Routes to Claude CLI or Cursor agent based on model
4. **Verify** - Checks if the agent closed the bead
5. **Retry** - If not closed, retries up to `--retries` times
6. **Loop** - Moves to the next bead

## License

MIT
