# Outrun agent skill

[![skills.sh](https://skills.sh/b/OutrunAIAPI/outrun-skill)](https://skills.sh/OutrunAIAPI/outrun-skill)

An [Agent Skill](https://www.skills.sh/) that teaches coding agents how to use [Outrun](https://outrun.to/?utm_source=github&utm_medium=repo&utm_campaign=skill), a prepaid, pay-as-you-go AI API. With it, your agent can set up Claude Code, Codex, OpenCode, Gemini CLI or an OpenAI-compatible SDK against Outrun. It picks models and prices from the live price list instead of guessing, and it keeps your API key out of the chat.

## Install

```bash
npx skills add OutrunAIAPI/outrun-skill
```

Check what the CLI finds without installing:

```bash
npx skills add OutrunAIAPI/outrun-skill --list
```

### Claude Code plugin

The repo is also a Claude Code plugin marketplace. Inside Claude Code:

```text
/plugin marketplace add OutrunAIAPI/outrun-skill
/plugin install outrun@outrun-ai
```

On Claude Code 2.1.275 or later, one line does both:

```text
/plugin install outrun --marketplace OutrunAIAPI/outrun-skill
```

The plugin adds the skill and a `/outrun:setup` command that walks you through connecting a tool.

### Gemini CLI extension

```bash
gemini extensions install https://github.com/OutrunAIAPI/outrun-skill
```

## What the skill covers

- Getting a key and adding balance (card, crypto or a prepaid code; no subscription)
- Base URLs for the OpenAI-compatible, Anthropic and Gemini formats
- Reading live models and per-model prices from `https://api.outrun.to/pricing`
- Copy-paste setup for Claude Code, Codex, OpenCode and Gemini CLI
- A minimal curl and Python example
- Common errors and when not to retry a paid request

The skill itself is in [`skills/outrun/SKILL.md`](skills/outrun/SKILL.md). It is plain Markdown with no scripts.

## Links

- Website: https://outrun.to/?utm_source=github&utm_medium=repo&utm_campaign=skill
- Docs: https://outrun.to/docs/?utm_source=github&utm_medium=repo&utm_campaign=skill
- Live models and prices: https://api.outrun.to/pricing
- Code examples and config files: https://github.com/OutrunAIAPI/outrun-examples

## License

MIT
