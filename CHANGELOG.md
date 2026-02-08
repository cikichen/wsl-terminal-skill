# Changelog

All notable changes to this skill project are documented in this file.

## [2.0] - 2026-02-08

### Added (2.0)

- Reliability-first shell policy (not force-WSL) formalized in `SKILL.md`.
- One-screen decision table for choosing WSL vs PowerShell.
- Explicit execution protocol: shell selection -> shell-specific generation ->
  execution -> explicit fallback.
- `references/SCENARIOS.md` with practical usage and fallback examples.
- Project `README.md` describing structure and expected outcomes.

### Changed (2.0)

- Rewrote `SKILL.md` into a normative policy document with clear guardrails.
- Rewrote `references/REFERENCE.md` into structured reliability guidance.
- Updated metadata and positioning to WSL-optional compatibility.

### Fixed (2.0)

- Removed ambiguity around shell selection logic.
- Clarified fallback communication contract.
- Standardized markdown formatting for lint-clean docs.

## [1.2] - 2026-02-08

### Added (1.2)

- One-screen shell decision table for quick shell choice.
- Compatibility note clarifying WSL is recommended but not mandatory.

### Changed (1.2)

- Strengthened reliability-first wording.

## [1.1] - 2026-02-08

### Added (1.1)

- Reliability-first positioning and shell-aware command generation.
- Explicit fallback boundaries and non-silent shell switching rule.
- AI-focused shell mismatch risk notes in reference docs.

### Changed (1.1)

- Shifted from WSL-first framing toward execution-stability framing.

## [1.0] - 2026-02-08

### Added (1.0)

- Initial WSL-priority skill definition.
- Windows-only exclusion list.
- Core command templates and fallback flow.
