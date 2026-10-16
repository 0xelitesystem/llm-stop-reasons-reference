# OpenAI Responses status and incomplete_details

The Responses API reports a top-level `status`, and an incomplete response says why in `incomplete_details.reason`.

## status

The reference: "The status of the response generation. One of completed, failed, in_progress, cancelled, queued, or incomplete."

House reading, not OpenAI's wording: only `completed` is a finished answer. `queued` and `in_progress` are not finished yet, and `failed`, `cancelled` and `incomplete` each need handling before you use any text.

## incomplete_details.reason

The reference lists four values: `max_output_tokens`, `max_messages`, `content_filter` and `steered`.

| Reason | What the reference says |
|---|---|
| `max_output_tokens` | Listed; the reasoning guide describes it (below). |
| `max_messages` | Documented value, no description in the reference. |
| `content_filter` | Listed, with no further description. |
| `steered` | "steered means the response stopped at a safe output boundary after a WebSocket response.steer event." |

## Reasoning models can stop before any text

The reasoning guide says that when the generated tokens reach the context window or your `max_output_tokens`, the response comes back `incomplete` with reason `max_output_tokens`, and: "This might occur before any visible output tokens are produced, meaning you could incur costs for input and reasoning tokens without receiving a visible response."

Its own example tells the two cases apart. After matching `status` incomplete and reason `max_output_tokens`, it checks whether `output_text` has any length: text means partial output, no text means the model ran out of tokens during reasoning. An empty answer here is not an empty reply from the model. It is a budget problem.

The guide also says: "OpenAI recommends reserving at least 25,000 tokens for reasoning and outputs when you start experimenting with these models."

## Tool calls

No `status` value means waiting on a tool. Tool calls arrive as `function_call` items in the `output` array, which the reference describes as "A tool call to run a function."

Sources (checked 2026-10-16):
https://developers.openai.com/api/reference/resources/responses/methods/create
https://developers.openai.com/api/docs/guides/reasoning
