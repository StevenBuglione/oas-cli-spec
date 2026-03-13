# OAS-CLI Config 0.1.0

## File Locations

Recommended scope locations:

- Managed: `/etc/oas-cli/.cli.json`
- User: `$XDG_CONFIG_HOME/oas-cli/.cli.json`
- Project: `<repo>/.cli.json`
- Local: `<repo>/.cli.local.json`

## Precedence

Scopes merge in this order:

1. Managed
2. User
3. Project
4. Local

Later scopes override earlier mutable values, but Managed denies remain absolute.

## Merge Rules

- keyed maps merge by key
- explicit `enabled: false` disables a source or service without deleting it
- allow and deny arrays append uniquely across scopes
- managed denies are preserved separately and remain non-overridable

## Refresh Policy

Each source MAY define a `refresh` object with these fields:

- `maxAgeSeconds`: advisory freshness override for HTTP-fetched discovery documents when origin cache headers are absent or more permissive local policy is required
- `manualOnly`: when `true`, automatic revalidation SHOULD be suppressed and stale cached content SHOULD be retained until an explicit refresh trigger is invoked

Refresh policy is source-scoped and follows normal scope precedence rules.

## Secret References

Secret values MUST NOT be embedded directly in project config. Supported reference types are:

- `env`
- `file`
- `osKeychain`
- `exec`

`exec` resolution MUST be disabled unless explicitly enabled by policy.
