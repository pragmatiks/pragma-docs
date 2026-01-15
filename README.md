<p align="center">
  <img src="assets/wordmark.png" alt="Pragma-OS" width="800">
</p>

# Pragma-OS Documentation

[![Docs](https://img.shields.io/badge/docs-online-brightgreen)](https://docs.pragmatiks.io)
[![Mintlify](https://img.shields.io/badge/built%20with-Mintlify-0D9373)](https://mintlify.com)

**[View Documentation](https://docs.pragmatiks.io)** | **[SDK](https://github.com/pragmatiks/pragma-sdk)** | **[CLI](https://github.com/pragmatiks/pragma-cli)** | **[Providers](https://github.com/pragmatiks/pragma-providers)**

Official documentation for the pragma-os platform.

## Documentation Structure

| Section | Description |
|---------|-------------|
| **Getting Started** | Prerequisites, quickstart, and setup verification |
| **Concepts** | Core concepts: resources, providers, reactive dependencies |
| **Guides** | Error recovery, migration guides |
| **CLI Reference** | Command-line interface documentation |
| **SDK Reference** | Python SDK for building providers |
| **Building Providers** | Complete guide to creating custom providers |
| **API Reference** | REST API documentation |

## Local Development

Install the Mintlify CLI:

```bash
npm i -g mintlify
```

Start the local development server:

```bash
mintlify dev
```

View your changes at `http://localhost:3000`.

## Project Structure

```
docs/
├── docs.json              # Navigation and theme configuration
├── index.mdx              # Landing page
├── getting-started/       # Onboarding guides
├── concepts/              # Core concepts
├── guides/                # How-to guides
├── cli/                   # CLI reference
├── sdk/                   # SDK reference
├── building-providers/    # Provider development
├── api-reference/         # API documentation
└── learn/                 # Platform overview and comparisons
```

## Writing Documentation

- Use MDX format with YAML frontmatter
- Every page requires `title` and `description` in frontmatter
- Use relative paths for internal links
- Test all code examples before publishing
- Follow existing patterns for consistency

## Publishing

Changes pushed to `main` are automatically deployed via the Mintlify GitHub integration.

## License

MIT
