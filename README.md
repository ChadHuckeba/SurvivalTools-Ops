# SurvivalTools-Ops

SurvivalStack observability repository. Owns the Alerts board, alert issue templates,
and ops configuration for the sentinel skill.

## Purpose

SurvivalTools-Ops is the observability companion to agy CLI. It provides the
infrastructure sentinel uses to record, track, and escalate agy operational events --
governance violations, failed operations, self-corrections, and other behavioral
anomalies that require operator attention.

This repository is not a general-purpose alerting system. It is scoped exclusively
to agy activity within the SurvivalStack ecosystem.

## Components

### Alerts Board (Project 12)

The Alerts board is the sole surface for sentinel-created alert items. It uses three
status values: Open, In Progress, and Resolved. Custom fields: Severity, Fingerprint,
and Ack Notes.

Alerts must not be created directly on this board. All alert creation goes through
the sentinel skill.

### ops-config.json

The authoritative configuration file for the observability layer. Contains:

- `ops_board`: Alerts board metadata (node ID, field IDs)
- `alert_taxonomy`: Named alert types with default severities and descriptions
- `alert_body_schema`: Required sections for all alert issue bodies
- `garbage_collection`: Archival policy configuration (currently disabled, pending #240)

### Issue Templates

Three issue templates govern alert creation:

- `normal-alert.md`: For Normal severity events
- `error-alert.md`: For Error severity events
- `critical-alert.md`: For Critical severity events

Templates must be used via `--body-file` when creating alert issues. Inline body
content is prohibited.

## Alert Taxonomy

| Alert Type | Default Severity | Description |
| :--- | :--- | :--- |
| `skill-linkage-failure` | Error | Issue-to-project linkage failed and manual fallback was required |
| `node-id-mismatch` | Error | Cached board node IDs or field IDs mismatch GitHub API |
| `service-health-failure` | Critical | A core ecosystem service went offline or failed healthchecks |
| `governance-violation` | Critical | An operation violated global or project policies |
| `provisioning-token-rotation` | Normal | PROVISIONING_TOKEN is approaching expiration |
| `branch-not-cleaned-after-merge` | Normal | A branch was not cleaned up after PR merge |

## Sentinel Integration

This repository is consumed by the sentinel skill located in the SurvivalStack skills
vault. Sentinel reads `ops-config.json` at alert creation time to resolve board
metadata and taxonomy. The `registry.json` in core-dotfiles is the authoritative
source for status option IDs and standard labels.

For sentinel procedural documentation see:
`~/.gemini/antigravity-cli/skills/sentinel/SKILL.md`

## Governance

This repository follows SurvivalStack global governance. See:
`~/core/dotfiles/gemini/GEMINI.md`

Garbage collection and archival policy are pending. See core-dotfiles issue #240.
