---
name: thoughts
description: Use when you need context about what the user has been thinking, working on, or saying recently. Use when the user references a recent idea, when you want to understand their current focus, or when building on prior thoughts would improve your response. Also use proactively when the user's request might benefit from their recent voice-captured context.
---

# Conflux Thoughts

The user captures voice thoughts throughout their day via Conflux push-to-talk. These are stored as JSONL at `~/Documents/Conflux/thoughts.jsonl` (or wherever their Conflux storage root is configured).

Each entry contains:
- `text` — transcribed thought
- `timestamp` — ISO 8601
- `language` — detected language (en, zh, etc.)
- `app_bundle_id`, `app_name`, `window_title` — what app/window was active
- `audio_duration_ms`, `transcription_ms` — timing metadata
- `inserted` — whether the text was also inserted at cursor

## How to Access

**Recent thoughts (last 10):**
```bash
tail -10 ~/Documents/Conflux/thoughts.jsonl | jq -r '"[\(.timestamp)] (\(.app_name // "unknown")): \(.text)"'
```

**Today's thoughts:**
```bash
TODAY=$(date -u +%Y-%m-%d); grep "$TODAY" ~/Documents/Conflux/thoughts.jsonl | jq -r '"[\(.timestamp)] (\(.app_name // "unknown")): \(.text)"'
```

**Search by keyword:**
```bash
grep -i "SEARCH_TERM" ~/Documents/Conflux/thoughts.jsonl | jq -r '"[\(.timestamp)]: \(.text)"'
```

## When to Use Proactively

- User says "I was thinking about..." or "earlier I said..." — search their thoughts
- User's request is ambiguous — recent thoughts may clarify intent
- Building a feature or fixing a bug — recent thoughts may contain relevant context about their goals
- User switches topics suddenly — check if a recent thought triggered it

## Important

- Thoughts are raw voice transcriptions — may contain minor speech-to-text errors
- Infer intended meaning from context rather than taking every word literally
- The `app_name` and `window_title` fields tell you WHERE the thought was captured, which adds context to WHY they were thinking it
