# Vinellu AI plugins

The plugin packages that bring [Vinellu](https://vinellu.com), the wine social
network, into AI assistants. Each package points at the same remote server,
`https://vinellu.com/mcp`, and ships the skills that teach the model how to use
it well.

The server exposes a catalog of 174 thousand wine labels with full tasting
sheets, food pairing and where to buy. People who sign in with their Vinellu
account also get their cellar, reviews, wishlist, taste profile and friends.

Vinellu is launching in Brazil first, so everything the end user reads
(listing copy, skill text, example prompts) is in Brazilian Portuguese. The repo
itself is documented in English.

## Packages

| Folder | Target | Format |
|---|---|---|
| [`claude-plugin/`](./claude-plugin) | Claude (claude.ai, Cowork, Claude Code) | Claude plugin: `.claude-plugin/plugin.json`, `.mcp.json`, skills |
| [`chatgpt-plugin/`](./chatgpt-plugin) | ChatGPT | Agent Plugins 1.0.0: `plugin.json`, `mcp.json`, assets (no skills) |

Neither package holds code or credentials. They are manifests, Markdown skills
and images. All behavior lives on the server.

## Skills

| Skill | What it does |
|---|---|
| `conectar-o-vinellu` | onboarding: what works without an account, when to ask for one, what each permission means |
| `escolher-um-vinho` | picks a bottle from taste or a dish, caps the answer at three options, ends at where to buy |
| `cuidar-da-adega` | cellar reads and writes, always through the two-step prepare/confirm pair |
| `registrar-o-que-bebi` | reviews (private) vs check-ins (public feed post), and warns before posting |
| `ler-um-rotulo` | the model reads the photo and sends raw text, never the image, and handles the daily quota |

The Claude package ships all five. The ChatGPT package ships none, because
OpenAI review rejected the onboarding skill (see
[docs/chatgpt-plugin.md](./docs/chatgpt-plugin.md)).

## Install

**Claude Code**, from this repo's marketplace:

```
/plugin install vinellu --marketplace avelino/vinellu-plugin-ai
```

Or from a local clone, for development:

```bash
claude --plugin-dir ./claude-plugin
```

**claude.ai and ChatGPT**: install from each platform's plugin directory once
the listing is live. Account sign-in happens on vinellu.com with a one-time
code approved in the Vinellu mobile app. Passwords never reach the assistant.

## Docs

- [AGENTS.md](./AGENTS.md): rules for anyone (human or agent) editing this repo
- [docs/claude-plugin.md](./docs/claude-plugin.md): why the Claude package looks the way it does
- [docs/chatgpt-plugin.md](./docs/chatgpt-plugin.md): why the ChatGPT package looks the way it does

## Support and policies

- Support: https://vinellu.com/suporte
- Privacy: https://vinellu.com/privacidade
- Terms: https://vinellu.com/termos

## License

The manifests and skill texts are under the [Apache License 2.0](./LICENSE).
The Vinellu name, logo, icon and screenshots are trademarks of Vinellu and are
not covered by that license. See [NOTICE](./NOTICE).
