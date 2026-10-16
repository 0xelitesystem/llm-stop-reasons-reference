# The action table

Almost every stop falls into one of five actions: use, continue, retry with more room, fall back, or show the user.

Rows are actions, cells are value names. Anthropic placements follow its quick reference, except `model_context_window_exceeded`, whose documented action is "Treat the response as truncated."; its row is a house reading. Every OpenAI and Gemini placement is a house recommendation, because those references give none. No cell says two values mean the same thing.

| Action | Anthropic | OpenAI Chat | OpenAI Responses | Gemini |
|---|---|---|---|---|
| Use the output | `end_turn`, `stop_sequence` | `stop`, once `message.refusal` is empty | `completed` | `STOP` |
| Continue the loop | `tool_use` (return `tool_result`), `pause_turn` (send the response back as-is) | `tool_calls` | no status value; tool calls arrive as `function_call` output items | `CONTINUATION` |
| Retry with more room, or continue | `max_tokens`, `model_context_window_exceeded` | `length` | `incomplete` + `max_output_tokens` | `MAX_TOKENS` |
| Fall back, or show the user | `refusal` (Anthropic says retry on a fallback model) | `content_filter`, `message.refusal` | `incomplete` + `content_filter` | `SAFETY`, `RECITATION`, `BLOCKLIST`, `PROHIBITED_CONTENT`, `SPII`, `blockReason` |
| Fix the request | | | | `MALFORMED_FUNCTION_CALL`, `UNEXPECTED_TOOL_CALL`, `MISSING_THOUGHT_SIGNATURE` |

The last row is a house reading of Gemini's own descriptions: an invalid function call, a tool call with no tools enabled, a missing thought signature. Each points at the request, so fix the request before you resend it.

Truncation is a state, not an action. `max_tokens`, `length`, `max_output_tokens` and `MAX_TOKENS` all mean the text you hold is incomplete. Never parse it as finished JSON.

An empty Anthropic `end_turn` stays in the first row, with its documented fix: a continuation prompt in a new user message.

## The three bugs this repo exists to stop

1. **pause_turn treated as done.** The response may hold a server tool call with no result block. Send it back as-is.
2. **An incomplete reasoning response read as an empty answer.** Status `incomplete`, reason `max_output_tokens`, no `output_text`: the budget ran out during reasoning. Give it more room.
3. **An empty Gemini candidates list read without promptFeedback.** If `blockReason` is set, the prompt was blocked.

Gemini values left out of this table keep their chapter 05 house action.

Sources: chapters 02 to 05.
