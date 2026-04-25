# ClawRecipes

> A CLI-first recipe system for scaffolding specialist agents and teams from Markdown

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![CI](https://img.shields.io/github/actions/workflow/status/JIGGAI/ClawRecipes/ci.yml?branch=main)](https://github.com/JIGGAI/ClawRecipes/actions)
[![Version](https://img.shields.io/npm/v/@jiggai/recipes)](https://www.npmjs.com/package/@jiggai/recipes)

**ClawRecipes** is an OpenClaw plugin that enables markdown-driven agent and team scaffolding. Define reusable agent/team templates (recipes) in Markdown, then use the CLI to scaffold workspaces, dispatch tickets, and orchestrate multi-agent workflows—all with a file-first, git-friendly architecture.

---

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Quickstart](#quickstart)
- [Command Reference](#command-reference)
- [Configuration](#configuration)
- [Workspace Model](#workspace-model)
- [Bundled Recipes](#bundled-recipes)
- [Documentation](#documentation)
- [Development](#development)
- [Contributing](#contributing)
- [License](#license)
- [Support](#support)

---

## Features

- **Markdown-driven scaffolding**: Define agent and team recipes in plain Markdown with frontmatter configuration
- **File-first workspaces**: All artifacts live on disk for easy inspection, version control, and diffing
- **CLI workflows**: Dispatch, ticket management, assignment, handoff, and completion via `openclaw` commands
- **Team orchestration**: Multi-agent workflows with role-based routing and handoff patterns
- **ClawKitchen integration**: Companion visual editor for users who prefer a GUI
- **Extensible**: Install skills per-agent or globally; create custom recipes for your organization

---

## Prerequisites

- **Node.js 20+** (for development)
- **OpenClaw** (CLI tool for running the plugin)

---

## Installation

### Install via OpenClaw plugin manager (recommended)

```bash
# Install the plugin
openclaw plugins install @jiggai/recipes

# If you use a plugin allowlist, explicitly trust recipes:
openclaw config get plugins.allow --json
openclaw config set plugins.allow --json '["memory-core","telegram","recipes"]'

# Restart the gateway
openclaw gateway restart

# Verify installation
openclaw plugins list
```

### Install from source (for development)

```bash
git clone https://github.com/JIGGAI/ClawRecipes.git ~/clawrecipes
cd ~/clawrecipes
npm ci

# Link the plugin into OpenClaw
openclaw plugins install --link ~/clawrecipes

# Restart the gateway
openclaw gateway restart

# Verify installation
openclaw plugins list
```

---

## Quickstart

### 1. List available recipes

```bash
openclaw recipes list
```

### 2. Scaffold a team

```bash
openclaw recipes scaffold-team development-team \
  --team-id development-team-team \
  --overwrite \
  --apply-config
```

This creates a team workspace at `~/.openclaw/workspace-development-team-team/` with roles, agent configs, and skill bindings defined by the `development-team` recipe.

### 3. Dispatch a request into work artifacts

```bash
openclaw recipes dispatch \
  --team-id development-team-team \
  --request "Add a new recipe for a customer-support team" \
  --owner lead
```

This creates a ticket and routes it through the team's workflow. Agents pick up tickets based on their assigned lanes.

---

## Command Reference

| Command | Description |
|---------|-------------|
| `openclaw recipes list` | List all available recipes |
| `openclaw recipes show <slug>` | Show details of a specific recipe |
| `openclaw recipes scaffold <slug>` | Scaffold a single agent from a recipe |
| `openclaw recipes scaffold-team <slug>` | Scaffold an entire team from a recipe |
| `openclaw recipes install-skill <id\|slug>` | Install a skill for an agent, team, or globally |
| `openclaw recipes install <slug>` | Install a recipe plugin from the marketplace |
| `openclaw recipes bindings` | List current skill bindings |
| `openclaw recipes bind <agent\|team> <skill>` | Bind a skill to an agent or team |
| `openclaw recipes unbind <agent\|team> <skill>` | Remove a skill binding |
| `openclaw recipes dispatch` | Create and route a ticket to the appropriate agent |
| `openclaw recipes tickets` | List all tickets in the workspace |
| `openclaw recipes assign <ticket> <agent>` | Assign a ticket to a specific agent |
| `openclaw recipes take <ticket>` | An agent takes ownership of a ticket |
| `openclaw recipes handoff <ticket> <agent>` | Hand a ticket off to another agent |
| `openclaw recipes complete <ticket>` | Mark a ticket as complete |
| `openclaw recipes move-ticket <ticket> <status>` | Change ticket status |
| `openclaw recipes cleanup-workspaces` | Remove stale or completed workspaces |

For full command details, see [docs/COMMANDS.md](docs/COMMANDS.md).

---

## Configuration

The plugin respects these OpenClaw configuration keys (defined in `openclaw.plugin.json`):

| Key | Default | Description |
|-----|---------|-------------|
| `workspaceRecipesDir` | `recipes` | Directory containing recipe Markdown files |
| `workspaceAgentsDir` | `agents` | Directory for agent workspaces |
| `workspaceSkillsDir` | `skills` | Directory for skill installations |
| `workspaceTeamsDir` | `teams` | Directory for team workspaces |
| `autoInstallMissingSkills` | `false` | Automatically install missing skills when binding |
| `confirmAutoInstall` | `true` | Prompt before auto-installing missing skills |
| `cronInstallation` | `prompt` | Cron job install mode: `off`, `prompt`, or `on` |

Settings can be adjusted via `openclaw config set <key> <value>`.

---

## Workspace Model

ClawRecipes uses a file-first architecture for maximum transparency and git-friendliness:

```
~/.openclaw/
├── workspace-<agentId>/        # Standalone agent workspace
│   └── ...
├── workspace-<teamId>/         # Team workspace
│   └── roles/
│       └── <role>/
│           └── ...
├── skills/
│   ├── <skill>/                # Global skill (shared across all agents)
│   └── workspace-*/skills/     # Workspace-scoped skill
└── agents.list                 # Registry of active agents
```

- **Agent IDs** are namespaced: `<teamId>-<role>` (e.g., `development-team-team-lead`)
- **Team IDs** end in `-team`
- **Recipe templates** use simple `{{var}}` replacement—no complex logic
- Workspaces are fully self-contained and portable

---

## Bundled Recipes

ClawRecipes ships with production-ready recipes for common use cases:

| Recipe | Type | Purpose |
|--------|------|---------|
| `development-team` | Team | Full-stack development team (lead, frontend, backend, QA) |
| `product-team` | Team | Product management trio (pm, designer, researcher) |
| `business-team` | Team | Business operations (analyst, marketing, ops) |
| `customer-support-team` | Team | Customer support workflow |
| `law-firm-team` | Team | Legal practice (partner, associate, paralegal) |
| `financial-planner-team` | Team | Financial planning (planner, analyst) |
| `crypto-trader-team` | Team | Cryptocurrency trading desk |
| `stock-trader-team` | Team | Stock trading desk |
| `social-team` | Team | Social media management |
| `clinic-team` | Team | Medical clinic (doctor, nurse) |
| `construction-team` | Team | Construction project team |
| `research-team` | Team | Research collaboration (lead, scientists) |
| `developer` | Agent | Solo developer agent |
| `editor` | Agent | Content editor agent |
| `project-manager` | Agent | Project manager agent |
| `researcher` | Agent | Research agent |
| `swarm-orchestrator` | Agent | Coordinates multiple agents |

See [docs/BUNDLED_RECIPES.md](docs/BUNDLED_RECIPES.md) for full details and customization tips.

---

## Documentation

| Document | Purpose |
|----------|---------|
| [Installation](docs/INSTALLATION.md) | Detailed plugin installation options and troubleshooting |
| [Agents & Skills](docs/AGENTS_AND_SKILLS.md) | Mental model and tool policies |
| [Tutorial: Create a Recipe](docs/TUTORIAL_CREATE_RECIPE.md) | Step-by-step guide to authoring recipes |
| [Commands](docs/COMMANDS.md) | Full CLI command reference |
| [Team Workflow](docs/TEAM_WORKFLOW.md) | File-first multi-agent workflow patterns |
| [Architecture](docs/ARCHITECTURE.md) | Codebase structure and data flow |
| [Recipe Format](docs/RECIPE_FORMAT.md) | Recipe schema and template syntax |
| [Contributing](CONTRIBUTING.md) | Setup, tests, and PR workflow |
| [Releasing](docs/releasing.md) | Maintainer guide to publishing releases |

---

## Development

### Setup

```bash
git clone https://github.com/JIGGAI/ClawRecipes.git
cd ClawRecipes
npm ci
```

### Run tests

```bash
npm test                    # Unit tests
npm run test:coverage       # With coverage report
npm run smoke               # Scaffold smoke test (requires OpenClaw)
```

### Linting and quality

```bash
npm run lint                # ESLint
npm run lint:fix            # ESLint with auto-fix
npm run smell-check         # ESLint + jscpd + pattern checks
```

Pre-commit hooks (husky + lint-staged) run automatically after `npm ci`.

---

## Contributing

We welcome contributions! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for setup instructions, testing requirements, and the PR workflow.

Key points:
- Run `npm test` and `npm run smell-check` before submitting a PR
- Ensure lint-staged passes (ESLint on staged `.ts` files)
- Fork the repo and open PRs against the `main` branch

---

## License

ClawRecipes is licensed under the **Apache-2.0** license.

```
Copyright (c) JIGGAI

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

See [LICENSE](LICENSE) for full text.

**Trademark**: The license does not grant permission to use JIGGAI trademarks except as required for reasonable and customary attribution. See [TRADEMARK.md](TRADEMARK.md).

---

## Support

- **Documentation**: [github.com/JIGGAI/ClawRecipes/docs](https://github.com/JIGGAI/ClawRecipes/tree/main/docs)
- **Issues**: [github.com/JIGGAI/ClawRecipes/issues](https://github.com/JIGGAI/ClawRecipes/issues)
- **OpenClaw Community**: Join the conversation on Slack

---

Built for [Steven Lees](https://australianai.slack.com/archives/D0AP0TCVB7A/p1777096587904099) by [Kilo for Slack](https://kilo.ai/features/slack-integration)
