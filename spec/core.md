# OAS-CLI Core 0.1.0

## Scope

OAS-CLI standardizes how OpenAPI-described HTTP services become agent-usable CLI tools without introducing a separate tool protocol. The core specification defines:

- discovery inputs and provenance requirements
- normalized tool catalog structure
- runtime execution and policy enforcement rules
- audit, caching, and governance expectations

## Discovery

Implementations MUST support these source types:

- `apiCatalog`: RFC 9727 API catalogs represented as `application/linkset+json`
- `serviceRoot`: RFC 8631 service roots that advertise `service-desc` and `service-meta`
- `openapi`: direct references to OpenAPI documents

Implementations MUST record provenance for every fetched discovery document, including the URL, fetch timestamp, and discovery method.

When a fetched document is obtained through HTTP, implementations MUST also retain any relevant cache metadata that informed freshness or staleness decisions, including validator fields such as `ETag` and `Last-Modified`, cache directives such as `Cache-Control`, cache outcome (`miss`, `fresh_hit`, `revalidated_hit`, `refreshed`, or `stale_hit`), and an explicit stale marker when cached content is served after refresh failure or offline operation.

## Normalized Tool Catalog

The runtime-to-CLI contract is the Normalized Tool Catalog (NTC). It MUST include:

- `catalogVersion`
- `generatedAt`
- `sourceFingerprint`
- `sources`
- `services`
- `tools`
- `effectiveViews`

Tool IDs MUST be stable across refetches of the same service. When `operationId` is present, it MUST be used as the operation identity component. Otherwise, implementations MUST derive an identity from method plus canonical path and mark it unstable.

Each tool entry SHOULD preserve the agent-relevant CLI metadata needed to regenerate a stable command surface, including aliases, description overrides, hidden status, request-body contracts, guidance examples, and any output, pagination, retry, or idempotency hints carried by overlays or skill manifests. When a tool exposes a request body, the published NTC contract MUST include `requestBody.required` and one or more `requestBody.contentTypes[]` entries, and each content-type entry MUST include both the media type and a machine-readable schema. Each tool's `safety` block MUST also include an explicit boolean `idempotent` field.

Each `sources[].provenance` entry MUST identify the provenance method and timestamp, and SHOULD expose per-fetch records when the implementation fetched remote discovery documents. These fetch records SHOULD include the HTTP request method, status code, and cache metadata that explain refresh or stale behavior.

## Runtime Requirements

The runtime MUST:

- apply overlays before OpenAPI indexing
- enforce managed policy denies before any network execution
- support curated mode and agent profile restrictions
- resolve secret references at execution time
- support manual refresh of cached remote discovery inputs
- emit structured observability events for runtime request lifecycles
- emit an audit event for every allowed or denied invocation attempt

Local implementations MUST support:

- an embedded runtime mode that executes in-process without a daemon
- a daemon mode with a stable runtime metadata record that local CLIs can discover
- isolated cache, audit, and runtime metadata paths per derived config scope or explicit instance id, with explicit overrides available for operators

Tracing hooks are OPTIONAL, but implementations that offer them SHOULD wrap runtime request handling, remote discovery fetches, catalog build operations, and refresh execution behind stable hook points so tracing can be enabled without changing business logic call sites.

## Governance

Policy enforcement order is:

1. merge scoped config
2. discover and normalize
3. compute effective view
4. apply managed denies
5. apply curated or agent restrictions
6. apply approval requirements
7. execute

## Compatibility Publication

Each conformance suite release SHOULD publish a machine-readable compatibility matrix that identifies the suite version, the targeted spec version, the implementation release or commit being assessed, and the pass/partial/fail status of major feature buckets such as HTTP caching, refresh, and observability hooks.
