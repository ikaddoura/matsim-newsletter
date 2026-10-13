# MATSim Weekly Newsletter

Schickt jeden Montag eine E-Mail mit den Entwicklungen der letzten 7 Tage auf
[github.com/matsim-org](https://github.com/matsim-org):

- KI-Zusammenfassung auf Deutsch (TL;DR + thematische Abschnitte) via GitHub Copilot CLI
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
   | `COPILOT_TOKEN` | Fine-grained PAT mit Berechtigung **„Copilot Requests“** ([anlegen](https://github.com/settings/personal-access-tokens/new)) |

   Bei Gmail: 2FA aktivieren und unter https://myaccount.google.com/apppasswords ein App-Passwort erzeugen.
3. **Actions → MATSim Weekly Newsletter → Run workflow** für einen ersten Test
   (Häkchen `dry_run` = nur Vorschau als Artefakt, kein Versand).

Der Workflow (`.github/workflows/newsletter.yml`) läuft montags um 06:00 UTC.
Die KI-Zusammenfassung läuft über die Copilot CLI (verbraucht Premium-Requests deines Copilot-Abos).
Optional kann per Repository-Variable `LLM_MODEL` ein Modell gewählt werden.
Schlägt die Zusammenfassung fehl, kommt die Mail trotzdem (nur ohne KI-Teil).
Die Copilot CLI läuft ohne Shell-/Schreibrechte in einem leeren Verzeichnis, da PR-Texte von Dritten stammen.

> GitHub deaktiviert Cron-Workflows in Repos ohne Aktivität nach 60 Tagen; man erhält vorher eine Mail und kann sie mit einem Klick reaktivieren.

## Betrieb auf eigenem Server (Alternative)

Node.js ≥ 20 installieren, dann:

```bash
npm ci
npm install -g @github/copilot
# Crontab (crontab -e): montags 08:00
0 8 * * 1  cd /opt/matsim-newsletter && GH_TOKEN=ghp_xxx COPILOT_GITHUB_TOKEN=github_pat_xxx SMTP_HOST=... SMTP_PORT=587 SMTP_USER=... SMTP_PASS=... MAIL_TO=... node src/newsletter.mjs >> newsletter.log 2>&1
```

`GH_TOKEN`: beliebiges Token mit Lesezugriff auf öffentliche Repos (für höhere API-Limits).

## Konfiguration

| Variable        | Default               | Bedeutung |
|-----------------|-----------------------|-----------|
| `GITHUB_ORG`    | `matsim-org`          | Beobachtete Organisation |
| `DAYS`          | `7`                   | Zeitraum in Tagen |
| `LLM_PROVIDER`  | `copilot`             | `copilot`, `openai` (OpenAI-kompatible API) oder `none` |
| `LLM_MODEL`     | CLI-Default           | Modell |
| `LLM_API_URL` / `LLM_API_KEY` | –       | Für `openai`, z.B. `https://api.openai.com/v1/chat/completions` |
| `COPILOT_BIN`   | `copilot`             | Pfad zur Copilot CLI |
| `DISABLE_LLM`   | `false`               | KI-Zusammenfassung abschalten |
| `SMTP_SECURE`   | `true` bei Port 465   | TLS erzwingen |

## Lokale Vorschau

```bash
npm ci
GH_TOKEN=... npm run preview   # schreibt newsletter-preview.html, verschickt nichts
```
