# Sito web Crew+ (`docs/site`)

> **Stato: sospeso (set 2026)** — online solo documentazione legale per gli store.
> Endpoint attivi: `#privacy`, `#termini`, `#account-deletion`, `version.json`.
> Sito marketing archiviato in `marketing/`; riattivazione: `YES=1 make resume-site`.

Sorgente statica del sito (pubblicazione su **GitHub Pages** → `ivuplus.leogranata.it`).

| Percorso | Contenuto |
|----------|-----------|
| `index.html` | **Legale** (deploy attivo): privacy, termini, cancellazione dati |
| `marketing/index.html` | Home marketing completa (offline fino a `make resume-site`) |
| `marketing/en/` | Versione inglese marketing |
| `style.css` | Stili condivisi |
| `assets/banner-source.jpg` | Sorgente banner (solo build locale) |

**Repo di deploy**: [Leottantuno/IVUPlus-site](https://github.com/Leottantuno/IVUPlus-site) (branch `main`).

## Comandi

```bash
# Ottimizza logo, favicon, banner, og-image
make optimize-site-images

# Sincronizza docs/site → IVUPlus-site (preserva CNAME e version.json)
YES=1 PUSH=1 make deploy-site MESSAGE='feat(site): ...'   # default SITE_MODE=legal

# Solo endpoint legali (default dopo sospensione marketing)
YES=1 PUSH=1 make suspend-site

# Ripristina sito marketing completo
YES=1 PUSH=1 make resume-site
```

Script diretti: `scripts/site/optimize-images.sh`, `scripts/site/deploy-site.sh`.

## Deploy

1. Modifica file in `docs/site/`.
2. Incrementa il query param CSS/JS (`style.css?v=…`, `reveal.js?v=…`) se cambi stili o script.
3. `YES=1 PUSH=1 make deploy-site` — rsync con `--delete`, backup di file solo nel repo Pages.

File **non** presenti in `docs/site` ma preservati nel repo Pages:

| File | Ruolo |
|------|--------|
| `CNAME` | Dominio custom `ivuplus.leogranata.it` |
| `version.json` | Manifest controllo aggiornamenti in-app |

## `version.json`

URL pubblico: **https://ivuplus.leogranata.it/version.json**

L’app Flutter legge questo JSON (max 1 volta / 24 h) per invitare ad aggiornare dallo store. Supporta versioni indipendenti per **Android**, **iOS** e **macOS**. Non va committato in `docs/site`: vive solo nel repo **IVUPlus-site** e viene preservato ad ogni deploy.

### Schema multi-piattaforma

```json
{
  "latest": "1.19.4",
  "min_required": "1.0.0",
  "android_url": "https://play.google.com/store/apps/details?id=com.leogranata.ivuplus",
  "ios_url": "https://apps.apple.com/app/id6758614108",
  "macos_url": "https://apps.apple.com/app/id6758614108",
  "check_enabled": {
    "android": true,
    "ios": true,
    "macos": true
  },
  "platforms": {
    "android": {
      "latest": "1.19.4",
      "min_required": "1.0.0",
      "url": "https://play.google.com/store/apps/details?id=com.leogranata.ivuplus",
      "enabled": true
    },
    "ios": {
      "latest": "1.19.4",
      "min_required": "1.0.0",
      "url": "https://apps.apple.com/app/id6758614108",
      "enabled": true
    },
    "macos": {
      "latest": "1.19.4",
      "min_required": "1.0.0",
      "url": "https://apps.apple.com/app/id6758614108",
      "enabled": true
    }
  }
}
```

### Dopo ogni release store

```bash
# Aggiorna e pubblica direttamente su GitHub Pages
make sync-version-manifest PLATFORM=android PUSH=1
make sync-version-manifest PLATFORM=ios PUSH=1
make sync-version-manifest PLATFORM=macos PUSH=1
make sync-version-manifest PLATFORM=all PUSH=1
```

Documentazione completa (comportamento app, dialog obbligatorio/opzionale, go-live iOS):

→ **[docs/deployment/store-version-manifest.md](../deployment/store-version-manifest.md)**

Template: [docs/deployment/version.json.example](../deployment/version.json.example)

Codice app: `lib/features/app_update/`.

## Cache browser

Dopo modifiche a CSS/JS, incrementare `?v=YYYYMMDD` su `style.css` e `reveal.js` in `index.html` e `en/index.html`.

## Animazioni

`reveal.js` applica fade-in leggero a sezioni, feature card e passi «Come funziona». Con `prefers-reduced-motion: reduce` le animazioni sono disattivate.
