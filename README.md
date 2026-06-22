# The Cellar — Personal Drinks Collection

A zero-cost static web app for cataloguing a spirits/drinks collection, hosted on
GitHub Pages. No build step, no backend, no paid services. Data lives in
`cellar.json` in this repo; every edit from the owner panel is a Git commit made
through the GitHub Contents API.

## Files

| File | Purpose |
|------|---------|
| `cellar.json` | The collection data (the single source of truth). |
| `guest.html` | Public, read-only tasting menu. Speakeasy theme. Shows only bottles with `public: true`. |
| `manage-7c4e9a1f.html` | **Owner panel** (deliberately obscure filename). Password-gated. Full CRUD. |
| `images/` | Bottle photos, committed here as `{id}.jpg`. |

## One-time setup

1. **Create a repo** and push these files to it.
2. **Enable GitHub Pages**: repo → Settings → Pages → Build from branch → `main` / root.
   Your site will be at `https://<owner>.github.io/<repo>/`.
3. **Create a Personal Access Token (classic)** with the **`repo`** scope:
   GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic).
4. Open the owner panel: `https://<owner>.github.io/<repo>/manage-7c4e9a1f.html`
   - Password: **`cellar2025`**
   - On first load it asks for `owner/repo`, branch, and your token. These are stored
     in this browser's `localStorage` only — never committed.

## Using it

- **Guest menu**: `https://<owner>.github.io/<repo>/guest.html` — share this freely.
  Tap a bottle to reveal its tasting notes.
- **Owner panel**: Add / edit / delete bottles, optional barcode autofill
  (OpenFoodFacts), optional photo (resized client-side to 800px, committed to `images/`).
  Use the **▣** button for a downloadable guest-menu QR code, **⚙** for settings,
  **⏏** to lock.

## Changing the password

The login compares a SHA-256 hash hardcoded in the owner panel JS (buried among decoy
constants). Default `cellar2025` →
`19185c71bc538026392aa9f6df9d4bb0895ecefb392c43911fa2223d825dafed`.

To change it, hash your new password and replace the operative constant (`_0xf2`):

```bash
printf '%s' 'your-new-password' | sha256sum
```

> **Security note:** this is a deterrent against casual snooping, not real security —
> anyone can read the client-side hash. The real protection on your data is the GitHub
> token, which is never stored in the repo. Treat the password as a soft gate.

## Notes / limitations

- The owner panel needs an https origin (or `localhost`) for the Web Crypto API used
  to hash the password. GitHub Pages is https, so this is fine in production.
- Each save/delete is a separate commit. Saves re-fetch `cellar.json` first and retry
  once on a `409` conflict.
- All GitHub API errors (bad token, missing scope, 404 repo/branch, rate limit,
  conflict) surface as on-screen toasts.
- The only third-party JS is `qrcodejs` loaded from cdnjs (for offline-capable QR
  generation, no external QR service).
