---
name: thoughts
description: Use when you need context about what the user has been thinking, working on, or saying recently. Use when the user references a recent idea, when you want to understand their current focus, or when building on prior thoughts would improve your response. Also use proactively when the user's request might benefit from their recent voice-captured context.
---

# Conflux Thoughts

The user captures voice thoughts throughout their day via Conflux push-to-talk. These are stored as JSONL at `~/Documents/Conflux/thoughts.jsonl` (or wherever their Conflux storage root is configured).

Each entry contains:
- `text` — transcribed thought
- `timestamp` — ISO 8601 **in UTC** (ends with `Z`)
- `language` — detected language (en, zh, etc.)
- `app_bundle_id`, `app_name`, `window_title` — what app/window was active
- `audio_duration_ms`, `transcription_ms` — timing metadata
- `inserted` — whether the text was also inserted at cursor

## How to Access

**Prefer the CLI** when available — it handles timezone conversion automatically:

```bash
conflux thoughts              # Last 10 thoughts, local time
conflux thoughts --last 0     # All thoughts
conflux search "KEYWORD"      # Search across all sessions and thoughts
```

**Fallback (raw JSONL)** — timestamps are UTC, convert to local time:

**Recent thoughts (last 10):**
```bash
tail -10 ~/Documents/Conflux/thoughts.jsonl | while IFS= read -r line; do
  ts=$(echo "$line" | jq -r '.timestamp')
  local_ts=$(date -jf "%Y-%m-%dT%H:%M:%SZ" "$ts" "+%Y-%m-%d %I:%M %p" 2>/dev/null || echo "$ts")
  app=$(echo "$line" | jq -r '.app_name // "unknown"')
  text=$(echo "$line" | jq -r '.text')
  echo "[$local_ts] ($app): $text"
done
```

**Today's thoughts:**
```bash
TODAY=$(date +%Y-%m-%d)
tail -100 ~/Documents/Conflux/thoughts.jsonl | while IFS= read -r line; do
  ts=$(echo "$line" | jq -r '.timestamp')
  local_date=$(date -jf "%Y-%m-%dT%H:%M:%SZ" "$ts" "+%Y-%m-%d" 2>/dev/null)
  [ "$local_date" = "$TODAY" ] || continue
  local_ts=$(date -jf "%Y-%m-%dT%H:%M:%SZ" "$ts" "+%I:%M %p" 2>/dev/null || echo "$ts")
  app=$(echo "$line" | jq -r '.app_name // "unknown"')
  text=$(echo "$line" | jq -r '.text')
  echo "[$local_ts] ($app): $text"
done
```

**Search by keyword:**
```bash
grep -i "SEARCH_TERM" ~/Documents/Conflux/thoughts.jsonl | while IFS= read -r line; do
  ts=$(echo "$line" | jq -r '.timestamp')
  local_ts=$(date -jf "%Y-%m-%dT%H:%M:%SZ" "$ts" "+%Y-%m-%d %I:%M %p" 2>/dev/null || echo "$ts")
  text=$(echo "$line" | jq -r '.text')
  echo "[$local_ts]: $text"
done
```

## When to Use Proactively

- User says "I was thinking about..." or "earlier I said..." — search their thoughts
- User's request is ambiguous — recent thoughts may clarify intent
- Building a feature or fixing a bug — recent thoughts may contain relevant context about their goals
- User switches topics suddenly — check if a recent thought triggered it

## Important

- Thoughts are raw voice transcriptions — may contain minor speech-to-text errors
- Infer intended meaning from context rather than taking every word literally
- **Timestamps are stored in UTC** — always convert to local time before displaying to the user
- The `app_name` and `window_title` fields tell you WHERE the thought was captured, which adds context to WHY they were thinking it
