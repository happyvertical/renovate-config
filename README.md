# @happyvertical/renovate-config

Shareable [Renovate](https://docs.renovatebot.com/) configuration for the HappyVertical organization.

## Presets

### Organization Presets

| Preset | Description | Use Case |
|--------|-------------|----------|
| `default` | Base configuration with common settings | Extended by all other presets |
| `monorepo` | Monorepo settings (handles `workspace:*`) | SDK, SMRT |
| `pnpm` | pnpm-specific configuration | Most repos |
| `bun` | Bun-specific configuration | IAC repo |
| `sdk` | SDK (foundation layer) | SDK monorepo |
| `smrt` | SMRT (framework layer) | SMRT monorepo |
| `application` | Application layer | praeco, caelus, aedile |
| `sites` | Web sites | happyvertical.com, bangblow.com |
| `client` | External organizations | Client orgs using @happyvertical/* |

### Dependency Grouping Presets

| Preset | Packages Grouped |
|--------|------------------|
| `groups/ai-sdks` | OpenAI, Anthropic, Google GenAI, LangChain, MCP |
| `groups/typescript-tools` | TypeScript, Vite, Vitest, Biome, Turbo, Changesets |
| `groups/aws-sdks` | @aws-sdk/* packages |
| `groups/database` | LibSQL, PostgreSQL, better-sqlite3, DuckDB |
| `groups/svelte` | Svelte, SvelteKit, Svelte plugins |

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
- Group all `@happyvertical/*` packages together
- Automerge patch updates
- Require review for major updates
- Configure GitHub Packages registry

**Note:** You need a GitHub token with `read:packages` scope to fetch `@happyvertical/*` packages from GitHub Packages.

## Configuration Details

### Base Settings (`default.json`)

- **Schedule**: Manual triggers via Dependency Dashboard (empty schedule)
- **Automerge**: All patch updates
- **PR Limits**: 4/hour, 10 concurrent
- **Commit Format**: `chore(deps): update {package} to {version}`
- **Branch Prefix**: `renovate/`
- **Labels**: `dependencies`, `renovate`
- **Vulnerability Alerts**: Always enabled

### Layer Strategies

| Layer | Version Strategy | Automerge |
|-------|-----------------|-----------|
| SDK | Pin all versions | Patches only |
| SMRT | Pin SDK, bump others | Patches + SDK patches |
| Applications | Semver ranges (bump) | All patches |
| Sites | Semver ranges | All patches |

### Enabling Automatic Updates

The default schedule is manual (via Dependency Dashboard). To enable automatic updates, add a schedule to your config:

```json
{
  "extends": [
    "local>happyvertical/renovate-config:sdk",
    "schedule:weekly"
  ]
}
```

Available schedules:
- `schedule:weekly` - Monday mornings
- `schedule:monthly` - First Monday of month
- `schedule:nonOfficeHours` - Outside business hours

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
