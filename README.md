# LLM Stop Reasons

A plain-language reference that maps every documented reason the Anthropic, OpenAI and Gemini APIs give for ending a response to what the calling code should do next: use the output, continue, retry with more room, fall back, or show the user. It covers Anthropic stop_reason, OpenAI Chat finish_reason, OpenAI Responses status and incomplete_details, and Gemini finishReason and blockReason.

Each chapter quotes the provider's own reference, keeps that apart from the house recommendation, and ends with its sources and the date they were checked. Where a provider documents no action, or no meaning, the chapter says so instead of guessing.

## The core idea

A stop reason is part of a successful response, not an error, so code that treats every 200 as a finished answer will mishandle paused turns, truncated output and refusals. The three providers name the same events differently. One event, hitting the output limit, has a name in each of them: `max_tokens`, `length`, `max_output_tokens` and `MAX_TOKENS`. Others exist in only one: `pause_turn` (Anthropic), `steered` (OpenAI Responses), `MALFORMED_FUNCTION_CALL` (Gemini).

## What is inside

- [01-a-stop-is-not-an-error.md](01-a-stop-is-not-an-error.md) stop reasons versus errors, refusals as HTTP 200, and where the value appears in a stream.
- [02-anthropic-stop-reason.md](02-anthropic-stop-reason.md) the seven documented `stop_reason` values with Anthropic's own actions, plus `pause_turn`, `stop_details` and empty `end_turn`.
- [03-openai-chat-finish-reason.md](03-openai-chat-finish-reason.md) the five `finish_reason` values, why a refusal is not one of them, and one value per choice.
- [04-openai-responses-status.md](04-openai-responses-status.md) `status`, the four `incomplete_details.reason` values, and reasoning models that stop before any text.
- [05-gemini-finish-reason.md](05-gemini-finish-reason.md) every `finishReason` value with its quoted description, `blockReason` and `finishMessage`.
- [06-the-action-table.md](06-the-action-table.md) one table from action to value names across all four, and the three bugs it prevents.

## How to use it

- **Writing a wrapper over one provider.** Read that provider's chapter and switch on every value it lists, with a branch for values you do not know.
- **Writing a wrapper over several.** Start from chapter 06, and keep each provider's value in your logs next to your own normalised action.
- **Debugging an empty or cut-off answer.** Chapter 04 for an OpenAI reasoning model, chapter 05 for an empty Gemini candidates list, chapter 02 for `pause_turn` and empty `end_turn`.

## The stance

Read the stop signal before you read the text. Keep "what the provider documents" apart from "what my wrapper decides". In every table here, the provider column carries only quoted or closely paraphrased documentation. Anything labelled a house recommendation, house action or house reading is this repo's advice, not the provider's, and it is never put in the provider's mouth. No two values from different providers are called equivalent.

This is an independent project. It is unaffiliated with Anthropic, OpenAI or Google. Vendor names identify the APIs described; nothing here implies endorsement.

## Sources

Retrieved 2026-10-16.

- Anthropic, handling stop reasons: https://platform.claude.com/docs/en/build-with-claude/handling-stop-reasons
- OpenAI, Chat Completions create reference: https://developers.openai.com/api/reference/resources/chat/subresources/completions/methods/create
- OpenAI, Responses create reference: https://developers.openai.com/api/reference/resources/responses/methods/create
- OpenAI, reasoning guide: https://developers.openai.com/api/docs/guides/reasoning
- Google, Gemini API generateContent reference: https://ai.google.dev/api/generate-content

## Related

[llm-stream-inspector](https://github.com/0xelitesystem/llm-stream-inspector), [agent-failure-modes](https://github.com/0xelitesystem/agent-failure-modes), [structured-output-and-json-mode-reference](https://github.com/0xelitesystem/structured-output-and-json-mode-reference).

## License

MIT. Copyright 0xelitesystem 2026.
