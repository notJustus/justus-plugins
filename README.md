# justus-plugins

Claude Code plugins.

## onsite

Plan, build and deploy a small fullstack app (frontend, REST API, Postgres, Docker) to a Linux server fast.

| Skill | What it does |
|---|---|
| `/onsite:setup` | Set up a fresh Mac: check tools, install what is missing, connect GitHub via SSH |
| `/onsite:plan` | Turn a task description into `.spec/planung.md` before writing code |
| `/onsite:backend` | Build the Kotlin + Spring Boot backend from the plan and run it locally |
| `/onsite:frontend` | Build the Vite + React or Next.js frontend and connect it to the backend |
| `/onsite:cheatsheet` | Commands for local dev, Compose, server, DNS, HTTPS, debugging ([read on GitHub](plugins/onsite/skills/cheatsheet/cheatsheet.md)) |

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
