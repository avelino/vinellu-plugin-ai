# AGENTS.md

Rules for anyone editing this repo, human or agent. The reasoning behind each
rule lives in [docs/claude-plugin.md](./docs/claude-plugin.md) and
[docs/chatgpt-plugin.md](./docs/chatgpt-plugin.md). JSON takes no comments, so
the "why" of every manifest field is written there.

## Layout

```
.claude-plugin/    marketplace.json, so Claude Code installs from this repo
claude-plugin/     Claude package (submitted with plugin path "claude-plugin", branch "main")
chatgpt-plugin/    ChatGPT package (Agent Plugins 1.0.0, uploaded as a ZIP)
docs/              design decisions for each package
```

Only listing copy and skills go inside a package folder. Repo docs, editor
rules and notes stay outside, because whatever sits in a package folder ships
with it.

## Language

- Repo docs (this file, `README.md`, `docs/`): English.
- Anything that reaches the end user: Brazilian Portuguese. That covers manifest
  descriptions, `defaultPrompt`, `claude-plugin/README.md` (it is the listing
  page) and every `SKILL.md`. Vinellu is launching in Brazil.
- Skill folder names stay in Portuguese. They are part of the skill identity
  (`vinellu:cuidar-da-adega`) and the ChatGPT `onboardingSkill` path.
- User-facing copy has no emoji and no protocol jargon (MCP, OAuth, scope,
  token, endpoint, connector). The reader drinks wine and has never heard of
  MCP. Tool names are fine inside skills, because those lines are instructions
  for the model.

## Hard rules, both packages

- **Zero credentials, in any file, even as an example.** Both platforms scan for
  them and reject the package. Reviewer access goes through each portal's form.
- **No `headers` on the MCP server, no hooks, no agents, no `bin/`, no app
  references.** Each one blocks submission on at least one platform.
- **Same server URL everywhere: `https://vinellu.com/mcp`.** Plugin and
  connector must share it, or people with both see the tools twice.
- **No `.DS_Store`, `Thumbs.db`, `desktop.ini` or `__MACOSX` inside a package.**
  Validation rejects them outright, and Finder creates the first one on its own.
- **Changed a package folder? Bump its `version` in the same PR.** Both portals
  refuse a repeated version.

## Claude package (`claude-plugin/`)

- `name` is `vinellu` and never changes after release. It prefixes every skill
  and is what people install. The readable name is `displayName`. `claude`,
  `anthropic` and `official` are forbidden anywhere in `name`.
- Server `type` is `http`. `streamable-http` is the ChatGPT vocabulary and fails
  here.
- Image paths from the package appear only in the manifest. A path inside
  backticks, a code block or a script holds the version for manual review. To
  show an image in the README, use Markdown image syntax.
- Non-image, non-font files stay under 256 KiB. Nothing goes over 5 MiB.

## ChatGPT package (`chatgpt-plugin/`)

- `name` is `app-6ab13ddd61f88191ba7fb6c92a930f39` and must not change. It is
  the id of the plugin that already exists in the OpenAI portal. Replacing it
  with something readable already broke an upload once.
- Every path inside the manifests starts with `./` and stays inside the folder.
- The ZIP is build output and is git-ignored.

## Values copied from elsewhere

These packages repeat values owned by the Vinellu app and server repo
(`avelino/vinellu.com`, private) and by the mobile app repo. When the source
changes, update the copy in the same PR.

| Here | Source |
|---|---|
| server `url` in both packages | `netlify.toml` + `netlify/edge-functions/mcp.ts` |
| `chatgpt-plugin/mcp.json` → `baseScopes` | `SCOPE_PISO` in `mcp-oauth.ts` |
| `brandColor` / `brandColorDark` | light and dark `--primary-500` of `PAGINA_CSS` in `mcp-oauth.ts` |
| permission list in `conectar-o-vinellu` (both packages) | `PERMISSIONS` in `src/pages/McpPage.tsx` |
| `defaultPrompt` and README example questions | `QUESTIONS` in `src/pages/McpPage.tsx` |
| tool names cited in skills | the `TOOLS` registry in `netlify/edge-functions/mcp.ts` |
| `longDescription` | `app_info.description` of `chatgpt-app-submission.json` |
| `assets/*` | `vinellu-app/assets/` (byte-for-byte copies) |

New tool on the server does not mean new skill. Ask whether the model would get
the sequence wrong without instructions. If yes, the tool goes into the skill
that covers its subject. A **removed** tool must leave every skill that cites
it. A new permission scope changes the permission list, which appears in three
places that move together: both `conectar-o-vinellu` skills and `McpPage.tsx`.

## Verify before calling it done

```bash
claude plugin validate ./claude-plugin   # the package
claude plugin validate .                 # the marketplace
```

The validator checks format and structure only. The portals also check the
license, a README of at least 40 words, name collisions and a security scan.
For the ChatGPT package, check by hand that every `./` path in `plugin.json`
and `mcp.json` exists on disk.
