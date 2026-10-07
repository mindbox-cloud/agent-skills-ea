# agent-skills-ea

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Plugins](https://img.shields.io/badge/plugins-1-blue)](#available-plugins)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin-orange)](https://claude.ai/code)

Early Access channel of the Claude Code plugins by [mindbox.cloud](https://mindbox.cloud/?locale=en_US). New skills and new versions land here before they reach the main marketplace, [agent-skills](https://github.com/mindbox-cloud/agent-skills).

> **Install either this marketplace or the main one, not both.** Both ship a plugin named `mindbox`, and with both installed its commands collide.

---

## Available Plugins

| Plugin | Description | Skill | Install |
|--------|-------------|-------|---------|
| [mindbox](./plugins/mindbox/) | Build marketing scenarios, audience filters and emails in a Mindbox project from plain-language requests: flow-create designs and fills a scenario, filter-build builds a platform-confirmed filter, filter-explain reads one back in business terms, email writes an email layout and email-ops previews it, saves it into a campaign and edits the campaign. Nothing is launched or sent to customers, and no filter is saved as a segment. | `mindbox:email`, `mindbox:email-ops`, `mindbox:filter-build`, `mindbox:filter-explain`, `mindbox:flow-create` | see below |

---

## Quick Install

```shell
/plugin marketplace add https://github.com/mindbox-cloud/agent-skills-ea
/plugin install <plugin-name>@mindbox-cloud-plugins-ea
```

See each plugin's README for available skills and usage.

---

## What's in this Repo

Early Access builds of the Claude Code plugins built by [mindbox.cloud](https://mindbox.cloud/?locale=en_US). Each plugin is:

- **Documented** — clear README, usage examples, changelog
- **English-only** — all content is in English for broad accessibility
- **MIT licensed** — free to use and adapt

Early Access content may change or be withdrawn before it reaches the main marketplace.

---

## Contributing

Read [CONTRIBUTING.md](./CONTRIBUTING.md) for the quality bar and process.

---

## License

[MIT](./LICENSE)
