# OAS-CLI CLI Profile 0.1.0

## Command Shape

The CLI command shape is:

```text
oascli <service> <group> <command> [args...] [flags...]
```

## Group Derivation

Implementations derive groups in this order:

1. `x-cli-group`
2. first OpenAPI tag
3. first non-parameter path segment
4. `misc`

## Command Derivation

Implementations derive command names in this order:

1. `x-cli-name`
2. normalized `operationId`
3. inferred verb from HTTP method plus path

If `x-cli-aliases` is present, implementations SHOULD expose those aliases as additional invokable command names. If `x-cli-hidden` is present, the command SHOULD be hidden from default help and listings while remaining invokable. If `x-cli-ignore` is present, the operation MUST be removed from the normalized catalog entirely.

## Parameters

- path parameters are positional arguments in path order
- query, header, and cookie parameters become flags
- request bodies must support structured JSON input

For request bodies, implementations MUST support:

- `--body @file.json`
- `--body -` for stdin
- an NTC `requestBody` contract that preserves `required`, media types, and a machine-readable schema for each content type

When overlays provide `x-cli-description`, `x-cli-output`, `x-cli-pagination`, or `x-cli-retry`, implementations SHOULD surface those hints through the normalized catalog so CLIs and agents can reproduce consistent help, formatting, and control-flow behavior.

## Output

Implementations MUST support:

- `json`
- `yaml`
- `pretty`

They MUST also expose a machine-readable tool schema derived from the normalized catalog.
