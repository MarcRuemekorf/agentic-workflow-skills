# Agentic Workflow Skills

## Installation

The skills ship as a plugin called `workflow`, for Claude Code and Codex CLI.

### Claude Code

```sh
claude plugin marketplace add MarcRuemekorf/agentic-workflow-skills
claude plugin install workflow@agentic-workflow-skills
```

Start a new session and type `/` to see the commands, e.g. `/define`. If another command already uses
the name, use the plugin's prefix: `/workflow:define`.

### Codex CLI

```sh
codex plugin marketplace add MarcRuemekorf/agentic-workflow-skills
codex plugin add workflow@agentic-workflow-skills
```

Start a new session and open `/skills` to see the skills, or type `$` followed by the skill name.

### Installing from a local clone

To try changes before pushing them, add your clone as the marketplace instead:

```sh
claude plugin marketplace add /path/to/agentic-workflow-skills
codex plugin marketplace add /path/to/agentic-workflow-skills
```

Then install as above. The plugin is copied when installed, so after editing the skills, update the
marketplace and reinstall:

```sh
claude plugin marketplace update agentic-workflow-skills
claude plugin install workflow@agentic-workflow-skills
```

### Uninstalling

```sh
claude plugin uninstall workflow
claude plugin marketplace remove agentic-workflow-skills

codex plugin remove workflow
codex plugin marketplace remove agentic-workflow-skills
```
