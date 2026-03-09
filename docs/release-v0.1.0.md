# Release notes draft: v0.1.0

## Summary

Initial public release of a push-based Microsoft Graph skill for OpenClaw.

Core proposition:
- webhook-driven Graph mail notifications
- queue + dedupe worker
- wake OpenClaw only when work actually happens

## Highlights

- Push-first mail integration (`/hooks/wake` default)
- Graph subscription lifecycle automation
- EC2 setup scripts for reverse proxy + services
- End-to-end smoke tests and autonomous diagnostics
- Documentation for self-hosted production operation

## Quickstart

1) Authenticate with Graph device code flow.
2) Configure webhook endpoint and OpenClaw hooks.
3) Create subscription and start worker loop.
4) Validate with diagnostics + smoke tests.

Primary references:
- `README.md`
- `SKILL.md`
- `references/mail_webhook_adapter.md`

## Known limitations

- Requires public HTTPS endpoint for Graph notification delivery.
- Personal Microsoft accounts may differ from work/school behavior in some Graph scenarios.
- Teams online meeting provisioning remains tenant/account dependent.
- Runtime logs can vary by host logging backend and retention settings.

## Security notes

- Hook token and Graph clientState are required in production.
- Token-bearing files must not be committed.
- Use least-privilege scopes and rotate credentials if exposed.

## Roadmap (short)

- Improve docs conversion assets (demo GIF/video and install journeys)
- Expand CI checks and publish workflow hardening
- Add richer deployment profiles for common self-hosted topologies
