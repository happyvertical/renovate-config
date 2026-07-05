# Dependency Update Strategy

## Current Behavior

- **Cadence**: One weekly PR on Saturday night, 9 PM-midnight `America/Edmonton`
- **Grouping**: Major, minor, patch, pin, and digest updates share the `weekly dependency update` PR
- **Automerge**: Disabled for normal dependency update PRs
- **Vulnerability Alerts**: Always create PR (never automerge)
- **Standalone Lockfile Maintenance**: Disabled for pnpm repos to avoid a second PR

## Allowing Repository Exceptions

Prefer keeping repository exceptions narrow. If a repository needs a temporary update stream for a specific package, add a local `packageRules` entry in that repository:

```json
{
  "description": "Hold this package for manual dashboard approval",
  "matchPackageNames": ["example-package"],
  "dependencyDashboardApproval": true
}
```
