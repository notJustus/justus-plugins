# Cheatsheet

1. [Lokal: Backend](#lokal-backend)
2. [Lokal: Frontend](#lokal-frontend)
3. [Docker Compose](#docker-compose)
4. [PostgreSQL](#postgresql)
5. [Git und GitHub](#git-und-github)
6. [Server einrichten](#server-einrichten)
7. [Deployment](#deployment)
8. [DNS und HTTPS](#dns-und-https)
9. [Linux](#linux)
10. [Debugging](#debugging)

## Lokal: Backend

```bash
docker compose up -d db                 # nur die DB starten (Port 5432 über docker-compose.local.yml)
cd backend && ./gradlew bootRun         # Backend auf :8080. Nach Code-Änderung neu starten
./gradlew test                          # Tests (Testcontainers: Docker muss laufen)
./gradlew build                         # kompilieren + testen + Jar
lsof -iTCP:8080 -sTCP:LISTEN -n -P      # Port belegt? Dann kill <PID>
```

```bash
curl -i localhost:8080/api/<ressource>
curl -i -X POST localhost:8080/api/<ressource> -H "Content-Type: application/json" -d '{"feld":"wert"}'
curl -i -X PATCH localhost:8080/api/<ressource>/1 -H "Content-Type: application/json" -d '{"feld":"wert"}'
curl -i -X DELETE localhost:8080/api/<ressource>/1
curl -s localhost:8080/actuator/health
```

| Status | heißt |
|---|---|
| 200 / 201 / 204 | OK / angelegt / ok ohne Body |
| 400 | Validierung (Backend-Log: `MethodArgumentNotValidException`) |
| 404 | ID unbekannt, oder Route fehlt (alter Code läuft noch?) |
| 415 | `Content-Type: application/json` fehlt |
| 500 | Exception, Stacktrace im Backend-Log |

## Lokal: Frontend

```bash
cd frontend
npm ci                                  # exakt nach Lockfile installieren
npm run dev                             # Vite :5173 / Next.js :3000, Proxy /api → :8080
npm run build                           # Artefakt: dist/ (Vite) oder .next/ (Next.js)
npm run lint
```

- DevTools: `Cmd+Opt+I`, Quelltext: `Cmd+Opt+U`
- Network → Filter **Fetch/XHR** → Request → Headers, Payload, Response
- CORS-Fehler in der Console, aber 200 im Network-Tab: URL im Code nicht relativ (`/api/...`)?

## Docker Compose

Im Ordner mit der `docker-compose.yml` ausführen.

| Befehl | macht |
|---|---|
| `docker compose up -d` | Sollzustand herstellen, im Hintergrund |
| `docker compose up -d --build` | vorher Images neu bauen (nach Code-Änderung) |
| `docker compose ps` | Status, `(healthy)`, Ports |
| `docker compose logs -f backend` | Logs live |
| `docker compose exec backend env \| grep DB_` | Config im Container prüfen |
| `docker compose restart frontend` | neu starten (z.B. nach Nginx-Config-Änderung) |
| `docker compose config` | zusammengeführte Config (mit `COMPOSE_FILE`) |
| `docker compose down` | Container weg, **Volume bleibt** |
| `docker compose down -v` | auch Volumes weg = **Daten weg** |

| Datei | geladen |
|---|---|
| `docker-compose.yml` | immer |
| `docker-compose.local.yml` | lokal: `.env` mit `COMPOSE_FILE=docker-compose.yml:docker-compose.local.yml` |
| `docker-compose.prod.yml` | Server: `.env` mit `COMPOSE_FILE=docker-compose.yml:docker-compose.prod.yml` |

```bash
docker ps -a                  # alle Container
docker images
docker system df              # Speicher
```

## PostgreSQL

```bash
docker compose exec db psql -U <user> -d <db>
```

```
\dt                  Tabellen
\d <tabelle>         Spalten, Typen, Constraints
\x                   breite Zeilen umbrechen
\q                   raus
SELECT * FROM flyway_schema_history;
```

```bash
docker compose exec db pg_dump -U <user> <db> > backup.sql
```

## Git und GitHub

```bash
git switch -c feature/<name>
git add -A && git commit -m "..."
git push -u origin feature/<name>
gh pr create --fill
gh pr checkout <nr>             # PR-Branch lokal holen
gh pr diff <nr>
git switch main && git pull     # nach dem Merge
git branch -D feature/<name>    # nach Squash-Merge
git log --oneline --graph --all
```

```bash
gh repo create <repo> --private --source=. --remote=origin --push
```

## Server einrichten

| # | wo | was | Befehl |
|---|---|---|---|
| 1 | Laptop | einloggen | `ssh <user>@<IP>` |
| 2 | Server | Updates | `apt update && apt upgrade -y` |
| 3 | Server | Docker | `curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh` |
| 4 | Server | Firewall prüfen | Cloud-Firewall: nur 22, 80, 443. Docker umgeht ufw |
| 5 | Server | Deploy Key | `ssh-keygen -t ed25519 -f ~/.ssh/deploy_<repo> -N ""` |
| 6 | GitHub | `.pub` eintragen | Repo → Settings → Deploy keys, **ohne** Write access |
| 7 | Server | `~/.ssh/config` | `Host github.com` / `IdentityFile ~/.ssh/deploy_<repo>` / `IdentitiesOnly yes` |
| 8 | Server | testen | `ssh -T git@github.com` |

```bash
ssh -v <user>@<IP>              # SSH-Phasen sehen
ssh-keygen -R <IP>              # alten known_hosts-Eintrag löschen
cat ~/.ssh/id_ed25519.pub       # Public Key anzeigen
```

## Deployment

```bash
git clone git@github.com:<user>/<repo>.git && cd <repo>
cp .env.example .env
openssl rand -hex 24            # Passwort erzeugen
nano .env                       # Werte + COMPOSE_FILE=docker-compose.yml:docker-compose.prod.yml
chmod 600 .env
docker compose up -d --build
docker compose ps
curl -i localhost/api/<ressource>
```

Update: `git pull && docker compose up -d --build`

## DNS und HTTPS

```bash
dig <sub>.<domain> +short             # zeigt der A-Record auf die IP?
dig @1.1.1.1 <sub>.<domain> +short    # anderen Resolver fragen
dig NS <domain> +short                # wo trägt man Records ein?
```

Reihenfolge: A-Record → `dig` ok → Port 80 offen → certbot → Nginx `listen 443 ssl` → `/etc/letsencrypt` read-only mounten → `ports: "443:443"`

```bash
apt install -y certbot
certbot certonly --standalone -d <sub>.<domain> -m <mail> --agree-tos \
  --pre-hook "docker stop <projekt>-frontend-1" --post-hook "docker start <projekt>-frontend-1"
certbot certificates
certbot renew --dry-run
curl -I http://<sub>.<domain>         # erwartet 301 → https
```

## Linux

```bash
ss -tlnp              # wer lauscht. 0.0.0.0 = von außen, 127.0.0.1 = nur lokal
free -h               # RAM, Spalte "available"
df -h /               # Platte
uname -m              # x86_64 / aarch64
systemctl status docker
journalctl -u docker -f
nano <datei>          # Ctrl+O speichern, Ctrl+X beenden
```

## Debugging

Der Request-Kette folgen. Zuerst Network-Tab: Kommt etwas an, und wer antwortet?

| Symptom | Ursache | prüfen |
|---|---|---|
| `Could not resolve host` | DNS | `dig` |
| Timeout | Firewall, falsche IP | `nc -zv <IP> 443` |
| Connection refused | niemand lauscht | `ss -tlnp`, `docker compose ps` |
| 404 mit `Server: nginx` | keine `location /api/`, Config nicht gemountet | `docker compose logs frontend` |
| 502 Bad Gateway | Nginx erreicht Backend nicht | `docker compose ps`, `logs backend` |
| 500 JSON von Spring | Exception | `docker compose logs backend` |
| 200 mit `[]` | DB leer | `psql` |
| Backend startet nicht: Connection refused 5432 | DB läuft nicht | `docker compose up -d db` |
| `Schema-validation: missing ...` | Entity passt nicht zur Migration | `V1__...sql` vs. `@Entity` |
| `host not found in upstream "backend"` | Nginx nicht im Compose-Netz | Compose statt `docker run` |
| Container `unhealthy` | Healthcheck schlägt fehl | `docker inspect <c> --format '{{json .State.Health}}'` |
