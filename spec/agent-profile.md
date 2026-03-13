# OAS-CLI Agent Profile 0.1.0

## Skill Manifest

The OAS-CLI Skill Manifest is a machine-readable advisory document that maps tool IDs to usage guidance and examples.

## Required Fields

- `oasCliSkill`
- `serviceId`
- `toolGuidance`

Each `toolGuidance` entry MAY contain:

- `whenToUse`
- `avoidWhen`
- `examples`

Each `examples[]` item MUST contain:

- `goal`
- `command`

## Safety Metadata

Agent-facing metadata MUST be able to represent:

- `destructive`
- `readOnly`
- `requiresApproval`
- `idempotent`
- retry recommendations
- polling or `Location`-header hints when long-running operations require follow-up requests

Every published NTC tool entry MUST expose an explicit boolean `safety.idempotent` field so agents do not have to infer it from method or prose.

## Workflow Binding

Workflows reference tools by operation identity and are exposed through the normalized catalog as resolved workflow steps.
