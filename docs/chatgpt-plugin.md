# ChatGPT package: design decisions

Why `chatgpt-plugin/` looks the way it does. The editing rules are in
[AGENTS.md](../AGENTS.md).

The folder is the package the OpenAI submission portal accepts by upload, in
the **Agent Plugins 1.0.0** format (what OpenAI used to call "Apps SDK" became
"Plugins"). The ZIP is build output and is **not** versioned:

```bash
mkdir -p dist && (cd chatgpt-plugin && zip -r ../dist/vinellu-chatgpt-plugin.zip . -x '*.DS_Store')
```

Before uploading, check that every `./…` path cited in `plugin.json` and
`mcp.json` exists on disk.

## Why the package exists

A submission came back as "Changes required" with `MCP URL: Endpoint
unavailable`, `Authentication: Unavailable` and `Domain verification:
Unavailable`. The server was fine: all four `/.well-known/oauth-*` documents
return 200, the `/mcp` 401 carries `WWW-Authenticate` with `resource_metadata`
(RFC 9728), and `tools/list` returns the 41 tools. What was missing was **the
package**. In the new format the MCP configuration comes inside the ZIP, not
from a dashboard form.

`chatgpt-app-submission.json`, in the private server repo, is the **old
format** (`chatgpt-app-submission.v1.json`). It is not retired. It is still
the source of the per-tool annotation justifications and the test cases. This
package read it once (`longDescription` came from its `app_info.description`)
and does not replace it.

## `name` is `vinellu`, a fresh submission

The first submission lived under the portal id
`app-6ab13ddd61f88191ba7fb6c92a930f39`, and the package used that id as `name`,
because to **update** an existing plugin `name` is the key that says which one.
Uploading `"name": "vinellu"` to that entry failed with:

> Plugin name must match the existing plugin. Change `name` from "vinellu" to
> "app-6ab13ddd61f88191ba7fb6c92a930f39", then upload the ZIP again.

