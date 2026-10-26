---
name: setup
description: Richtet einen fremden Mac (z.B. Leihgerät) für die Fullstack-Aufgabe ein. Prüft die Tools, installiert Fehlendes und verbindet GitHub per SSH. Nutzen zu Beginn auf einem neuen Rechner oder wenn der Nutzer einrichten will.
---

# Rechner einrichten

## 1. Prüfen

Selbst ausführen und als Tabelle zeigen (Tool | Version | ok / fehlt):

```bash
brew --version; git --version; gh --version
docker --version; docker compose version
java -version; node -v; npm -v
```

## 2. Fehlendes installieren

Dem Nutzer zeigen, was installiert wird und mit welchem Befehl. Erst nach seinem Okay ausführen. Befehle, die ein Passwort abfragen, tippt der Nutzer selbst mit `!` davor.

| Was | Befehl |
|---|---|
| Homebrew | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| Docker | `brew install --cask docker` |
| JDK 21 | `brew install --cask temurin@21` |
| Node 22 + npm | `brew install node@22` |
| gh CLI | `brew install gh` |

## 3. GitHub verbinden

Diese Befehle zum Kopieren ausgeben. Der Nutzer ergänzt Name und Mail und tippt selbst:

```bash
git config --global user.name "<Name>"
git config --global user.email "<GitHub-Mail>"
gh auth login    # GitHub.com → SSH → neuen Key erzeugen → im Browser bestätigen
```

Danach selbst prüfen: `gh auth status` und `ssh -T git@github.com`.

## 4. Server-Zugang

Hängt davon ab, wie der Server gestellt wird:
- Login per Key: Public Key anzeigen (`cat ~/.ssh/id_ed25519.pub`) und auf dem Server eintragen lassen
- Login per Passwort: `ssh <user>@<IP>`

## Editor

Nur wenn der Nutzer danach fragt: [cursor.md](cursor.md).
