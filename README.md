# MATSim Weekly

Statische Website mit Wochen- und Monatsüberblicken über die Entwicklungen auf
[github.com/matsim-org](https://github.com/matsim-org), gehostet über GitHub Pages.

- **Pro Kalenderwoche** (ISO-KW, Mo–So) eine Seite unter `docs/weeks/2026-W41.html`
- **Pro Monat** ein Monatsrückblick unter `docs/months/2026-09.html`
- **Startseite** mit neuester Woche und Archiv aller Wochen/Monate; alte Seiten bleiben erhalten
- Inhalt: KI-Zusammenfassung auf Deutsch + Kennzahlen, Releases, neue Repos, gemergte PRs, aktivste Repos, neue Issues, Contributors.
  Bot-/Dependabot-PRs werden nur gezählt.
- Rohdaten pro Periode in `docs/data/` (daraus werden alle Seiten bei jedem Lauf neu gerendert)

## Automatik

`.github/workflows/site.yml`:

| Zeitpunkt | Aktion |
|-----------|--------|
| Montags 05:00 UTC | Seite für die abgeschlossene Vorwoche |
| Am 1. des Monats 05:30 UTC | Monatsrückblick für den Vormonat |

Danach werden die Seiten ins Repo committet und auf GitHub Pages deployed.
Manuell: **Actions → MATSim Weekly Site → Run workflow** (`week`, `month` oder `rebuild`, optional mit IDs wie `2026-W40 2026-W41` oder `2026-09` zum Nachholen).

## KI-Zusammenfassung / Tokens

- Die Zusammenfassung läuft über die **GitHub Copilot CLI** und verbraucht Premium-Requests deines Copilot-Abos (~5–6 pro Monat).
- Dafür einmalig ein Fine-grained PAT mit Berechtigung **„Copilot Requests“** anlegen
  ([hier](https://github.com/settings/personal-access-tokens/new)) und als Repository-Secret `COPILOT_TOKEN` speichern.
- Optional per Repository-Variable `LLM_MODEL` ein Modell wählen.
- Das automatische `GITHUB_TOKEN` wird nur zum Lesen der GitHub-Daten und zum Committen genutzt.
- Ohne `COPILOT_TOKEN` (oder bei Fehlern) entstehen die Seiten trotzdem, nur ohne KI-Teil.
- Die Copilot CLI läuft ohne Shell-/Schreibrechte in einem leeren Verzeichnis, da PR-Texte von Dritten stammen.

## Lokal ausführen

```bash
npm install -g @github/copilot   # einmalig, danach `copilot` zum Anmelden
node src/site.mjs week            # letzte abgeschlossene Woche
node src/site.mjs week 2026-W38   # bestimmte Woche(n)
node src/site.mjs month 2026-09   # bestimmter Monat
node src/site.mjs rebuild         # nur neu rendern (z.B. nach Layout-Änderungen)
```

## Konfiguration (Umgebungsvariablen)

| Variable        | Default        | Bedeutung |
|-----------------|----------------|-----------|
| `GITHUB_ORG`    | `matsim-org`   | Beobachtete Organisation |
| `GH_TOKEN` / `GITHUB_TOKEN` | –  | Für höhere GitHub-API-Limits |
| `LLM_PROVIDER`  | `copilot`      | `copilot`, `openai` (OpenAI-kompatible API) oder `none` |
| `LLM_MODEL`     | CLI-Default    | Modell |
| `LLM_API_URL` / `LLM_API_KEY` | – | Für `openai`, z.B. `https://api.openai.com/v1/chat/completions` |
| `COPILOT_BIN`   | `copilot`      | Pfad zur Copilot CLI |
| `DISABLE_LLM`   | `false`        | KI-Zusammenfassung abschalten |
| `OUT_DIR`       | `docs`         | Ausgabeverzeichnis |

> Hinweis: `github.com/matsim` ist ein fremder User-Account; MATSim liegt unter `matsim-org`.
