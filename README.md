# ClawRecipes

ClawRecipes is a CLI-first recipe system for scaffolding specialist agents and teams from Markdown. It is built around durable, file-first workflows: recipes define roles, skills, workspace structure, and handoff patterns that can be inspected, versioned, and run locally.

## What it demonstrates

- Markdown-driven agent and team scaffolding
- File-first workspaces that play well with git
- CLI workflows for dispatch, tickets, assignment, handoff, and completion
- Practical orchestration patterns for repeatable AI-assisted work
- A companion path into ClawKitchen for users who prefer a visual interface

---

## Quickstart

### 1. Install

Preferred package install:

```bash
openclaw plugins install @jiggai/recipes

# If you use a plugin allowlist, explicitly trust recipes:
openclaw config get plugins.allow --json
openclaw config set plugins.allow --json '["memory-core","telegram","recipes"]'

openclaw gateway restart
openclaw plugins list
```

Install from GitHub:

```bash
git clone https://github.com/JIGGAI/ClawRecipes.git ~/clawrecipes
openclaw plugins install --link ~/clawrecipes
openclaw gateway restart
openclaw plugins list
```

### 2. List available recipes

```bash
openclaw recipes list
```

### 3. Scaffold a team

```bash
openclaw recipes scaffold-team development-team \
  --team-id development-team-team \
  --overwrite \
  --apply-config
```

### 4. Dispatch a request into work artifacts

```bash
openclaw recipes dispatch \
  --team-id development-team-team \
  --request "Add a new recipe for a customer-support team" \
  --owner lead
```

---

## Command surface

- `openclaw recipes list|show|status`
- `openclaw recipes scaffold`
- `openclaw recipes scaffold-team`
- `openclaw recipes install-skill <idOrSlug> [--yes] [--global|--agent-id <id>|--team-id <id>]`
- `openclaw recipes install <slug>`
- `openclaw recipes bind|unbind|bindings`
- `openclaw recipes dispatch ...`
- `openclaw recipes tickets|move-ticket|assign|take|handoff|complete`
- `openclaw recipes cleanup-workspaces`

For full details, see [docs/COMMANDS.md](docs/COMMANDS.md).

---

## Configuration

The plugin supports these config keys:

| Key | Default |
|---|---|
| `workspaceRecipesDir` | `recipes` |
| `workspaceAgentsDir` | `agents` |
| `workspaceSkillsDir` | `skills` |
| `workspaceTeamsDir` | `teams` |
| `autoInstallMissingSkills` | `false` |
| `confirmAutoInstall` | `true` |
| `cronInstallation` | `prompt` (`off`, `prompt`, `on`) |

Config schema is defined in `openclaw.plugin.json`.

---

## Documentation

| Doc | Purpose |
|---|---|
| [Installation](docs/INSTALLATION.md) | Install the plugin |
| [Agents & Skills](docs/AGENTS_AND_SKILLS.md) | Mental model and tool policies |
| [Tutorial](docs/TUTORIAL_CREATE_RECIPE.md) | Create your first recipe |
| [Commands](docs/COMMANDS.md) | Full command reference |
| [Team Workflow](docs/TEAM_WORKFLOW.md) | File-first workflow |
| [Architecture](docs/ARCHITECTURE.md) | Codebase structure |
| [Contributing](CONTRIBUTING.md) | Setup, tests, and PR workflow |

---

## Development

Run:

```bash
npm test
npm run test:coverage
npm run smell-check
```

Husky runs pre-commit hooks after `npm ci` installs them.

### Scaffold smoke test

A lightweight smoke check validates `scaffold-team` output contains the required testing workflow docs.

```bash
npm run test:smoke
# or
npm run scaffold:smoke
```

Notes:

- Creates a temporary `workspace-smoke-<timestamp>-team` under `~/.openclaw/`.
- If it does not delete cleanly, run: `openclaw recipes cleanup-workspaces --prefix smoke- --yes`.
- Exits non-zero on mismatch.
- Requires OpenClaw and workspace config.

---

## Workspace model

- Standalone agents: `~/.openclaw/workspace-<agentId>/`
- Teams: `~/.openclaw/workspace-<teamId>/` with `roles/<role>/...`
- Global skills: `~/.openclaw/skills/<skill>`
- Scoped skills: `~/.openclaw/workspace-*/skills/<skill>`
- Team IDs end with `-team`
- Agent IDs are namespaced: `<teamId>-<role>`
- Recipe template rendering is intentionally simple: `{{var}}` replacement only

---

## Removing a scaffolded team

ClawRecipes includes a confirmation-gated uninstall command.

```bash
openclaw recipes remove-team --team-id <teamId> --plan --json
openclaw recipes remove-team --team-id <teamId> --yes
openclaw gateway restart
```

Cron cleanup is conservative. It removes only cron jobs explicitly stamped with `recipes.teamId=<teamId>`.

Manual fallback: delete `~/.openclaw/workspace-<teamId>` and remove `<teamId>-*` agents from `agents.list[]` in `~/.openclaw/openclaw.json`.

---

## Current goals

- Daily shipping and pull requests for ClawRecipes features
- Improve recipes with more detailed agent files
- Add skill installation for agents through ClawKitchen
- Continue improving the file-first team workflow model

---

## License

ClawRecipes is licensed under **Apache-2.0**.

If you redistribute ClawRecipes or a derivative work, retain the license and attribution notices in `LICENSE` and `NOTICE`.

The license does not grant permission to use JIGGAI trademarks except as required for reasonable and customary attribution. See `TRADEMARK.md`.

Contributions are welcomed. By contributing, you agree that your contributions are licensed under the project’s Apache-2.0 license.
