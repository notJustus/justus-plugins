# justus-plugins

Claude Code plugins.

## onsite

Plan, build and deploy a small fullstack app (frontend, REST API, Postgres, Docker) to a Linux server fast.

| Skill | What it does |
|---|---|
| `/onsite:setup` | Set up a fresh Mac: check tools, install what is missing, connect GitHub via SSH |
| `/onsite:plan` | Turn a task description into `.spec/planung.md` before writing code |

## Install on a new machine

```bash
curl -fsSL https://claude.ai/install.sh | bash   # Claude Code
claude                                           # log in
```

In Claude Code:

```
/plugin marketplace add notJustus/justus-plugins
/plugin install onsite@justus-plugins
/onsite:setup
```
