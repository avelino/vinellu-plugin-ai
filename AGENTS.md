# AGENTS.md

Rules for anyone editing this repo, human or agent. The reasoning behind each
rule lives in [docs/claude-plugin.md](./docs/claude-plugin.md),
[docs/chatgpt-plugin.md](./docs/chatgpt-plugin.md) and
[docs/cursor-plugin.md](./docs/cursor-plugin.md). JSON takes no comments, so
the "why" of every manifest field is written there.

## Layout

```
.claude-plugin/    marketplace.json, so Claude Code installs from this repo
.cursor-plugin/    marketplace.json, so the Cursor marketplace finds cursor-plugin/
claude-plugin/     Claude package (submitted with plugin path "claude-plugin", branch "main")
chatgpt-plugin/    ChatGPT package (Agent Plugins 1.0.0, uploaded as a ZIP)
cursor-plugin/     Cursor package (Cursor Plugin format, submitted as the repo link)
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
  (`vinellu:cuidar-da-adega`).
- User-facing copy has no emoji and no protocol jargon (MCP, OAuth, scope,
  token, endpoint, connector). The reader drinks wine and has never heard of
  MCP. Tool names are fine inside skills, because those lines are instructions
  for the model.

## Hard rules, every package

- **Zero credentials, in any file, even as an example.** Both platforms scan for
  them and reject the package. Reviewer access goes through each portal's form.
- **No `headers` on the MCP server, no hooks, no agents, no `bin/`, no app
  references.** Each one blocks submission on at least one platform.
- **Same server URL everywhere: `https://vinellu.com/mcp`.** Plugin and
  connector must share it, or people with both see the tools twice.
- **No `.DS_Store`, `Thumbs.db`, `desktop.ini` or `__MACOSX` inside a package.**
  Validation rejects them outright, and Finder creates the first one on its own.
- **Changed a package folder? Bump its `version` in the same PR.** The portals
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

- `name` is `vinellu` and must not change. It is the key the OpenAI portal uses
  to match an upload to its plugin entry. The old entry
  (`app-6ab13ddd61f88191ba7fb6c92a930f39`) is abandoned, see
  [docs/chatgpt-plugin.md](./docs/chatgpt-plugin.md).
- Every path inside the manifests starts with `./` and stays inside the folder.
- The ZIP is build output and is git-ignored.
- No skills. OpenAI review rejected `conectar-o-vinellu` as "not suitable for
  broad discovery". Adding a skill back needs a reason that answers that.

## Cursor package (`cursor-plugin/`)

- `name` is `vinellu`, same as the entry in `.cursor-plugin/marketplace.json`.
- `mcp.json` has `url` only. No `type`, no `headers`, no `variables`.
- Manifest paths are relative, with no `..` and no leading `/`.
- Skills are copies of `claude-plugin/skills`. Edit a skill in one package,
  edit it in the other in the same PR. The only allowed difference is the
  "Como ela liga a conta" block of `conectar-o-vinellu`.
- No rules. An `alwaysApply` rule would load in every chat of the editor.

## Values copied from elsewhere

These packages repeat values owned by the Vinellu app and server repo
(`avelino/vinellu.com`, private) and by the mobile app repo. When the source
changes, update the copy in the same PR.

| Here | Source |
|---|---|
| server `url` in both packages | `netlify.toml` + `netlify/edge-functions/mcp.ts` |
| `chatgpt-plugin/mcp.json` → `baseScopes` | `SCOPE_PISO` in `mcp-oauth.ts` |
| `brandColor` / `brandColorDark` | light and dark `--primary-500` of `PAGINA_CSS` in `mcp-oauth.ts` |
| permission list in `claude-plugin/skills/conectar-o-vinellu` | `PERMISSIONS` in `src/pages/McpPage.tsx` |
| `defaultPrompt` and README example questions | `QUESTIONS` in `src/pages/McpPage.tsx` |
| tool names cited in skills | the `TOOLS` registry in `netlify/edge-functions/mcp.ts` |
| `cursor-plugin/skills/*` | `claude-plugin/skills/*` (see the Cursor section) |
| `longDescription` | `app_info.description` of `chatgpt-app-submission.json` |
| `assets/*` | `vinellu-app/assets/` (byte-for-byte copies) |

New tool on the server does not mean new skill. Ask whether the model would get
the sequence wrong without instructions. If yes, the tool goes into the skill
that covers its subject. A **removed** tool must leave every skill that cites
it. A new permission scope changes the permission list, which appears in three
places that move together: the `conectar-o-vinellu` skill (in both the Claude and Cursor packages) and
`McpPage.tsx`.

## Verify before calling it done

```bash
claude plugin validate ./claude-plugin   # the package
claude plugin validate .                 # the marketplace
```

The validator checks format and structure only. The portals also check the
license, a README of at least 40 words, name collisions and a security scan.
For the ChatGPT package, check by hand that every `./` path in `plugin.json`
and `mcp.json` exists on disk.

The Cursor package has no CLI validator. Check that the JSON parses, that the
skills match, and load it locally:

```bash
jq . .cursor-plugin/marketplace.json cursor-plugin/.cursor-plugin/plugin.json cursor-plugin/mcp.json
diff -r claude-plugin/skills cursor-plugin/skills   # only conectar-o-vinellu may differ
ln -s "$PWD/cursor-plugin" ~/.cursor/plugins/local/vinellu   # then Developer: Reload Window
```
