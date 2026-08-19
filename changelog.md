---
description: 📜 Automated changelog and release notes generation workflow based on Git commit history, semantic versioning, and Keep a Changelog standards.
---

# /changelog - Changelog & Release Notes Generation Mode

$ARGUMENTS

## Purpose & Core Rule

Activates CHANGELOG mode to analyze Git commit history and PR diffs between tags/branches and generate structured, human-readable release notes.

> **CHANGELOG = ACCURATE DELTAS, USER-CENTRIC IMPACT, ZERO FABRICATED ENTRIES**

- Group changes strictly according to the **Keep a Changelog** standard (`Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, `Security`).
- Parse Conventional Commits (`feat`, `fix`, `refactor`, `perf`, `docs`, `breaking`) into clean release bullet points.
- Highlight breaking changes and migration steps prominently at the top of the version section.

---

## When to Use

- **Use when:** Preparing a new release/tag, updating `CHANGELOG.md`, summarizing features/fixes delivered in a milestone, or creating GitHub Release notes.
- **Do not use:** Writing general documentation or summarizing individual code files.

---

## Supported Intents

```text
/changelog
/changelog v1.0.0...v1.1.0
/changelog --unreleased
/changelog --version 2.0.0
/changelog --github-release
```

---

## Change Classification Standards

| Section Header | Included Commit Types | Description |
| :--- | :--- | :--- |
| **🚨 Breaking Changes** | Commits with `BREAKING CHANGE` or `type!:` | Incompatible API changes, removed endpoints, DB migrations requiring manual steps |
| **✨ Added** | `feat`, `feat(...)` | New user-facing features, new API endpoints, new capabilities |
| **⚡ Changed** | `perf`, `refactor` | Modified existing functionality, performance gains, structural enhancements |
| **🐛 Fixed** | `fix`, `fix(...)` | Bug fixes, regression resolutions, error handling improvements |
| **🔒 Security** | `security`, `fix(security)` | Vulnerability patches, auth hardening, dependency CVE remediations |
| **🗑️ Deprecated / Removed**| `deprecate`, `remove` | Flagged features to be removed in future versions, dropped obsolete code |

*(Note: purely internal `chore`, `ci`, `test`, `style` commits are filtered out or grouped under a minor "Maintenance" collapsed section to avoid user-facing noise).*

---

## Execution Protocol

### Phase 1: Determine Version Delta & Target Range
- Identify base tag and target tag/HEAD (e.g. `git tag -l`, `git log v1.0.0..HEAD`).
- If no tag is provided, default to changes since the last tag or `--unreleased`.

### Phase 2: Extract & Parse Git History
- Run `git log <range> --pretty=format:"%h%x09%an%x09%s"` to extract commit hashes, authors, and subjects.
- Filter out merge commits and noise. Categorize each commit into its corresponding Keep a Changelog group.

### Phase 3: Synthesize User-Centric Descriptions
- Rewrite cryptic commit messages into clear, benefit-driven summaries (e.g. `feat(auth): add google sso` ➔ `Added support for Google OAuth single sign-on`).

### Phase 4: Format & Update CHANGELOG.md
- Prepend the new version section under `## [Unreleased]` or `## [vX.Y.Z] - YYYY-MM-DD` in `CHANGELOG.md`.

---

## Output Format

```markdown
## 📜 Release Notes: [vX.Y.Z] (YYYY-MM-DD)

### 🚨 Breaking Changes
- **Auth:** Deprecated session cookie endpoint in favor of Bearer tokens (`POST /api/v2/auth/login`).

### ✨ Added
- Added multi-language support for Vietnamese and English locales.
- Added synthetic relational data seeding script for staging environments.

### 🐛 Fixed
- Resolved race condition during concurrent order checkout submissions.
- Fixed SSR hydration mismatch on landing page hero banner.

### ⚡ Performance & Maintenance
- Virtualized customer table rendering, reducing memory footprint by 40%.
- Upgraded Next.js to v15 and React to v19.
```

---

## Definition of Done

1. Git commits within the specified range parsed and accurately categorized.
2. Breaking changes and migration notes explicitly surfaced.
3. Output formatted cleanly according to Keep a Changelog standards.
