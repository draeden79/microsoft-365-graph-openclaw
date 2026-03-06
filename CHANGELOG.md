# Changelog

All notable changes to this project will be documented in this file.

The format is based on Keep a Changelog, and this project follows Semantic Versioning.

## [0.1.0] - 2026-03-06

### Added
- Push-first Microsoft Graph workflow for OpenClaw with webhook adapter, queue, and worker.
- EC2 setup automation, smoke tests, and full pipeline diagnostics scripts.
- Mail subscription lifecycle CLI (`create`, `status`, `renew`, `delete`, `list`).
- Wake-first integration with OpenClaw hooks (`/hooks/wake` default mode).

### Changed
- Default subscription resource updated to `me/messages` for broader delivery coverage.
- Documentation refocused on cost-aware, event-driven operation and self-hosted production setup.
- Diagnostics now include human-readable UTC timestamps for webhook operation logs.

### Security
- Documented secret handling and explicit hook token/clientState validation guidance.
- Added publish-scope controls and release hardening documentation.
