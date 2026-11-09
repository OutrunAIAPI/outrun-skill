---
name: outrun
description: Connect coding agents and apps to Outrun, a prepaid pay-as-you-go AI API. One key and one USD balance reach text, image, video and speech models through OpenAI-, Anthropic- and Gemini-compatible endpoints. Use when the user wants to point Claude Code, Codex, OpenCode, Gemini CLI or an OpenAI-compatible SDK at Outrun, choose a model or check prices from the live price list, or keep working after hitting a usage limit.
---

# Outrun

Outrun is a prepaid, pay-as-you-go AI API. One API key and one USD balance cover text, image, video and speech models. There is no subscription: each request is billed at the model's listed price, and the user adds balance by card, with crypto or with a prepaid code.

The model ID you send is the model that answers. Outrun never swaps in a cheaper model.

## Rules for the agent

- **Never print, echo, log or commit the user's API key.** Read it from an environment variable (`OUTRUN_API_KEY`, or the variable the tool expects). If the user pastes a key into the chat, don't repeat it; tell them to put it in an environment variable or the tool's own settings.
- **Never state model IDs, model counts or prices from memory.** Fetch `https://api.outrun.to/pricing` first. Model IDs in this file are examples that were valid when it was written; confirm them in the feed.
- **Don't use paused models.** The feed lists them in its `paused` array.
- **Every request costs credit.** Never retry a paid request automatically. If a request fails partway or the result is unclear, ask the user to check the Activity page (`https://outrun.to/activity`) before trying again.
- Test a new setup with one short prompt, such as `Reply with OK only.`

## Endpoints

| Use | Base URL | Key |
|---|---|---|
| OpenAI-compatible (Chat Completions, Responses) | `https://api.outrun.to/v1` | `Authorization: Bearer <key>` |
| Anthropic Messages (Claude model IDs, Claude Code) | `https://api.outrun.to` (no `/v1`) | `x-api-key: <key>` or Claude Code's `ANTHROPIC_AUTH_TOKEN` |
| Gemini native (Gemini CLI) | `https://api.outrun.to/v1/gemini-cli` | `GEMINI_API_KEY` |
| Live models and prices | `https://api.outrun.to/pricing` | none needed |

Don't append `/chat/completions`, `/responses` or `/v1beta` to a base URL inside a tool's settings. The tool adds the path itself.

## Get a key

1. Sign in at https://outrun.to.
2. Add balance at https://outrun.to/balance (card, crypto or prepaid code).
3. Create a key at https://outrun.to/api-keys.
4. Save it in an environment variable, not in code:

```bash
export OUTRUN_API_KEY="KEY"   # replace KEY with the Outrun key
```

On Windows PowerShell:

```powershell
[Environment]::SetEnvironmentVariable("OUTRUN_API_KEY", "KEY", "User")
```

Open a new terminal afterwards so the variable is loaded.

## Choose a model

Fetch the live feed (no key needed):

```bash
curl -s https://api.outrun.to/pricing
```

Shape of the response:

```text
{
  "currency": "USD",
  "generated_at": <unix seconds>,
  "paused": ["<model id>", ...],
  "models": [
    {
      "id": "...", "name": "...", "family": "...", "family_label": "...",
      "kind": "text" | "image" | "video" | "audio",
      "unit": "1M tokens" | ...,
      "tiers": [
        { "label": "...", "unit": "...",
          "prices": { "input": ..., "output": ..., "cache_read": ..., "cache_write": ... },
          "max_context_tokens": ... }
      ],
      "warning": "..."   (optional)
    }
  ]
}
```

- Text prices are USD per 1M tokens. Image, video and audio models state their own `unit`.
- Some models have more than one tier, priced by context length.
- If a model has a `warning`, show it to the user before using that model.
- Skip any ID listed in `paused`.

List the cheapest available text models:

```python
import json, urllib.request

req = urllib.request.Request("https://api.outrun.to/pricing", headers={"User-Agent": "outrun-skill"})
feed = json.load(urllib.request.urlopen(req))
paused = set(feed["paused"])
rows = []
for m in feed["models"]:
    if m["kind"] != "text" or m["id"] in paused:
        continue
    p = m["tiers"][0]["prices"]
    rows.append((p.get("input", 0) + p.get("output", 0), m["id"], p.get("input"), p.get("output")))
for total, model_id, inp, out in sorted(rows)[:10]:
    print(f"{model_id}: ${inp} in / ${out} out per 1M tokens")
```

