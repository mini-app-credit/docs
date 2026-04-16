# CLAUDE.md — Documentation Vault

This file provides guidance to Claude Code when working with documentation in this Obsidian vault.

## Vault Structure

```
docs/
  Home.md                    # Entry point, navigation hub
  product/                   # Product vision, roadmap, PRDs, user stories
  architecture/              # System design, ADRs, module docs, infrastructure
  guides/                    # Setup and integration guides (OAuth, etc.)
  policy/                    # Legal documents (Privacy, Terms, DPA, etc.)
  tasks/                     # Task docs and change log
  assets/                    # Images, diagrams, attachments
```

### Policy directory

`docs/policy/` is the **single source of truth** for all legal documents. Texts are  
written in English. Any  
edit to a policy must happen **here first**, then mirror to the TSX file.

Legal-entity details (name, address, jurisdiction, emails, effective date) are
parametrised via `SITE.legal` in `apps/landing/src/shared/site/constants.ts`. To
rebrand or change entity, only touch that config and the Markdown here — never
hardcode in TSX.

## Writing Rules

- Write in Russian (natural, conversational tone — not formal/corporate)
- Use Obsidian wiki-links: `[[Page Name]]` for internal references
- Use tags: `#product`, `#architecture`, `#adr`, `#prd`, `#roadmap`, `#module`
- Frontmatter is required on every page:
  ```yaml
  ---
  title: Page Title
  created: YYYY-MM-DD
  updated: YYYY-MM-DD
  tags: [relevant, tags]
  ---
  ```
- Keep pages focused — one topic per page, link to related pages
- Architecture docs reference actual file paths from the codebase (`apps/backend/src/...`)
- Product docs are implementation-agnostic — describe what and why, not how

## Naming Conventions

- Files: `kebab-case.md`
- Product docs: `product/prd-feature-name.md`, `product/roadmap.md`
- Architecture docs: `architecture/adr-NNN-title.md`, `architecture/module-name.md`
- Use descriptive names, not numbers or abbreviations

## Obsidian Features to Use

- `> [!note]` / `> [!warning]` / `> [!info]` callouts for important information
- Mermaid diagrams for architecture visualization (```mermaid blocks)
- Tables for comparisons and specifications
- Task lists `- [ ]` for action items and checklists

