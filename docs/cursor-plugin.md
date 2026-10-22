# Cursor package: design decisions

Why `cursor-plugin/` looks the way it does. The editing rules are in
[AGENTS.md](../AGENTS.md). The spec is the
[Cursor plugins reference](https://cursor.com/docs/reference/plugins.md).

## Cursor Plugin format, not Agent Plugins

Cursor loads two formats: Agent Plugins (root `plugin.json`, the same standard
the ChatGPT package uses) and its own Cursor Plugin format
(`.cursor-plugin/plugin.json`). Both carry skills and an MCP server, which is
all this package needs. The Cursor format wins on two points:

- it takes a `logo` field and a `.cursor-plugin/marketplace.json`, which is how
  the marketplace finds a package that lives in a subfolder
- its `mcp.json` infers the transport from `url`, so there is no `type` value
  to get wrong (`http` for Claude, `streamable-http` for Agent Plugins)

## Found through `.cursor-plugin/marketplace.json`

Submission is a link to a public Git repo. The package is not at the repo root,
so the root `.cursor-plugin/marketplace.json` lists it with
`"source": "cursor-plugin"`. The parser then reads
`cursor-plugin/.cursor-plugin/plugin.json` and runs folder discovery inside
`cursor-plugin/`. Manifest values win over the marketplace entry when both set
the same field.

The Claude marketplace lives in `.claude-plugin/marketplace.json`. The two
files sit side by side and do not read each other.

## `name` is `vinellu`

Same rule as the Claude package: lowercase kebab-case, the product name, and it
is what people install. Changing it after release creates a new listing.

## The server comes in through `mcp.json`, with no `type`

```json
{
  "mcpServers": {
    "vinellu": {
      "url": "https://vinellu.com/mcp"
    }
  }
}
```

- **No `type`.** Cursor infers remote transport from `url`.
- **No `headers`, no `variables`.** Variables exist for tokens an admin types in
  the dashboard. Vinellu signs in through OAuth discovery on the server (the
  401 carries `WWW-Authenticate` with `resource_metadata`), so there is nothing
  to declare and nothing that could leak.
- **Same URL as the other packages**, so people with more than one install see
  one set of tools.

## Skills are copies of the Claude ones

The checklist forbids `..` in manifest paths, so `skills` cannot point at
`../claude-plugin/skills`. A symlink might work, but the reviewer pulls the
repo and nothing in the docs says the parser follows links. Copies are the
version that certainly loads.

Four of the five skills are identical to the Claude package. Only
`conectar-o-vinellu` differs: in Claude the account lives on the plugin's
connectors tab, in Cursor it lives in Cursor settings, on the tool list, where
the server asks to sign in. Everything else (permissions, two-step writes,
friends) is the same text.

Check drift with:

```bash
diff -r claude-plugin/skills cursor-plugin/skills
```

The only expected difference is the "Como ela liga a conta" block of
`conectar-o-vinellu`.

## Logo is the raster icon

`logo` resolves to a `raw.githubusercontent.com` URL at the submitted commit.
The PNG has its own background and reads the same in light and dark themes,
which the stroke SVG does not. Byte-for-byte copy of the icon the other
packages use.

## What stays out

- **Rules**: an `alwaysApply` rule would load in every chat of the person's
  editor, wine question or not. Skills load only when the description matches.
- **Hooks, agents, commands, scripts**: nothing here runs code. A hook would
  also need review for every script it calls.
- **Variables**: see above.

## Version

Independent numbering, starting at `1.0.0`. Every update is reviewed before it
publishes, so bump it in the same PR that changes the folder.
