# Anthropic stop_reason

Anthropic returns one of seven documented `stop_reason` values, and its quick reference gives an action for each.

| Value | When it occurs | What to do |
|---|---|---|
| `end_turn` | Claude finished its response naturally. | Use the response. |
| `max_tokens` | The response reached your `max_tokens` limit. | Raise `max_tokens` or continue the response. |
| `stop_sequence` | Claude emitted one of your `stop_sequences`. | Read `stop_sequence` to see which one fired. |
| `tool_use` | Claude is calling a tool. | Run the tool and return the result. A server tool call still missing its result block completes in a later response. |
| `pause_turn` | A server-tool loop reached its iteration limit. | Send the assistant content back to continue. |
| `refusal` | Claude declined to respond. | Read `stop_details` and retry on a fallback model. |
| `model_context_window_exceeded` | The response filled the model's context window. | Treat the response as truncated. |

Both columns are quoted from the quick reference.

## Notes, each from the same page

- **pause_turn.** Returned when the server-side sampling loop reaches its iteration limit while executing server tools such as web search; the default limit is 10 iterations per request. Continue by sending the response back as-is. A client `tool_use` wait is never `pause_turn`: that is `tool_use`, and you continue it with `tool_result` blocks.
- **max_tokens inside a tool call.** If the truncated response contains an incomplete tool use block, retry the request with a higher `max_tokens` to get the full tool use.
- **stop_details.** It is `null` for every stop reason other than `refusal`.
- **model_context_window_exceeded.** The API returns it without a beta header on Sonnet 4.5 and newer models. Earlier models need the `model-context-window-exceeded-2025-08-26` beta header.
- **Empty end_turn.** Anthropic documents an empty response (exactly 2 to 3 tokens with no content) with `end_turn`, particularly after tool results. Its fix: add a continuation prompt in a new user message rather than retrying with the empty response.

Source (checked 2026-10-16):
https://platform.claude.com/docs/en/build-with-claude/handling-stop-reasons