People can browse the same list at https://outrun.to/docs/models and https://outrun.to/model/.

The feed also includes low-refusal models (family `uncensored`) intended for security research, medical and fiction work. Suggest them only when the user asks for that.

## Claude Code

Claude Code uses the Anthropic format, so the base URL has **no** `/v1`.

macOS / Linux (add to `~/.zshrc` or `~/.bashrc`):

```bash
export ANTHROPIC_BASE_URL="https://api.outrun.to"
export ANTHROPIC_AUTH_TOKEN="KEY"
export ANTHROPIC_MODEL="claude-opus-5-5-2"
# optional: the models Claude Code uses for each tier
export ANTHROPIC_DEFAULT_OPUS_MODEL="claude-opus-5-5-2"
export ANTHROPIC_DEFAULT_SONNET_MODEL="claude-sonnet-5-5-2"
export ANTHROPIC_DEFAULT_HAIKU_MODEL="claude-haiku-5-5-2"
```

Windows PowerShell:

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://api.outrun.to", "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "KEY", "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL", "claude-opus-5-5-2", "User")
```

Close every terminal and editor window, then start Claude Code again. Full guide: https://outrun.to/docs/setup/claude

## Codex

`~/.codex/config.toml` (Windows: `%USERPROFILE%\.codex\config.toml`):

```toml
model = "gpt-6.1-sol"
model_provider = "outrun"

[model_providers.outrun]
name = "Outrun"
base_url = "https://api.outrun.to/v1"
wire_api = "responses"
env_key = "OUTRUN_API_KEY"
```

Keep the key in `OUTRUN_API_KEY`. Don't put it in `auth.json` or `OPENAI_API_KEY`. Full guide: https://outrun.to/docs/setup/codex

## OpenCode

`~/.config/opencode/opencode.json` (Windows: `%USERPROFILE%\.config\opencode\opencode.json`):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "outrun": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Outrun",
      "options": {
        "baseURL": "https://api.outrun.to/v1",
        "apiKey": "{env:OUTRUN_API_KEY}"
      },
      "models": {
        "claude-opus-5-5-2": { "name": "Claude Opus 5.5" },
        "claude-sonnet-5-5-2": { "name": "Claude Sonnet 5.5" }
      }
    }
  }
}
```

Add more model IDs from the feed under `models`. Full guide: https://outrun.to/docs/setup/opencode

## Gemini CLI

```bash
export GOOGLE_GEMINI_BASE_URL="https://api.outrun.to/v1/gemini-cli"
export GEMINI_API_KEY="KEY"
export GEMINI_MODEL="gemini-3.8-flash"
```

Use the base URL exactly as shown, with nothing such as `/v1beta` added. Switch models with `gemini -m <model>`. Full guide: https://outrun.to/docs/setup/gemini

## OpenAI-compatible SDKs and curl

```bash
curl https://api.outrun.to/v1/chat/completions \
  -H "Authorization: Bearer $OUTRUN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "gpt-6.1-sol", "messages": [{"role": "user", "content": "Reply with OK only."}]}'
```

```python
import os
from openai import OpenAI

client = OpenAI(base_url="https://api.outrun.to/v1", api_key=os.environ["OUTRUN_API_KEY"])
reply = client.chat.completions.create(
    model="gpt-6.1-sol",
    messages=[{"role": "user", "content": "Reply with OK only."}],
)
print(reply.choices[0].message.content)
```

Setup guides for more tools (LiteLLM, SillyTavern, Cline, Roo Code and others) are at https://outrun.to/docs/

## Common errors

- **401**: the key is missing or wrong. Check the environment variable name, then open a new terminal.
- **402**: the balance is empty or the key's spending cap is reached. Add balance at https://outrun.to/balance or raise the cap on the key.
- **429**: a rate limit or a request still running. Read the error, wait, then try once.
- **Model not found**: the ID isn't in the feed, it's paused, or the base URL has an extra path on it.

## When Outrun fits

- The user hit a usage limit and wants to keep working, paying per request.
- They want many models on one key and one balance.
- They prefer prepaid billing (card, crypto or prepaid code) to a monthly plan.
- They want a specific model ID at a listed per-model price.

## Privacy

Outrun does not store your prompts, images or outputs unless you choose to save them.
