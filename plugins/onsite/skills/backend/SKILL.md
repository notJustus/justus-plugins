---
name: backend
description: Baut das Backend (Kotlin + Spring Boot, PostgreSQL) nach .spec/planung.md und startet es lokal. Nutzen, wenn das Backend angelegt, erweitert oder lokal gestartet wird.
---

# Backend bauen

Grundlage: Datenmodell und API aus `.spec/planung.md`.

## Festgelegt

| Bereich | Entscheidung |
|---|---|
| Skelett | Spring Initializr per `curl` (Ablauf Schritt 2), nicht frei generieren |
| Schema | Flyway-Migrationen (`db/migration/V1__....sql`), Hibernate `ddl-auto: validate` |
| Config | `application.yaml`, Werte per Env-Variable mit lokalen Defaults (`${DB_HOST:localhost}`), lokal `.env` per `spring.config.import: optional:file:../.env[.properties]` |
| Lokale DB | Service `db` in `docker-compose.yml` ohne `ports:`. Port 5432 nur in `docker-compose.local.yml`, aktiviert per `COMPOSE_FILE=docker-compose.yml:docker-compose.local.yml` in der lokalen `.env` |
| Starten | `./gradlew bootRun` gegen die Compose-DB. Nicht `bootTestRun` |
| Tests | werden mit dem Code geschrieben: pro Controller ein `@WebMvcTest` (Statuscodes, Validierung), ein `@SpringBootTest` mit Testcontainers als Durchstich gegen echtes Postgres |
| Packages | nach Schichten: `web/` (Controller, DTOs), `service/`, `repository/`, `domain/` (Entities) |
| Fehler | Bean Validation an DTOs + `@Valid` → 400. Eigene Exception im Service → per `@RestControllerAdvice` 404 |

## Ablauf

1. Branch `feature/backend`.
2. Skelett nach `backend/` holen (`packageName` passend zur Aufgabe):

   ```bash
   curl https://start.spring.io/starter.zip \
     -d type=gradle-project-kotlin -d language=kotlin -d javaVersion=21 \
     -d configurationFileFormat=yaml \
     -d dependencies=web,data-jpa,postgresql,flyway,validation,actuator,testcontainers \
     -d groupId=com.example -d artifactId=backend -d name=backend \
     -d packageName=com.example.<app> \
     -o backend.zip && unzip backend.zip -d backend && rm backend.zip
   ```

3. `docker-compose.yml` (nur `db`: `postgres:18`, Named Volume, Healthcheck mit `pg_isready`), `docker-compose.local.yml`, `.env.example`, lokale `.env`.
4. Migration `V1__...sql` aus dem Datenmodell, dann Entities, Repositories, Services, Controller nach der API-Tabelle, jeweils mit Tests.
5. `./gradlew build` (inkl. Tests, Docker muss laufen) muss grün sein. Die App nicht selbst starten, das macht der Nutzer.
6. Committen, pushen, PR öffnen.

## Fertig, wenn

`./gradlew build` ist grün, der Nutzer hat jeden Endpunkt per `curl` geprüft und den PR gemergt.
