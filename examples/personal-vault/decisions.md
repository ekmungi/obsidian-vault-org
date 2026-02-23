# Decision Log

## Format

| Field | Description |
|-------|-------------|
| ID | Sequential identifier (DEC-001, DEC-002, ...) |
| Date | Decision date (DD.MM.YYYY) |
| Decision | What was decided |
| Context | Why this decision was needed |
| Alternatives | What else was considered |
| Outcome | Expected result |

---

## DEC-001: Two Separate Vaults with Shared PARA Structure

**Date**: 23.02.2026
**Status**: Decided

**Decision**: Maintain two separate Obsidian vaults (personal and enterprise) rather than a single unified vault. Both vaults use identical PARA folder structure with numbered prefixes (00-04).

**Context**: Anant is migrating from Notion to Obsidian and needs to organize both personal and work knowledge. Key considerations:
- Work content may have compliance or confidentiality requirements
- Personal content should not be on enterprise-managed devices/storage
- Obsidian does not natively support vault-level access controls
- OneDrive sync operates per-vault

**Alternatives Considered**:
1. **Single vault with folder separation** -- Simpler management but risks mixing personal and work content. No way to selectively sync or restrict access.
2. **Two vaults with independent structure** -- Maximum flexibility but doubles maintenance effort and creates inconsistency.
3. **Two vaults with shared structure** (chosen) -- Consistent experience across vaults while maintaining separation. Shared templates designed in this repo and deployed to both.

**Outcome**: Consistent user experience across contexts. Templates and plugins are designed once and deployed to both vaults. Structural decisions apply to both unless explicitly noted as vault-specific.
