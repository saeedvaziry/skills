---
name: check-codex-context-remaining
description: Fetch a local Codex thread's remaining context-window percentage and token counts from Codex session telemetry. Use in Codex CLI, or another local Codex surface backed by the same runtime, when the user asks how much Codex context remains, how full the context window is, for current context usage, or for the model-visible equivalent of Codex `/status`. Do not use for non-Codex agent harnesses.
---

# Check Codex Context Remaining

Fetch telemetry for the current thread instead of estimating from conversation length.

## Compatibility

Treat this as a Codex-specific workflow, not a cross-harness standard. It depends on:

- `CODEX_THREAD_ID` being exported to the agent process
- Codex session logs under `${CODEX_HOME:-${HOME}/.codex}/sessions`
- Codex JSONL `event_msg` → `token_count` telemetry
- Codex's current context-percentage calculation

Use it with Codex CLI and with local Codex app or IDE executions only when those prerequisites are present. Do not use it for Claude Code, Cursor, Gemini CLI, GitHub Copilot, or other coding-agent harnesses; use that harness's native status command or telemetry API instead. It may also be unavailable in remote or cloud Codex executions that do not expose local session logs.

## Procedure

1. Confirm this is a Codex process with `CODEX_THREAD_ID`. If not, stop and explain that the skill is incompatible with the current harness.
2. Run the command below from the current Codex process so `CODEX_THREAD_ID` identifies the active thread.
3. Report `remaining_percent`, `remaining_tokens`, and `context_window`.
4. Describe the result as a snapshot from the latest token-count event. The current response can consume additional context after the snapshot.

```bash
codex_state_dir="${CODEX_HOME:-${HOME}/.codex}"
thread_id="${CODEX_THREAD_ID:-}"

if [ -z "$thread_id" ]; then
  printf '%s\n' '{"error":"CODEX_THREAD_ID is not available in this process"}'
  exit 1
fi

case "$thread_id" in
  *[!A-Za-z0-9-]*)
    printf '%s\n' '{"error":"CODEX_THREAD_ID contains unexpected characters"}'
    exit 1
    ;;
esac

session_file=$(find "$codex_state_dir/sessions" -type f \
  -name "*${thread_id}*.jsonl" -print -quit 2>/dev/null)

if [ -z "$session_file" ]; then
  printf '%s\n' '{"error":"session log for the current thread was not found"}'
  exit 1
fi

latest_event=$(
  jq -c '
    select(
      .type == "event_msg"
      and .payload.type == "token_count"
      and .payload.info != null
    )
    | {timestamp, info: .payload.info}
  ' "$session_file" | tail -n 1
)

if [ -z "$latest_event" ]; then
  printf '%s\n' '{"error":"the session has no token-count event yet"}'
  exit 1
fi

printf '%s\n' "$latest_event" | jq '
  def max_zero: if . < 0 then 0 else . end;

  12000 as $baseline
  | (.info.model_context_window // 0) as $window
  | (.info.last_token_usage.total_tokens // 0) as $used
  | if $window <= $baseline then
      {error: "invalid or unavailable context-window size"}
    else
      ($window - $baseline) as $effective_window
      | (($used - $baseline) | max_zero) as $user_used
      | (($effective_window - $user_used) | max_zero) as $remaining
      | {
          sampled_at: .timestamp,
          context_window: $window,
          tokens_in_context: $used,
          remaining_tokens: $remaining,
          remaining_percent:
            (($remaining * 100 / $effective_window) | round)
        }
    end
'
```

## Interpretation

- Use `last_token_usage.total_tokens`; `total_token_usage` is cumulative across turns and does not represent the current context size.
- The 12,000-token baseline matches Codex's current `/status` calculation and accounts for fixed prompts, tools, and compaction headroom.
- Do not print or summarize other JSONL fields. Session logs contain conversation and tool content.
- If the command fails, report its specific error. Do not search unrelated home-directory files.
- Do not present this procedure as portable to another agent harness merely because that harness has a context window or a status command.
- If a future Codex version disagrees with this result, re-check Codex's `BASELINE_TOKENS` and `percent_of_context_window_remaining` implementation.
