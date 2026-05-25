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

## Evidence-based authoring

Use MCPs to fact-check before writing docs. The cost of a query is far less than shipping incorrect command syntax or stale screenshots.

**Use internal knowledge for**: docs structure, writing style, MDX/Markdown patterns, Mintlify component selection.

**Always query MCPs for**: current Pragmatiks command syntax, current SDK / API shapes, current Mintlify component APIs, anything where being wrong would mislead readers.

If you find yourself thinking "I'm pretty sure `pragma resources apply` takes flag X" or "the SDK exposed Y last time I used it" — STOP and query. Pragmatiks moves fast; the docs are downstream of every other repo.

### MCP routing

- **context7** (`mcp__context7__resolve-library-id`, `mcp__context7__query-docs`) — authoritative current docs for a specific library / framework / SDK / CLI. Use whenever you need to confirm the shape of an external thing you are documenting.
- **deepwiki** (`mcp__deepwiki__ask_question`, `mcp__deepwiki__read_wiki_contents`, `mcp__deepwiki__read_wiki_structure`) — conversational Q&A over an entire OSS GitHub repository. Use to confirm Pragmatiks SDK / CLI / provider behavior. Pragmatiks repos are indexed at `pragmatiks/{sdk,cli,providers}`.
- **exa** (`mcp__exa__web_search_exa`, `mcp__exa__get_code_context_exa`, `mcp__exa__crawling_exa`) — live web search (release notes, GitHub issues) and direct code extraction from a GitHub URL. Use when context7 / deepwiki cannot answer.
- **claude-mem** (`mcp__plugin_claude-mem_mcp-search__smart_search`, `mcp__plugin_claude-mem_mcp-search__search`, `mcp__plugin_claude-mem_mcp-search__get_observations`) — search prior session memory. Use to recover earlier decisions about how a topic should be documented, or to confirm that a topic has not already been written up.

## Solution preference order

Before writing custom MDX components or scripts, work through these in order:

1. **Reuse what is already in the project.** Check `docs.json` for an existing Mintlify component, navigation pattern, or theme setting that solves the problem. Grep existing pages for prior patterns. The cheapest correct answer is already on disk.
2. **Adopt an established Mintlify component.** Mintlify ships a rich component library — prefer official components over hand-rolled JSX. Cross-check the current component list via context7 or the Mintlify docs.
3. **Custom MDX, only as a last resort.** Only after 1 and 2 fail should you write a custom component or script.

Prefer the simplest page that reads well. Avoid clever layouts that add maintenance burden.

## New dependency proposal (BLOCKING)

This repo is Mintlify-managed and normally has no Node / Python dependency manifest. If your work introduces one (e.g. a new `package.json` for a custom build step), STOP before adding it.

1. **Research candidates.** For each viable candidate, record: name, version, license, maintainer, last release date, popularity signals, known issues, fit and trade-offs, at least one realistic alternative considered.
2. **Present findings to the user** with a one-sentence recommendation, including whether the dependency is even necessary in a Mintlify-hosted repo. Do NOT install.
3. **Wait for approval.** Install only after explicit user approval. If rejected, fall back to plain MDX or to a different approach.

For trivial editorial changes (new pages, edits, navigation reorders) this section does not apply.

## Engineering Principles

Canonical engineering rules for all Pragmatiks code in this repository. Workers (developers and reviewers) must follow these in every dispatch. Reviewers must check each PR against this list and produce one finding per violation.

### Scope

Applies to all code in this repository. Some principles only apply to one language or stack — flagged where relevant.

This section is the ground truth for engineering principles in this repository. The same text is embedded in every Pragmatiks subrepo's `CLAUDE.md`. When a principle changes, every embed must be updated in lockstep and the corresponding `pragmatiks-lint` / `@pragmatiks/lint` rule versions bumped.

### Enforcement layers

| Layer | What | Where |
|---|---|---|
| 1. Style + standard smells | `ruff` (Python), `eslint` (TS) with curated rule set | per-repo `task check` / `pnpm lint` |
| 2. Complexity gating | `radon` / `xenon` (Python), `eslint-plugin-sonarjs/cognitive-complexity` (TS) | CI fail on regression |
| 3. Pragmatiks-specific rules | `semgrep` ruleset (cross-language) + custom scripts | shared via `pragmatiks-lint` (PyPI) and `@pragmatiks/lint` (npm) |

