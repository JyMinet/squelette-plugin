# Squelette — plugin for Claude

**Governance for AI-assisted projects.** *Who decides. Who does. What proves it's done.*

This plugin teaches Claude to work with [Squelette](https://github.com/JyMinet/squelette): a small controller that lives inside your repository, keeps the goal, the scope, the human decisions and the evidence of completion explicit, and **refuses** what a human did not authorize.

The rules stay in each governed repository (`AGENTS.md`, `docs/agent-governance/AGENTS.core.md`, `scripts/project_control.py`). The plugin only helps Claude find them and follow them; when the two disagree, the repository wins.

## What's inside

| Skill | Use it to |
|---|---|
| `/squelette:new-project` | start a new governed project from the latest public release of Squelette |
| `/squelette:guide` | work inside a governed repository — Claude also loads it on its own when it recognizes one |
| `/squelette:review` | review a governed project, or write a review mandate for another AI |

## Install

In Claude Code:

```text
/plugin marketplace add JyMinet/squelette-plugin
/plugin install squelette@squelette
```

## Requirements

Git and Python 3 (standard library only). The plugin runs nothing on its own: no hook, no server, no data sent anywhere. `new-project` downloads the public template from GitHub when you ask for a new project.

## What it is not

- Not a replacement for the rules in your repository.
- Not a prompt that makes Claude smarter: a way to keep a project on course while agents work on it.
- Not tied to Claude: Squelette itself works the same with Codex, ChatGPT, Gemini or any other agent. This plugin is the Claude-side helper.

## Version

Written for Squelette 3.21.1. The `guide` skill compares this version with the one of the repository it works in, and tells you when the plugin is behind.

## License

MIT — see [LICENSE](LICENSE).
