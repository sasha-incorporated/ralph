# Ralph

Autonomous bead worker powered by Claude Code. Ralph picks up ready work from your [beads](https://github.com/sasha-incorporated/beads_rust) issue tracker, completes it, commits, and loops to the next bead.

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

- [Claude CLI](https://docs.anthropic.com/en/docs/claude-cli) installed and authenticated
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

# Queue mode: specific beads with specific models
ralph --queue "bd-21cp:sonnet,bd-35y4:opus"
ralph --queue "bd-abc,bd-def" --model haiku
```

## Options

| Option | Description | Default |
|--------|-------------|---------|
| `--label <label>` | Filter beads by label | (all beads) |
| `--max <n>` | Maximum iterations | unlimited |
| `--model <model>` | Model: haiku, sonnet, opus | haiku |
| `--retries <n>` | Max retries per bead | 5 |
| `--queue <spec>` | Explicit queue (id:model,...) | (auto-discover) |

## How it works

1. **Find work** - Uses `br ready` to find the next unblocked bead
2. **Invoke Claude** - Passes the bead to Claude Code in YOLO mode
3. **Verify** - Checks if Claude closed the bead
4. **Retry** - If not closed, retries up to `--retries` times
5. **Loop** - Moves to the next bead

## License

MIT
