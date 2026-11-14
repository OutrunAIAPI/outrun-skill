---
description: Connect a coding tool or SDK to Outrun, using models and prices from the live feed
argument-hint: "[tool: claude-code | codex | opencode | gemini-cli | sdk]"
---

Help the user connect a tool to Outrun, a prepaid pay-as-you-go AI API. Use the `outrun` skill in this plugin for the endpoints, config snippets and rules.

Tool the user named: $ARGUMENTS

1. If no tool is named above, ask which one: Claude Code, Codex, OpenCode, Gemini CLI, or an OpenAI-compatible SDK or curl. Ask for their operating system if the config depends on it.
2. Fetch `https://api.outrun.to/pricing` (no key needed). Take model IDs only from that feed and skip any ID in its `paused` array. Never state model IDs, model counts or prices from memory.
3. Show the exact config for that tool, with the key read from `OUTRUN_API_KEY` or the variable the tool expects. Write the placeholder `KEY` wherever the user must put their key.
4. Never ask the user to paste their API key into the chat, and never print, echo, log or commit a key. If they paste one anyway, don't repeat it; tell them to put it in an environment variable or the tool's own settings.
5. If they have no key yet: sign in at https://outrun.to, add balance at https://outrun.to/balance, then create a key at https://outrun.to/api-keys.
6. Suggest one short test prompt, such as `Reply with OK only.` Every request costs credit: don't send paid requests unless the user asks, and never retry a paid request automatically.
