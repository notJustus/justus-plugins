# Cursor einrichten

Installieren: `brew install --cask cursor`

Ziel-Layout:

```
┌──────────┬──────────────────────────┬──────────────┐
│ Explorer │ Editor (Dateien)         │ Terminal mit │
│          │                          │ `claude`     │
│          ├──────────────────────────┤ (volle Höhe) │
│          │ Terminal                 │              │
└──────────┴──────────────────────────┴──────────────┘
```

1. Terminal öffnen, Rechtsklick auf den Terminal-Tab → "Move View" → Secondary Side Bar. Dort `claude` starten
2. `Cmd+Shift+P` → "Terminal: Create New Terminal in Editor Area", den Tab in die untere Hälfte des Editor-Bereichs ziehen
3. Rechtsklick auf die untere Gruppe → "Lock Group", damit Dateien oben aufgehen

Extensions: Prettier (`esbenp.prettier-vscode`), Kotlin (`fwcd.kotlin`)

`settings.json` (`Cmd+Shift+P` → "Preferences: Open User Settings (JSON)"):

```json
{
  "window.commandCenter": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "kotlin.languageServer.enabled": false,
  "kotlin.debugAdapter.enabled": false
}
```
