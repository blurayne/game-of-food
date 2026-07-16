# Game of Food — Energie-Tag Tagesplaner

Interaktiver Tagesplaner (POC): Lebensmittel zusammenstellen, Energie- und Nährstoffbilanz live verfolgen.

## Live

Deployed via GitHub Pages: https://blurayne.github.io/game-of-food/

## Status

Proof of concept — a single self-contained `index.html` (responsive layout), no build step.

## Docs & Daten

- [`SPEC.md`](SPEC.md) — vollständige Spezifikation (Datenmodell, Mechaniken, Layout)
- [`lebensmittel.json`](lebensmittel.json) — Lebensmittel-Datenbasis (v10.0: 111 Zutaten, 16 Gerichte, Tagesbedarf)

## Deployment

Every push to `main` (or the current POC branch) runs `.github/workflows/deploy-pages.yml`,
which publishes the repository root to GitHub Pages.

One-time setup required in the repository settings:
**Settings → Pages → Build and deployment → Source: "GitHub Actions"**.
