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

## `name` is the plugin id, not "vinellu"

The `name` in `plugin.json` is `app-6ab13ddd61f88191ba7fb6c92a930f39`. It looks
like garbage and is not. It is the id of the plugin that already exists in the
portal, the same one in the dashboard URL (`plugin_asdk_app_6ab13…`) and in the
subject of the rejection email (`[asdk_app_6ab13…]`).

We learned this the expensive way. The package started with
`"name": "vinellu"`, the obvious kebab-case the docs suggest for a new plugin,
and the upload died in validation:

> Plugin name must match the existing plugin. Change `name` from "vinellu" to
> "app-6ab13ddd61f88191ba7fb6c92a930f39", then upload the ZIP again.

To **update** an existing plugin, `name` is the key that says which plugin is
being updated. Swapping it for something readable creates a new plugin at best
and breaks the upload at worst. The name people read is
`interface.displayName` (`Vinellu`), a different field that is free to change.

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

## Known mismatch: `category`

The docs **do not publish** the category list. They say "category title
required, from the dashboard" and give only `Productivity` and `Developer Tools`
as examples. The old schema enumerated them in upper case
(`…, FOOD, LIFESTYLE, PRODUCTIVITY, …`), and the two examples in the new docs
are the same list in Title Case (`PRODUCTIVITY` → `Productivity`,
`DEVELOPER_TOOLS` → `Developer Tools`). Hence `FOOD` → **`Food`**. If the
dashboard offers a different label for the same category, use the dashboard's.
It is the source, the derivation is inference.

The `agent-plugins.org` schemas do not validate this whole package either. The
1.0.0 `mcp.schema.json` sets `additionalProperties: false` on the
`streamable-http` server and accepts only `type`, `url` and `headers`. So the
`extensions."com.openai".auth` block that OpenAI's own docs ask for fails the
schema that `$schema` points to. A validation error on that field is expected.
Any other is not.

## Version

The rejected submission was `1.0.12`, and every new ZIP needs a new version.
Changed the package? Bump `version` in `plugin.json` in the same PR.
