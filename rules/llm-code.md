# AI / LLM / ML Code

Applies only to code that calls LLM APIs, builds agents, or trains/fine-tunes models. Ignore otherwise.

LLM output is non-deterministic and real calls cost money. Verify without burning budget.

## Cost and runtime

- Before calling a paid API (OpenAI, Anthropic, Gemini, etc.) or starting GPU training, fine-tuning, or any job over ~5 minutes, ask first and state the expected number of calls or runtime.
- ML experiments: fix random seeds and run on a small data subset before any full run.

## Testing

- Mock the LLM client in tests. Use recorded or fixed fake responses, never live calls.
- Test properties, not exact text: output parses into the expected schema (e.g. Pydantic), the agent calls the right tool with valid arguments, retries and error paths work, loops terminate.
- Never hardcode a model response to make an agent test pass. If a behavior only works with one specific response, the logic is wrong.

## Structure

- Prompts, model names, temperatures, and token limits live in one config place, not inline across files.
- API keys come only from environment/config. Never write them into code, logs, or test fixtures.
