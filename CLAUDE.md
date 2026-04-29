# CLAUDE.md

## Project

**pragma-docs**: Documentation for the Pragmatiks platform, hosted on [docs.pragmatiks.io](https://docs.pragmatiks.io).

## Deployment

Deployed via **Mintlify** — push to `main` auto-deploys. Mintlify provides preview URLs for PRs.

**Config**: `docs.json` defines navigation, theme, and settings.

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

---

## Mintlify specifics

- **Format**: MDX files with YAML frontmatter (every page).
- **Frontmatter required**: `title` (clear page title), `description` (concise SEO/navigation summary).
- **Internal links**: relative paths only — never absolute URLs.
- **Code blocks**: every block must declare a language tag (Mintlify rendering depends on it).
- **Images**: must include alt text.
- **Components**: use Mintlify components where applicable.
