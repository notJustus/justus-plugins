---
name: frontend
description: Baut das Frontend (Vite + React oder Next.js, je nach .spec/planung.md) und verbindet es lokal mit dem Backend. Nutzen, wenn das Frontend angelegt, erweitert oder lokal gestartet wird.
---

# Frontend bauen

Grundlage: Frontend-Entscheidung und Routen-Tabelle aus `.spec/planung.md`. Das Backend läuft lokal auf `localhost:8080`.

## Festgelegt

| Bereich | Vite + React | Next.js |
|---|---|---|
| Gerüst | `npm create vite@latest frontend -- --template react-ts` | `npx create-next-app@latest frontend --ts --app --eslint --no-tailwind --use-npm --yes` |
| Routing | eine Route: keins. Mehrere: React Router | App Router (Ordner = Route) |
| Daten | `fetch` auf relative Pfade (`/api/...`), Funktionen gebündelt in `src/api.ts` | Server Component: `fetch` auf `${API_URL}/api/...` (`API_URL` Default `http://localhost:8080`, im Container `http://backend:8080`). Client Component: relative Pfade |
| Dev-Proxy `/api` → `localhost:8080` | `server.proxy` in `vite.config.ts` | `rewrites` in `next.config.ts` |

Für beide:
- Keine UI-Library, kein globaler State, plain CSS
- Filter und Sortierung in der URL (`?maxBeitrag=300`)
- Formulare: HTML-Attribute (`required`, `type="email"`, `min`), die eigentliche Validierung macht das Backend
- Nie `localhost:8080` im Browser-Code, damit es hinter Nginx ohne CORS funktioniert

## Ablauf

1. Branch `feature/frontend`.
2. Gerüst anlegen, Dev-Proxy eintragen.
3. Seiten nach der Routen-Tabelle bauen.
4. `npm run build` muss durchlaufen. Den Dev-Server nicht selbst starten.
5. Committen, pushen, PR öffnen. In die PR-Beschreibung: Startbefehl (`npm run dev`) und welche Seiten und Formulare zu prüfen sind. Der Nutzer startet und testet selbst im Browser.

## Fertig, wenn

Der Nutzer hat alle Seiten mit echten Daten gesehen (Network-Tab und Console ohne Fehler) und den PR gemergt.
