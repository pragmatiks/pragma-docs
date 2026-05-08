# Pragmatiks Codex Instructions

## Project

This repository is `pragma-docs`, the Mintlify documentation site for **Pragmatiks** at `docs.pragmatiks.io`.

User-facing docs must say **Pragmatiks** for the product and platform. Use repository names such as `pragma-docs`, `pragma-cli`, `pragma-sdk`, `pragma-providers`, and `pragma-os` only when referring to repositories, package names, paths, commands, or GitHub projects.

## Claude Code Compatibility

Preserve the existing Claude Code setup. `CLAUDE.md` contains Claude-specific and shared documentation guidance, including confidential implementation terms that must not appear in published docs.

Codex-specific durable instructions live in this file. Do not replace or remove `CLAUDE.md` unless the user explicitly asks.

## Repository Layout

```text
docs.json              Mintlify site configuration, theme, API config, and navigation
index.mdx              Docs landing page
getting-started/       Onboarding and setup verification
quickstart/            Role-based quickstarts
concepts/              Core product concepts
guides/                User workflows and how-to guides
cli/                   CLI reference
sdk/                   SDK reference
store/                 Store and provider catalog docs
providers/             Provider-specific reference pages
building-providers/    Provider authoring docs
mcp/                   MCP server docs
api-reference/         OpenAPI-backed API reference
style.css              Mintlify CSS overrides
```

## Commands

Use `task` as the project interface when a wrapper exists.

- Install tooling: `mise install`
- Check docs build: `task check`
- Preview docs locally: `task dev`
- Build readiness for production deploy: `task build`

Direct Mintlify checks are acceptable when the Taskfile has no wrapper:

- Broken links: `mise exec -- npx mintlify@latest broken-links`
- Accessibility: `mise exec -- npx mintlify@latest a11y`
- Static export, when explicitly needed: `mise exec -- npx mintlify@latest export --output export.zip`

`task dev` serves the docs at `http://localhost:3200`. Run Mintlify commands from the repository root because `docs.json` lives there. The current Mintlify CLI validates production build readiness with `validate`; it does not expose a separate `build` command.

## Release And Deployment

Mintlify deploys from `main` through the Mintlify GitHub integration. Pull requests receive Mintlify preview URLs. There is no separate local release script in this repository.

Before considering docs ready for review, run `task check`. For navigation or broad link changes, also run `mise exec -- npx mintlify@latest broken-links`.

## Documentation Style

Follow the existing Mintlify style:

- Every `.mdx` page must have YAML frontmatter with `title` and `description`.
- Use relative internal links, not absolute docs URLs.
- Add a language tag to every code block.
- Add alt text to images.
- Prefer existing Mintlify components and local page patterns over new structures.
- Keep docs user-facing: document commands, options, workflows, resource YAML, and high-level concepts.
- Do not publish internal implementation details, internal URLs, ports, storage paths, token locations, or provider deployment mechanics.

Use consistent terminology:

- Product/platform: **Pragmatiks**
- Command-line tool: **Pragma CLI** or `pragma`
- Managed objects: resources
- Resource executors/integrations: providers
- Configuration examples: YAML resources and `config` blocks

## Secrets And Local Files

Do not commit local secrets, personal Codex config, MCP auth, hooks, or machine-specific files.

Known local files:

- `.claude/settings.local.json` is ignored and must stay local.
- `.claude/worktrees/` may exist as local Claude Code worktrees and must not be edited or committed as part of normal docs work.

This repository has no required `.env` file for docs validation. Mintlify login may enable hosted features locally, but validation and build commands must not depend on personal auth.

## Git And Worktrees

Codex usually works in the current checkout or a per-thread worktree. Treat the current worktree as the working area. Do not create additional worktrees unless the user asks.

Before editing, inspect relevant files and `git status`. Never revert user changes or unrelated generated changes.

## Linear And Issue Work

Use Linear as the source of truth for planned work and follow-ups.

Autonomy expectations:

- When the user asks to work on a Linear issue, read the issue context, map it to repo files, and identify validation commands before editing.
- Move the issue to an active status when starting substantial work.
- If a clear follow-up is deferred, create or update a Linear issue instead of leaving only chat context.
- Add concise implementation and validation notes to Linear when work is ready for review.
- Do not mark issues Done for local-only work unless the user explicitly asks. Completion means implemented, validated, and merged or accepted.

Escalate to the user before changing Linear when priority, scope, status, or dependency relationships are ambiguous.
