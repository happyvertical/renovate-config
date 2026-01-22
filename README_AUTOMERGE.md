# Automerge Configuration

## Current Behavior

- **Minor & Patch**: Automerge without PR
- **Major**: Create PR for review
- **Vulnerability Alerts**: Always create PR (never automerge)

## Automerging Major Updates

To automerge major updates for specific packages (e.g., packages with frequent major releases or integer versioning), add them to the `matchPackageNames` array in `default.json`:

```json
{
  "description": "Automerge major updates for packages with frequent major releases",
  "matchPackageNames": [
    "@types/node",
    "@types/react",
    "typescript-eslint"
  ],
  "matchUpdateTypes": ["major"],
  "automerge": true,
  "automergeType": "branch"
}
```

### Common Candidates

Packages that often have non-breaking major updates:
- `@types/*` - TypeScript type definitions
- `@eslint/*` - ESLint core and plugins
- `eslint-plugin-*` - ESLint plugins
- `prettier-plugin-*` - Prettier plugins
- Build tools with calendar-based versioning

You can also use patterns:
```json
"matchPackagePatterns": ["^@types/", "^eslint-plugin-", "^prettier-plugin-"]
```
