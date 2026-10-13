# MATSim Weekly Newsletter

Schickt jeden Montag eine E-Mail mit den Entwicklungen der letzten 7 Tage auf
[github.com/matsim-org](https://github.com/matsim-org):

- KI-Zusammenfassung auf Deutsch (TL;DR + thematische Abschnitte) via GitHub Models
- Kennzahlen, Releases, neue Repos, gemergte PRs (pro Repo), aktivste Repos, neue Issues, Contributors
- Bot-/Dependabot-PRs werden herausgefiltert und nur gezählt

> Hinweis: `github.com/matsim` ist ein fremder User-Account. Das MATSim-Projekt liegt unter `matsim-org`.

## Betrieb auf GitHub Actions (empfohlen, kein eigener Server nötig)

1. Diesen Ordner als (privates) Repository auf GitHub pushen.
2. Unter **Settings → Secrets and variables → Actions** folgende Secrets anlegen:

   | Secret      | Beispiel                     |
   |-------------|------------------------------|
   | `SMTP_HOST` | `smtp.gmail.com`             |
   | `SMTP_PORT` | `465` (SSL) oder `587` (STARTTLS) |
   | `SMTP_USER` | `dein.konto@gmail.com`       |
   | `SMTP_PASS` | App-Passwort des Mailkontos  |
   | `MAIL_FROM` | `MATSim Weekly <dein.konto@gmail.com>` |
   | `MAIL_TO`   | `ich@example.com` (mehrere kommagetrennt) |

   Bei Gmail: 2FA aktivieren und unter https://myaccount.google.com/apppasswords ein App-Passwort erzeugen.
3. **Actions → MATSim Weekly Newsletter → Run workflow** für einen ersten Test.

Der Workflow (`.github/workflows/newsletter.yml`) läuft montags um 06:00 UTC.
Für die KI-Zusammenfassung wird das automatische `GITHUB_TOKEN` mit `models: read` verwendet – kein zusätzlicher API-Key nötig.
Schlägt die Zusammenfassung fehl, kommt die Mail trotzdem (nur ohne KI-Teil).

> GitHub deaktiviert Cron-Workflows in Repos ohne Aktivität nach 60 Tagen; man erhält vorher eine Mail und kann sie mit einem Klick reaktivieren.

## Betrieb auf eigenem Server (Alternative)

Node.js ≥ 20 installieren, dann:

```bash
npm ci
# Crontab (crontab -e): montags 08:00
0 8 * * 1  cd /opt/matsim-newsletter && GH_TOKEN=ghp_xxx SMTP_HOST=... SMTP_PORT=587 SMTP_USER=... SMTP_PASS=... MAIL_TO=... node src/newsletter.mjs >> newsletter.log 2>&1
```

`GH_TOKEN` ist ein Personal Access Token (fine-grained, nur öffentlicher Lesezugriff + Berechtigung „Models: read“).

## Konfiguration

| Variable        | Default               | Bedeutung |
|-----------------|-----------------------|-----------|
| `GITHUB_ORG`    | `matsim-org`          | Beobachtete Organisation |
| `DAYS`          | `7`                   | Zeitraum in Tagen |
| `LLM_MODEL`     | `openai/gpt-4.1-mini` | Modell auf GitHub Models |
| `MODELS_TOKEN`  | = `GH_TOKEN`          | Separates Token für GitHub Models |
| `DISABLE_LLM`   | `false`               | KI-Zusammenfassung abschalten |
| `SMTP_SECURE`   | `true` bei Port 465   | TLS erzwingen |

## Lokale Vorschau

```bash
npm ci
GH_TOKEN=... npm run preview   # schreibt newsletter-preview.html, verschickt nichts
```
