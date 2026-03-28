# Conflux Claude Plugin

Claude Code plugin for [Conflux](https://getconflux.ai) — access your voice-captured thoughts and session context directly in Claude Code.

## Skills

### `/conflux:thoughts`

Access your push-to-talk voice thoughts captured by Conflux. Claude can proactively use your recent thoughts as context when they're relevant to your current task.

## Installation

```bash
claude --plugin-dir /path/to/conflux-claude-plugin
```

## Requirements

- [Conflux](https://getconflux.ai) macOS app with push-to-talk enabled
- Thoughts stored at `~/Documents/Conflux/thoughts.jsonl` (default location)