If a principle has a programmatic check, the reviewer relies on the tool. If the principle is judgment-based, the reviewer comments with `⚠️` severity.

---

### 1. YAGNI — You Aren't Gonna Need It

Do not add features, abstractions, or configuration for hypothetical future needs. No premature generalization, no speculative interfaces, no "we might need this later" code.

**Programmatic check**:
- Python: `vulture` flags unused functions and dead branches.
- TS: `knip` flags unused exports, files, and dependencies.

**Reviewer hint**: flag any new abstraction layer not justified by current callers.

### 2. KISS — Keep It Simple

Prefer the simplest implementation that works. Three similar lines beat a premature abstraction. Inline the obvious; abstract only when a third caller appears.

**Programmatic check**:
- Python: `ruff C901` (cyclomatic complexity threshold).
- TS: `eslint-plugin-sonarjs/cognitive-complexity`.

**Reviewer hint**: extract-method PR? Verify there are at least three callers in the diff or repo.

### 3. Boy Scout Rule

Leave the file better than you found it. Small adjacent cleanup (rename, move, dead-line removal) is welcome when touching a file. Do not pile in unrelated refactors.

**Programmatic check**: none — judgment.

**Reviewer hint**: if a PR touches no nearby messy code, no penalty. If it adds new mess, block.

### 4. Open–Closed Principle

Modules should be open for extension and closed for modification. New behavior added by adding code, not by modifying existing tested code paths.

**Programmatic check**: none — judgment.

**Reviewer hint**: if a PR modifies a stable public interface or stable internal contract to add a feature that could have been added via a new function/method, request an alternative.

### 5. Single Responsibility Principle

Each function, method, class, and module should have one reason to change. If you cannot describe what a unit does without saying "and" or "or", split it.

**Programmatic check**:
- Function names with `_and_`, `_or_`, `And`, `Or` flagged by `pra-srp-and-or-name` semgrep rule.
- Function size: `eslint max-lines-per-function`, `max-statements`, `max-depth`. Python: `ruff PLR0915` (too many statements), `PLR0912` (too many branches).
- Cognitive complexity from #2.

**Reviewer hint**: if a function name reads as compound, splitting is mandatory.

### 6. Always Use Dependency Injection

Pass dependencies in via constructor / function arguments. Do not instantiate concrete services inside business logic. Wire the graph at the application boundary (FastAPI lifespan, CLI entry point, Next.js server boundary, test harness).

**Programmatic check**:
- `pra-no-inline-instantiation` semgrep rule (heuristic): flags concrete-class instantiation inside non-boundary modules. False positives expected — allowlist module paths (`main.py`, `app.py`, `lifespan.py`, `entry.ts`, etc.).

**Reviewer hint**: a class that constructs an `httpx.AsyncClient` inside `__init__` is wrong; it should accept one as a constructor arg.

### 7. I/O Prefix Discipline

Function/method names starting with `get_`, `fetch_`, `retrieve_`, `load_`, `save_`, `read_`, `write_`, `query_` must perform I/O (network, disk, database, IPC). Pure-computation functions must use neutral names (`compute_*`, `build_*`, `derive_*`, `format_*`, `parse_*`).

**Programmatic check**:
- `pra-io-prefix-mismatch` semgrep rule: flags `get_*` / `fetch_*` / `retrieve_*` functions whose body contains no `await`, no httpx/requests/db client call, no file open. Heuristic; allowlist via decorator (`@no_io`) or function tag.

**Reviewer hint**: a `get_user_id_from_token(token: str) -> str` that just decodes a JWT must be renamed `parse_user_id_from_token` or `extract_user_id`.

### 8. Twelve-Factor App

Configuration via environment variables only. Read environment at the application boundary, never deep in business logic. No credentials, URLs, or behavior flags hard-coded. Stateless processes. Treat backing services (DB, cache, queue) as attached resources via URLs.

**Programmatic check**:
- `pra-env-read-deep` semgrep rule: flags `os.environ` / `os.getenv` / `process.env` reads outside designated boundary modules.
- `pra-no-hardcoded-secrets` semgrep rule: flags string literals matching common credential patterns (`sk-`, `AKIA`, etc.).

**Reviewer hint**: env reads should live in a settings module (Python: `Settings` Pydantic class; TS: a single `env.ts` boundary file).

### 9. Clean Code (default)

When unsure, follow Clean Code: meaningful names, small functions, single level of abstraction per function, no flag arguments, fewer arguments over more, prefer pure functions, fail fast at boundaries.

