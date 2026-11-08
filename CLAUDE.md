# CLAUDE.md

## Project

**pragma-docs**: Documentation for the Pragmatiks platform, hosted on [docs.pragmatiks.io](https://docs.pragmatiks.io).

## Shared rules

Delivery lives in pragma-kit `project-instructions.md`. Evidence-based development, the solution preference order, the new-dependency proposal, the 13 engineering principles and the reviewer and developer protocols live in the pragma-kit plugin's `pragma-kit:engineering-principles` skill: load it before planning, writing or reviewing a change here. This file holds only what is specific to this repository.

## Deployment

Deployed via **Mintlify** — every merge to `main` auto-deploys. `main` changes only through pull requests (squash or rebase); the org ruleset refuses direct pushes, force-pushes and deletion. Mintlify provides preview URLs for PRs.

**Config**: `docs.json` defines navigation, theme, and settings.

## Development

mise is the only toolchain source: `mise.toml` pins node, and `mise.lock` locks it for macOS arm64 and Linux x64. On a fresh clone run `mise trust`, then `mise run install`.

Use mise tasks (`mise tasks` lists them all):

| Command | Purpose |
|---------|---------|
| `mise run install` | Install the locked toolchain |
| `mise run dev` | Start the local docs server on port 3200 |
| `mise run check` | Validate the Mintlify docs build |
| `mise run lock:toolchain` | Re-lock the toolchain after editing `mise.toml` (needs mise at the `min_version` in `mise.toml`) |
| `mise run check:mise-pins` | Fail when a `jdx/mise-action` step's `version:` or `sha256:` differs from `min_version` and its runner's mise binary checksum |
| `mise run check:toolchain-scripts` | Fail when a shared toolchain script under `scripts/tasks/` differs from the public repositories' copies (same-named branch, else `main`) |

This repository normally has no Node or Python dependency manifest; adding one is a new-dependency proposal that also argues why a Mintlify-hosted repository needs it.

---

## CONFIDENTIAL — DO NOT PUBLISH

**The following internal implementation details are CONFIDENTIAL and must NEVER appear in documentation:**

| Category | Forbidden Terms |
|----------|-----------------|
| Message queue | NATS, JetStream, message broker, event streaming |
| Database | SurrealDB, graph database internals |
| Scaling | KEDA, auto-scaling implementation, consumer groups |
| Build system | BuildKit, tarball creation, container build process |
| Architecture | Internal service communication, queue depths, retry mechanics |

**What TO document:**
- Command syntax and options
- User-facing workflows
- Resource YAML structure
- High-level concepts (what users need to know)

**What NOT to document:**
- How the system works internally
- Internal URLs, ports, paths
- Authentication token storage locations
- Provider deployment internals
- Message delivery mechanics

If unsure whether something is confidential, ASK before including it.

**Review check.** Any mention of NATS, JetStream, SurrealDB, KEDA, BuildKit, internal URLs or ports, retry mechanics or queue depths in a diff = blocker.

---

## Mintlify specifics

- **Format**: MDX files with YAML frontmatter (every page).
- **Frontmatter required**: `title` (clear page title), `description` (concise SEO/navigation summary).
- **Internal links**: relative paths only — never absolute URLs.
- **Code blocks**: every block must declare a language tag (Mintlify rendering depends on it).
- **Images**: must include alt text.
- **Components**: use Mintlify components where applicable.