That entry got stuck after its rejection. Every upload, with the old id or not,
was blocked by "Keep the existing MCP connection. Configure the existing MCP app
before updating its plugin ZIP", the MCP tab showed "Unavailable" and rescan was
disabled. OpenAI support acknowledged it as a known issue in the update flow
with no workaround
([forum thread](https://community.openai.com/t/rejected-plugin-cannot-be-updated-mcp-unavailable-and-zip-upload-blocked/1405612)).

So the package was resubmitted from scratch as a new plugin: `name` is
`vinellu` and the version restarted at `1.0.0`. From now on `name` is the
permanent key of this new entry and must not change. The name people read is
`interface.displayName` (`Vinellu`), a separate field.

## No skills

The first fresh upload carried the `conectar-o-vinellu` onboarding skill,
copied from the Claude package. The skill scan rejected it as blocking:

> conectar-o-vinellu: The content or focus of your skill is not suitable for
> broad discovery and cannot be approved as submitted.

OpenAI does not say what "broad discovery" means for a skill. The skill only
explained the service and how to sign in, which is what the listing and the
server instructions already do, and the old `1.0.12` package shipped no skill
at all. So it was dropped in `1.0.2`. The server alone delivers the 41 tools.

## OAuth endpoints: discovered, not declared

`mcp.json` declares `client.mode: "dcr"` and **no** OAuth URLs. This is on
purpose:

1. **Discovery works in production.** `/.well-known/oauth-authorization-server`
   (RFC 8414) serves `authorization_endpoint`, `token_endpoint` and
   `registration_endpoint`, and the `/mcp` 401 points at
   `/.well-known/oauth-protected-resource/mcp` (RFC 9728). The docs say to add
   `authorizationUrl`/`tokenUrl` **if** discovery does not provide them. It
   does.
2. **Declaring them creates a second copy that goes stale.** The endpoints
   already live on the server. Repeated here, any change there would need a new
   ZIP and a new review to stay in sync, and the docs warn that **changing the
   URL of an already connected MCP requires a support ticket**. A field left
   out cannot drift.
3. **DCR takes no credentials.** With `mode: "dcr"` there is no `clientId` and
   no secret. `/oauth/register` returns 201 in production.

## Scopes: `baseScopes` is the floor, nothing more

`baseScopes: ["leitura"]` mirrors `SCOPE_PISO` on the server. `leitura` (read)
is the floor, not an extra scope. The server refuses any scope set without it,
because a token that can write without reading is how a bottle gets duplicated
in a cellar. `baseScopes` goes on every request, so the package guarantees the
floor instead of trusting the client.

`defaultScopes` is left **out** on purpose. Without it the runtime uses the
discovered `scopes_supported`, all five, and the person approves them one by one
on the consent screen inside the app, which lists each permission in
Portuguese. Setting `defaultScopes: ["leitura"]` would look more conservative
and break the app: the server's refresh **only narrows** (RFC 6749 §6), so a
connection born with `leitura` alone never reaches the 24 write tools without a
whole new authorization. The guideline asks for permissions limited to what is
needed. All five are needed, and each is declared before use.

## Assets

| File | Source (mobile app repo, read only) | Size |
|---|---|---|
| `assets/icon.png` | `assets/icon.png` | 1024×1024 |
| `assets/logo.svg` | `assets/mini-logo-wine.svg` | 720×720 (square `viewBox`) |
| `assets/logo-dark.svg` | `assets/mini-logo-white.svg` | 720×720 |
| `assets/screenshot-1..5.png` | `assets/store/appstore_1..5.png` | 1284×2778 |

Nothing was generated. Every file is a byte-for-byte copy. The docs require a
**square** icon and logo, at least 48×48, raster at most 4096 per side, in PNG,
JPEG, WebP or SVG up to 5 MiB. All three pass.

There is no `composerIconDark` because `icon.png` is a full-bleed raster with
its own background and reads the same on white and on `#212121`. The
`logo.svg`/`logo-dark.svg` pair exists because those two are transparent
strokes, where the theme does matter. The dark one is the `mini-logo-white.svg`
the app already had, not a file made up to fill a field.

`brandColor: #5b1e31` is the light `--primary-500` of the server's consent page
CSS, at 12.5:1 against white. `brandColorDark: #b85a65` is the dark
`--primary-500` of the **same** CSS, at 3.6:1 against `#212121`. The docs check
both contrasts in separate fields and derive a dark color on their own if only
the light one is set. A derived color is worse than the one the product already
uses in dark mode.

## What stays out, and why

- **`apps` / `.app.json`**: the docs reject packages that reference an app.
- **`hooks`**: the docs reject packages with lifecycle hooks.
- **`test_credentials`, `reviewer_instructions`, any credential**: rejected in
  the metadata. Reviewer access goes through the **Review details** form in the
  dashboard, not through the ZIP.
- **`headers` on the server**: the docs reject non-empty `headers`.
- **Repo docs inside the package**: everything in the folder goes into the ZIP.

## What the ZIP does not solve

Outside the package, for the submission to close:

- **Domain verification**: OpenAI issues a fresh token, and it must be served
  raw at `https://vinellu.com/.well-known/openai-apps-challenge`. The file and
  header already exist on the server (`text/plain`, `no-store`). Swap in the
  current challenge.
- **Review details**, in the dashboard: reviewer credentials, 5 positive cases,
  3 negative ones, the video URL and release notes. The test cases come from
  `chatgpt-app-submission.json`.
- **Verified identity** for the organization. Without it the package uploads
  but cannot be submitted.

## `category` is `Entertainment`

The category list is closed. It is published under `plugin_category_unknown` in
OpenAI's [submission errors](https://developers.openai.com/plugins/deploy/submission-errors)
reference: `Productivity`, `Creativity`, `Developer Tools`,
`Business & Operations`, `Data & Analytics`, `Communication`,
`Education & Research`, `Security`, `Finance`, `Healthcare`, `Travel`,
`Entertainment` or `Other`.

The first version guessed `Food`, derived from the old upper-case schema
(`FOOD`), and the portal rejected it with "Select a valid category". There is
no food, drink or lifestyle entry. `Entertainment` is the closest fit for wine
discovery with a social layer. `Other` is the fallback if review pushes back.

## Schema mismatch

The `agent-plugins.org` schemas do not validate this whole package either. The
1.0.0 `mcp.schema.json` sets `additionalProperties: false` on the
`streamable-http` server and accepts only `type`, `url` and `headers`. So the
`extensions."com.openai".auth` block that OpenAI's own docs ask for fails the
schema that `$schema` points to. A validation error on that field is expected.
Any other is not.

## Version

The fresh submission starts at `1.0.0`, and every new ZIP needs a new version.
Changed the package? Bump `version` in `plugin.json` in the same PR.
