---
name: plan
description: Plant eine Fullstack-Aufgabe (Frontend, HTTP-API, relationale DB, Docker, Deployment auf Linux) vor der Implementierung. Füllt das Planungs-Template als .spec/planung.md. Nutzen, wenn eine neue Aufgabenstellung vorliegt oder der Nutzer planen will.
---

# Aufgabe planen

Ziel: `.spec/planung.md` im Projekt-Root als Bauplan für die Implementierung. **In diesem Schritt keinen Code schreiben.**

## Ablauf

1. Aufgabenstellung lesen (vom Nutzer eingefügt oder als Datei). Fehlt sie, danach fragen.
2. [template.md](template.md) lesen und als `.spec/planung.md` im Projekt-Root füllen.
3. Jede Annahme, die nicht direkt aus der Aufgabe folgt, mit **[Annahme]** markieren.
4. Unklarheiten, die das Design ändern würden, als Rückfragen ans Team sammeln (nur im Chat).
5. Im Chat kurz zusammenfassen:
   - die 3 wichtigsten Entscheidungen
   - alle [Annahme]-Stellen als nummerierte Liste
   - die Rückfragen ans Team

   Dann auf das Feedback des Nutzers warten.
6. Feedback in `.spec/planung.md` einarbeiten, bis der Nutzer zufrieden ist. Erst dann implementieren.

## Leitplanken

- **Scope klein halten:** Das Ganze inkl. Deployment muss in ca. 4 Stunden fertig sein. Lieber wenige Must-haves sauber, Rest als Nice-to-have.
- **"Festgelegt" im Template gilt.** Nur die offenen Abschnitte füllen.
