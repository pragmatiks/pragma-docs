# pragma-docs

The public Pragmatiks documentation at [docs.pragmatiks.io](https://docs.pragmatiks.io): MDX pages deployed by Mintlify on every merge to `main`, with a preview URL per PR. `docs.json` defines navigation, theme and settings.

Delivery: pragma-kit `project-instructions.md`. Engineering rules: the `pragma-kit:engineering-principles` skill; load it before planning, writing or reviewing a change.

## Confidential internals

The docs site is public, so internal implementation details never appear in it:

| Category | Forbidden terms |
|----------|-----------------|
| Message queue | NATS, JetStream, message broker, event streaming |
| Database | SurrealDB, graph database internals |
| Scaling | KEDA, auto-scaling implementation, consumer groups |
| Build system | BuildKit, tarball creation, container build process |
| Architecture | Internal service communication, queue depths, retry mechanics |

Document command syntax and options, user-facing workflows, resource YAML structure and the high-level concepts users need. Leave out how the system works internally, internal URLs, ports and paths, where authentication tokens are stored, provider deployment internals and message delivery mechanics. When unsure whether something is confidential, ask before including it.

Review check: any mention of NATS, JetStream, SurrealDB, KEDA, BuildKit, internal URLs or ports, retry mechanics or queue depths in a diff is a blocker.

## Gotchas

- **Frontmatter.** Every page has YAML frontmatter with a clear `title` and a concise `description` (SEO and navigation).
- **Links.** Internal links are root-relative without the extension (`/concepts/resources`), never absolute URLs.
- **Code blocks** declare a language tag, since Mintlify's rendering depends on it. Images carry alt text. Use Mintlify components where they fit.
- **Branch.** `main` changes only through pull requests (squash or rebase); the org ruleset refuses direct pushes, force-pushes and deletion.
- **Setup.** mise is the only toolchain source, and the repository normally has no Node or Python dependency manifest. Fresh clone: `mise trust`, then `mise run install`; `mise run dev` serves on port 3200 and `mise run check` validates the build.
