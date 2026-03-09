# Publish to ClawHub

## Goal

Publish and keep this skill synchronized through the ClawHub flow with explicit packaging control.

## Prerequisites

- ClawHub access and authentication configured in your environment
- `SKILL.md` metadata finalized
- `.clawhubignore` reviewed to exclude runtime/local artifacts

## Pre-publish checklist

1) Verify no secrets in repository history or working tree.
2) Ensure `README.md`, `SECURITY.md`, `CHANGELOG.md`, and quickstarts are up to date.
3) Confirm workflows pass (`lint-shell`, `smoke`, `secret-scan`, `link-check`).
4) Confirm release notes draft (`docs/release-v0.1.0.md`) is aligned with tag content.

## Publish and sync flow

Use the ClawHub sync flow for scan and publish updates:

```bash
clawhub sync --all
```

If your setup uses explicit login or project scoping, run those commands first per your ClawHub environment policy.

## Post-publish validation

- Confirm metadata renders correctly from SKILL frontmatter.
- Confirm ignored files are not included in package payload.
- Verify install path from public listing in a clean environment.

## Release hygiene

- Tag release (e.g. `v0.2.0`) after final verification.
- Keep changelog and release notes synchronized.
- Update docs first, then publish, to keep adoption friction low.

## Release commands (after local changes)

If Git identity is not set:

```bash
git config user.email "your@email.com"
git config user.name "Your Name"
```

Then commit, tag, push, and publish:

```bash
git add -A
git status
git commit -m "chore: v0.2.0 - flatten repo, minimal setup, docs, CRLF/LF, session-key optional, generate-client-state"
git tag v0.2.0
git push origin main
git push origin v0.2.0
clawhub sync --all
```
