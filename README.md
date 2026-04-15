# Conflux Claude Plugin

Claude Code plugin for [Conflux](https://getconflux.ai) — read and write voice-captured thoughts, session context, and agent handoff notes directly in Claude Code.

## Skills

### `/conflux:thoughts`

Access and contribute to the user's thought stream captured by Conflux.

**Read:** Claude proactively searches recent thoughts for context relevant to your current task. Supports filtering by source (`agent` or `user`).

**Write:** Claude records structured notes at lifecycle moments (task completion, bug fixes, topic switches, session end) so other sessions and agents can pick up where you left off.

## Installation

```bash
claude plugin add conflux
```

Or from source:

```bash
claude --plugin-dir /path/to/conflux-claude-plugin
```

## Requirements

- [Conflux](https://getconflux.ai) macOS app (v0.4.0+)
- `conflux` CLI available at `/usr/local/bin/conflux`

## CLI Commands Used

```bash
conflux thoughts                          # Read recent thoughts
conflux thoughts --search "query"         # Search thoughts
conflux thoughts --source agent           # Agent-written thoughts only
conflux thoughts --source user            # Human thoughts only
conflux think "text" --agent-name NAME    # Write an agent thought
```
