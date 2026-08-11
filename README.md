# @happyvertical/renovate-config

Shareable [Renovate](https://docs.renovatebot.com/) configuration for the HappyVertical organization.

## Presets

### Organization Presets

| Preset | Description | Use Case |
|--------|-------------|----------|
| `default` | Base configuration with common settings | Extended by all other presets |
| `org-standards` | Org-standard toolchain versions (Node, pnpm) | Extended by `default`; applies everywhere |
| `monorepo` | Monorepo settings (handles `workspace:*`) | SDK, SMRT |
| `pnpm` | pnpm-specific configuration | Most repos |
| `bun` | Bun-specific configuration | IAC repo |
| `sdk` | SDK (foundation layer) | SDK monorepo |
| `smrt` | SMRT (framework layer) | SMRT monorepo |
| `application` | Application layer | praeco, caelus, aedile |
| `sites` | Web sites | happyvertical.com, bangblow.com |
| `client` | External organizations | Client orgs using @happyvertical/* |

### Dependency Labeling Presets

These presets add targeted labels without splitting the weekly organization PR.

| Preset | Packages Labeled |
|--------|------------------|
| `groups/ai-sdks` | OpenAI, Anthropic, Google GenAI, LangChain, MCP |
| `groups/typescript-tools` | TypeScript, Vite, Vitest, Biome, Turbo, Changesets |
| `groups/aws-sdks` | @aws-sdk/* packages |
| `groups/database` | LibSQL, PostgreSQL, better-sqlite3, DuckDB |
| `groups/svelte` | Svelte, SvelteKit, Svelte plugins |

### Org-standard toolchain versions

`org-standards.json` is the single knob for org-wide toolchain versions.
Each rule caps a tool with `allowedVersions`; every repo converges on the cap
and holds there. To roll out a new standard (e.g. a new Node or pnpm version),
bump the cap in `org-standards.json` — Renovate opens the bump PR in every
repo on its next run, and each repo's own CI validates it.

Current standards:

| Tool | Version | Strategy | Covers |
|------|---------|----------|--------|
| Node | 24.18.0 | bump ranges | `engines.node`, `.nvmrc`, `node` Docker images, `node-version:` pins in workflows and composite actions |
| pnpm | 11.13.0 | bump ranges | `packageManager`, `engines.pnpm` |
| TypeScript | 5.9.3 | pin exact | blocks TS 6/7 this wave |
| Vitest | 4.1.10 | pin exact | `vitest` and `@vitest/*` in lockstep |
| Vite | 8.1.4 | pin exact | |
| SvelteKit | 2.69.3 | pin exact | |
| Biome | 2.5.3 | pin exact | |
| Turbo | 2.10.5 | pin exact | |
| Lefthook | 2.1.10 | pin exact | |

The blanket `rangeStrategy: bump` rules in `pnpm.json` and `application.json`
exclude the pinned tools so the pins are not overridden (later-resolving
package rules win in Renovate).

Caveats: Renovate never downgrades (repos already above a cap — e.g. on
TypeScript 6 — must be brought back manually), it cannot add a missing pin
(repos without a `packageManager` field need it added once by hand), and the
`client` preset does not extend `default`, so repos on it do not inherit these
standards.

## Usage

### For HappyVertical Repos

Add a `renovate.json` to your repository:

**SDK:**
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>happyvertical/renovate-config:sdk"]
}
```

**SMRT:**
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>happyvertical/renovate-config:smrt"]
}
```

**Applications (praeco, caelus, aedile):**
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>happyvertical/renovate-config:application"]
}
```

**Sites (happyvertical.com, bangblow.com):**
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>happyvertical/renovate-config:sites"]
}
```

**IAC:**
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>happyvertical/renovate-config",
    "local>happyvertical/renovate-config:bun"
  ]
}
```

### For External Organizations (Clients)

If you're using `@happyvertical/*` packages in your project, you can use our client preset:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>happyvertical/renovate-config:client"]
}
```

This preset will:
- Create one weekly dependency update PR on Saturday night
- Label `@happyvertical/*` package updates
- Require review before merging updates
- Configure GitHub Packages registry

**Note:** You need a GitHub token with `read:packages` scope to fetch `@happyvertical/*` packages from GitHub Packages.

## Configuration Details

### Base Settings (`default.json`)

- **Schedule**: Saturday night, 9 PM-midnight `America/Edmonton`
- **Grouping**: One `weekly dependency update` PR for dependency updates
- **Automerge**: Disabled for normal dependency update PRs
- **PR Limits**: 1/hour, 1 concurrent PR, 1 concurrent branch
- **Commit Format**: `chore(deps): update {package} to {version}`
- **Branch Prefix**: `renovate/`
- **Labels**: `dependencies`, `renovate`
- **Vulnerability Alerts**: Always enabled
- **Standalone Lockfile Maintenance**: Disabled in the pnpm preset to avoid a second PR
- **Registry Authentication**: Provided by the Renovate runtime's trusted user-level npm config; the preset does not materialize credential-bearing project `.npmrc` files.
- **Managed Policy Workflow**: `.github/workflows/agent-policy.yml` is excluded because it is generated and validated by the shared agent-policy control plane.

### Layer Strategies

| Layer | Version Strategy | Automerge |
|-------|-----------------|-----------|
| SDK | Pin all versions | Disabled |
| SMRT | Pin SDK, bump others | Disabled |
| Applications | Semver ranges (bump) | Disabled |
| Sites | Semver ranges | Disabled |

### Schedule Overrides

The default schedule is already enabled for HappyVertical repos. Override it only when a repository needs an explicit exception:

```json
{
  "extends": ["local>happyvertical/renovate-config:sdk"],
  "schedule": ["* 0-3 * * 0"]
}
```

## Repository Structure

```
renovate-config/
├── default.json           # Base preset
├── monorepo.json          # Monorepo settings
├── pnpm.json              # pnpm configuration
├── bun.json               # Bun configuration
├── sdk.json               # SDK preset
├── smrt.json              # SMRT preset
├── application.json       # Application preset
├── sites.json             # Sites preset
├── client.json            # Client preset
└── groups/
    ├── ai-sdks.json       # AI SDK grouping
    ├── typescript-tools.json  # TypeScript tools grouping
    ├── aws-sdks.json      # AWS SDK grouping
    ├── database.json      # Database grouping
    └── svelte.json        # Svelte grouping
```

## License

MIT
