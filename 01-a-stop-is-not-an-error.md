# A stop is not an error

Every value in this repo arrives in a successful HTTP 200 response, so check the stop signal on every response before you use the text.

Anthropic draws the line in two lists on its stop reasons page:

| Stop reasons (successful responses) | Errors (failed requests) |
|---|---|
| Part of the response body | HTTP status codes 4xx or 5xx |
| Indicate why generation stopped normally | Indicate request processing failures |
| Response contains valid content | Response contains error details |

A refusal follows the same rule: "Safety classifiers return this stop reason as a normal HTTP 200 response, not an error." A wrapper that reads status 200 as a finished answer hands a refusal to the user as if it were an answer.

## Streaming

Anthropic says that when streaming, `stop_reason` is:

- `null` in the initial `message_start` event
- Provided in the `message_delta` event
- Not provided in any other events

So a stream consumer reads the value from `message_delta`. A `null` seen in `message_start` means nothing yet.

Gemini says of `finishReason` on a candidate: "If empty, the model has not stopped generating tokens." An empty value means generation is still running, not that it ended cleanly.

This chapter covers only the streaming behaviour quoted above. To inspect a captured stream, see https://github.com/0xelitesystem/llm-stream-inspector.

## The order of checks

1. HTTP status. A 4xx or 5xx is an error; handle it as one.
2. The stop signal. Read it on every 200: `stop_reason` (Anthropic), `finish_reason` on each choice (OpenAI Chat), `status` and `incomplete_details` (OpenAI Responses), `finishReason` on each candidate and `promptFeedback` (Gemini).
3. Only then, the text.

Sources (checked 2026-10-16):
https://platform.claude.com/docs/en/build-with-claude/handling-stop-reasons
https://ai.google.dev/api/generate-content
