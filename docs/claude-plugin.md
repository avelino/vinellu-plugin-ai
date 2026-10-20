# Claude package: design decisions

Why `claude-plugin/` looks the way it does. The editing rules are in
[AGENTS.md](../AGENTS.md).

## Plugin and connector are two submissions

The connector is the server. The plugin is the package that points at it and
carries the skills. Anthropic's docs ask for **both** to be submitted when a
plugin references its own server, with the **same URL**, so people who have
both see one set of tools instead of two.

| Artifact | Destination | What it is |
|---|---|---|
| `claude-plugin/` | Anthropic directory | this package: manifest, skills, server reference |
| `chatgpt-plugin/` | OpenAI portal | Agent Plugins 1.0.0 package, uploaded as a ZIP |
| MCP connector form | Anthropic directory | the server submission, filed separately |

## Why the package is a subfolder

The docs accept a package at the repo root or in a subfolder, and the form has a
**Plugin path** field for this. The value is `claude-plugin`, branch `main`.

A subfolder has one known cost. When the package is not at the root, validation
gets stricter about hooks, local MCP servers and scripts (shell variables,
command substitution, files calling files). This package has none of them, so
the cost never applies. If a hook ever lands here, the math changes and the
package may need its own repo.

## `name` is `vinellu`, forever

Unlike the ChatGPT package, where `name` is the opaque id of a plugin that
already existed in the portal, here the `name` was born with the package. It is
the permanent identity: people install by it, it prefixes every skill
(`vinellu:cuidar-da-adega`), and the docs say never to change it after release.
The name people read is `displayName`.

Constraints on `name`: lowercase, digits and hyphens, at most 64 characters,
the product name rather than a generic word, and no `claude`, `anthropic` or
`official` anywhere. That last one is a validation error.

## The server comes in through `.mcp.json`, with `type: "http"`

```json
{
  "mcpServers": {
    "vinellu": {
      "type": "http",
      "url": "https://vinellu.com/mcp"
    }
  }
}
```

Three things differ from the ChatGPT package on purpose:

1. **`type` is `http`, not `streamable-http`.** Anthropic accepts `http`, `sse`
   or `ws`. `streamable-http` is Agent Plugins 1.0.0 vocabulary, and copying it
   over fails validation.
2. **No OAuth declared.** The manifest has no field for it and does not need
   one. The `/mcp` 401 carries `WWW-Authenticate` with `resource_metadata`
   (RFC 9728), and all four `/.well-known/oauth-*` documents return 200,
   including dynamic client registration, which returns 201. The client
   discovers everything. A field left out cannot drift from the server.
3. **No `headers`.** The security scan looks for credentials in MCP headers and
   blocks the submission. There are no credentials in this package, in any
   file, not even as examples.

On claude.ai and in Cowork this block shows up as the plugin's **Connectors**
tab, where each person signs in. Claude Code connects directly.

## The skills are what the plugin adds

A plugin that only points at the server adds almost nothing. The connector
alone already delivers the 41 tools. What it does not deliver is the sequence,
the defaults and what a good answer looks like. There are five skills, and each
one exists because of a concrete mistake the model makes without it:

| Skill | Why it exists |
|---|---|
| `conectar-o-vinellu` | the account is a separate tab of the plugin, so people may install and never sign in. The skill says what works without an account, asks for one only when the question needs it, and turns the five permissions into sentences people understand. It is the same list as `PERMISSIONS` in `McpPage.tsx` |
| `escolher-um-vinho` | without it the answer is a raw dump of `buscar_vinho`. The skill closes the chain taste → label → where to buy, caps the answer at three options with one reason each, and forbids describing any label that did not come from the catalog |
| `cuidar-da-adega` | the only place where people can lose data. It pins the `preparar_*` / `confirmar_*` pair with the `resumo` shown in between, and fixes quantities through an adjustment instead of a consumption, which would log drinking that never happened |
| `registrar-o-que-bebi` | a check-in **publishes a post on the feed**, and no tool name says so. The skill forces a warning before `confirmar_checkin`, and separates the review (private, 1 to 5) from the check-in (public) |
| `ler-um-rotulo` | the image never goes through the tool. The model reads the photo and sends raw text. Without the skill it tries to send the image, or "fixes" the producer name and matches the wrong label. It is also the only surface with a daily quota, and the 429 must become a sentence in Portuguese |

The ChatGPT onboarding skill was the starting point for the first one, not the
ceiling. Two things changed: signing in now goes through the plugin's
connectors tab instead of the ChatGPT flow, and there is a new section on
friends, which the other package lacks and which is where writes that reach a
third person live.

Skills load on claude.ai, in Cowork and in Claude Code. Agents and hooks would
load only in Cowork and Claude Code, and there are none here.

## What stays out, and why

- **`bin/`**: a top-level `bin/` blocks installation on claude.ai and Cowork.
  Nothing here needs an executable.
- **Hooks, agents, commands, local MCP servers**: hooks and agents do not load
  in chat, and a hook in a subfolder package tightens validation (see above). A
  command loads as a skill in chat, so it would be the same thing with a worse
  name.
- **Screenshots**: the Anthropic manifest has no screenshot field. The listing
  reads `plugin.json` and the package README. The five ChatGPT PNGs would add
  three megabytes nobody reads.
- **Light and dark logo pair**: the `icon` field is single, with no dark
  variant. A transparent stroke SVG depends on the theme. The raster icon has
  its own background and reads the same in both. So the icon is the PNG, a
  byte-for-byte copy of the same 1024×1024 file the ChatGPT package uses. Its
  file name and path appear only in the manifest. The docs hold a version for
  manual review when a package image path shows up in backticks, a code block
  or a script.
- **Credentials of any kind**: the scan rejects them, and reviewer access goes
  through the dashboard form.
- **Repo docs inside the package**: anything in `claude-plugin/` ships with the
  plugin, and the docs say a `CLAUDE.md` at the package root does not load as
  context anyway. Editing rules live in the repo root [AGENTS.md](../AGENTS.md),
  and the package README holds only the listing copy.

## License

Validation **blocks** a package with no license, either a `LICENSE` file in the
folder or a `license` field in the manifest. The manifest declares
`Apache-2.0`, matching the repo [LICENSE](../LICENSE). It covers what is
actually distributed here, the manifests and the five skill texts. It does not
cover the catalog, the server or the brand (see [NOTICE](../NOTICE)).

## Version

The numbering is independent of the ChatGPT package. The docs ask for a bump on
every release, and the listing serves the new version only after it publishes.