**Programmatic check**: combination of `ruff`, `eslint`, `eslint-plugin-sonarjs`, `eslint-plugin-unicorn`.

**Reviewer hint**: if a function takes a boolean flag that switches behavior, flag (split into two functions).

### 10. No Comments

The code must be self-explanatory. Do not write comments. Exceptions:

- Public docstrings on library APIs (`pragma-sdk` public surface).
- A single-line WHY comment for a non-obvious workaround, hidden constraint, or subtle invariant. Removing it would confuse a future reader.

Forbidden: block comments restating what the code does; section dividers; commented-out code; "added for X" / "used by Y" trail comments; multi-line docstrings on private internals; planning comments left in source (`# TODO: refactor later`).

**Programmatic check**:
- `pra-no-block-comments` semgrep rule: flags multi-line `#` blocks in Python and `/* ... */` blocks in TS that are not docstrings.
- `pra-no-todo-comments` semgrep rule: flags `# TODO` / `// TODO` / `/* TODO */`.
- Existing custom script for comment ban (to migrate to semgrep).

**Reviewer hint**: every comment in the diff must be justifiable as WHY. Otherwise: delete and rename code instead.

### 11. Semantic Names — No Abbreviations

Identifiers must use full words. No `k8s`, `cfg`, `db`, `req`, `res`, `ctx`, `tmp`, `pkg`, `svc`, `mgr`, `repo`, `usr`, `pwd`, `idx`, `cnt`, `msg`, `err`, etc. Use `kubernetes`, `config`, `database`, `request`, `response`, `context`, `temporary`, `package`, `service`, `manager`, `repository`, `user`, `password`, `index`, `count`, `message`, `error`.

**Allowlist** (industry-standard exceptions):
- `id`, `url`, `uri`, `api`, `cli`, `sdk`, `os`, `io`, `ip`, `tls`, `ssl`, `jwt`, `json`, `yaml`, `html`, `css`, `dom`, `ast`, `gpu`, `cpu`, `ram`, `vm`.
- React-specific: `props`, `ref`, `e` (event handler param).
- Python-specific: `cls`, `self`, `kwargs`, `args`.

**Programmatic check**:
- `eslint-plugin-unicorn/prevent-abbreviations` (TS) — direct fit, with allowlist config.
- `pra-no-abbreviations` semgrep rule (Python) — regex matching forbidden short identifiers, with allowlist.

**Reviewer hint**: `db`, `cfg`, `k8s` in any new code = blocker.

### 12. Compound Names Violate SRP

If a function or method name contains `and`, `or`, `then`, or describes multiple actions, it violates SRP and must be split. Same applies to class names and module names. Examples to forbid: `validate_and_save_user`, `fetch_or_create_session`, `build_and_publish_wheel`.

**Programmatic check**:
- `pra-srp-and-or-name` semgrep rule (cross-language).

**Reviewer hint**: blocker — propose the split inline.

---

### Reviewer protocol

Every reviewer dispatch must:

1. Run `pragmatiks-lint check` (programmatic findings) before reading the diff.
2. Read the diff.
3. For each principle, produce findings as:

   ```
   path:line: <emoji> <severity>: <principle #N> <problem>. <fix>.
   ```

   Severities: 🚨 blocker · ⚠️ important · 💡 nit.

4. Final verdict: `APPROVE` / `APPROVE_WITH_NITS` / `REQUEST_CHANGES`.
5. **Evidence-check the diff.** If the diff documents command syntax, SDK shapes, or external API behavior you cannot fully verify, query context7 / deepwiki / exa to confirm. Pragmatiks command surface drifts — verify before approving.
6. **Confidentiality scrutiny.** Cross-check the diff against the `## CONFIDENTIAL — DO NOT PUBLISH` section above. Any mention of NATS, JetStream, SurrealDB, KEDA, BuildKit, internal URLs/ports, retry mechanics, or queue depths = blocker. If a new top-level dependency was added, also confirm the new-dependency proposal is in the PR description.

A reviewer who fails to invoke programmatic tooling but only eyeballs the diff is incomplete and should be re-run.

### Developer protocol

Every developer dispatch must:

1. Read this `## Engineering Principles` section before starting.
2. Run `pragmatiks-lint check` locally before opening a PR.
3. Resolve all 🚨 blockers from the lint pack. ⚠️ findings: address or justify in PR body.
4. State principle compliance in the callback to the supervisor.
