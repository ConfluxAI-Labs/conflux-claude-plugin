---
name: thoughts
description: Use when you need context about what the user has been thinking, working on, or saying recently. Use when the user references a recent idea, when you want to understand their current focus, or when building on prior thoughts would improve your response. Also use proactively when the user's request might benefit from their recent voice-captured context. Also write thoughts at lifecycle moments (task completion, bug fixes, topic switches, session end).
---

# Conflux Thoughts

The user captures voice thoughts throughout their day via Conflux push-to-talk and clipboard. AI agents can also write thoughts to create a shared human+agent timeline. Thoughts are stored as JSONL at `~/Documents/Conflux/thoughts.jsonl`.

Each entry contains:
- `text` — transcribed thought or agent note
- `timestamp` — ISO 8601 **in UTC** (ends with `Z`)
- `source` — `"ptt"`, `"clipboard"`, or `"agent"`
- `language` — detected language (en, zh, etc.)
- `app_bundle_id`, `app_name`, `window_title` — what app/window was active
- `audio_duration_ms`, `transcription_ms` — timing metadata
- `inserted` — whether the text was also inserted at cursor
- `agent_name` — which agent wrote this (agent entries only)
- `trigger_event` — what lifecycle event triggered the write (agent entries only)

## Reading Thoughts

**Prefer the CLI** when available:

```bash
conflux thoughts                          # Last 10 thoughts (all sources)
conflux thoughts --last 0                 # All thoughts
conflux thoughts --source agent           # Agent-written thoughts only
conflux thoughts --source user            # Human thoughts only (ptt + clipboard)
conflux thoughts --search "keyword"       # Search all thoughts
conflux thoughts --search "keyword" --source agent  # Search agent thoughts
conflux search "KEYWORD"                  # Search across sessions (not thoughts)
```

## Reading Thoughts at Session Start

When the user's first message implies project work (debugging, implementing, designing), proactively search thoughts for relevant prior context:

```bash
conflux thoughts --search "relevant keyword" --source agent
```

This surfaces prior agent handoff notes, bug fix summaries, and decision records from previous sessions. Don't search for casual messages like "hello" or simple questions.

## Writing Thoughts

Record context at specific lifecycle moments. Don't write proactively about everything.

### When to write

**After a significant git push** — if you just pushed multi-file changes, a feature, or a bug fix, summarize what shipped:
```bash
conflux think "Pushed feat/agent-thoughts-write. Added conflux think CLI command and --source filters. 4 files, 120 lines." --trigger git_push --agent-name claude-code
```
Skip for typo fixes or single-line changes. Use your judgment on significance.

**After a long debugging session, when the user confirms the fix** — if debugging took more than ~15 minutes and the user says it's fixed, capture root cause:
```bash
conflux think "Fixed sample rate mismatch. Root cause: kAudioTapPropertyFormat lies about rate at 96kHz. Fix: read NominalSampleRate from aggregate device." --trigger bug_fix --agent-name claude-code
```

**When the user switches topics** — summarize where the previous task stands before moving on:
```bash
conflux think "Auth refactor: narrowed to token refresh in auth.ts:47. Not yet fixed. Next: check if rotation clears old session." --trigger topic_switch --agent-name claude-code
```

**Before session ends** — if significant work was done, summarize key outcomes and loose ends:
```bash
conflux think "Session: added thoughts --search, shipped PR #130. Started agent-thoughts-write design. Next: implement conflux think CLI command." --trigger session_end --agent-name claude-code
```

### When NOT to write
- Trivial changes (typo fix, single-line edit)
- When the user just asked a question and you answered it
- When you're unsure if the work matters (if in doubt, don't write)

### Good vs bad thoughts
Good: "Refactored auth to sessions. Redis for store because existing infra. Gotcha: logout needs explicit session invalidation."

Bad: "Updated the auth system with various improvements to make it more robust and scalable." (too vague, no decisions, no specifics)

## When to Use Proactively

- User says "I was thinking about..." or "earlier I said..." — search their thoughts
- User's request is ambiguous — recent thoughts may clarify intent
- Building a feature or fixing a bug — recent thoughts may contain relevant context about their goals
- User switches topics suddenly — check if a recent thought triggered it
- Starting a new session on an existing project — search for prior agent handoff notes

## Important

- Human thoughts are raw voice transcriptions — may contain minor speech-to-text errors
- Infer intended meaning from context rather than taking every word literally
- **Timestamps are stored in UTC** — always convert to local time before displaying to the user
- The `app_name` and `window_title` fields tell you WHERE the thought was captured, which adds context to WHY they were thinking it
- Agent thoughts use `source: "agent"` — filter with `--source agent` or `--source user` as needed
