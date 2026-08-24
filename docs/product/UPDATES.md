# Update and Release Architecture

Perfex-MCP adopts the hardened updater direction proven in Perfex Magic Login.

## Release contract

Production GitHub Release assets:

```text
perfex_mcp.zip
perfex_mcp.zip.sha256
```

ZIP contains top-level `perfex_mcp/`. Version in module header, Git tag and `perfex_mcp/update_manifest.json` must match exactly.

## Pre-install validation

1. semantic version is newer;
2. ZIP and checksum exist;
3. source is HTTPS and approved GitHub/GitHubusercontent release location;
4. SHA-256 matches;
5. archive entries remain under `perfex_mcp/` and reject traversal/null paths;
6. bootstrap file exists;
7. package version equals release tag;
8. manifest exists/version matches;
9. required Perfex migration target exists and pending sequence is contiguous;
10. target module directory writable and backup can be created.

## Policies

- `off`: manual only.
- `patch`: unattended same-major/minor patch only when manifest marks safe.
- `safe`: any newer release explicitly marked `auto_update_safe`.

Behavior-changing/config/migration-heavy releases default `auto_update_safe: false`.

## Backup / rollback boundary

Before file replacement create a ZIP backup under Perfex temp/update backup area and record it.

Before DB migration starts, a failed file replacement may restore prior files automatically. After DB migration begins, **do not blindly restore old PHP files** because DDL may already be applied. Retain backup, mark failed, persist safe error/state and require DB review before retry.

## Migration numbering

Every published version has the matching Perfex migration target, including deliberate no-op migration for code-only releases where Perfex requires it. Pending migration identifiers stay contiguous. Freeze exact version-number mapping rules in Phase 0 against the supported Perfex loader before publishing `v1.0.0`.

## Release workflow

Tag-triggered CI validates version equality, manifest JSON, matching migration target, PHP lint/static/tests, package structure and secret scan; then builds ZIP, SHA256 and immutable GitHub Release assets.

Never force-push/retarget a published tag or replace an existing published version. Increment safely.

## Update history

`tblperfex_mcp_updates` records source/target version, release tag, checksum, manual/automatic mode, status, backup reference/path, safe error, start/completion timestamps. Admin UI surfaces recent attempts and recovery guidance.

## Current state

No production module package/updater/release implementation exists in the repository at specification time. The first real release line starts only after implementation and staging validation.
