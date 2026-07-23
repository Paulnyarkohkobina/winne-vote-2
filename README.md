# Student Council Election

A single-page ballot app for running a student council election: an admin
sets up positions and candidates, uploads a voter roster, and watches
results tally live; students verify with a student ID + password and vote
once each.

## ⚠️ Important limitation before you deploy

This app was adapted from a Claude artifact that used a built-in shared
storage API. On GitHub Pages there's no server, so this version stores
everything in the browser's `localStorage` instead (see the shim at the
top of the `<script>` in `index.html`).

**That means data is per-browser, per-device — it does not sync across
students' phones/laptops.** Concretely:

- If the admin sets up positions on their laptop, students visiting the
  site on their own devices will see an *empty, unconfigured* ballot.
- Votes cast on one device aren't visible to the admin viewing results on
  another device.

This setup works fine for:
- Local testing / development
- A single shared kiosk device (e.g. one laptop at a polling table, where
  the admin sets it up and every student votes on that same machine)

It is **not** suitable for a real election where students vote from their
own devices. For that, replace the storage shim with a real shared
backend — a few free options:
- **Firebase Realtime Database / Firestore** (generous free tier, easy JS SDK)
- **Supabase** (Postgres-backed, free tier, JS SDK)
- **A small serverless API** (e.g. Cloudflare Workers + KV, or a Google
  Apps Script backed by a Sheet)

The four functions to swap (`get`, `set`, `delete`, `list` on
`window.storage`) are isolated at the top of the script, so the rest of
the app's logic doesn't need to change — just point those four functions
at your backend instead of `localStorage`.

## Deploying to GitHub Pages

1. Create a new GitHub repository (public, so Pages can serve it for free).
2. Add this `index.html` (and this README, optionally) to the repo root.
3. Commit and push:
   ```bash
   git init
   git add .
   git commit -m "Add student council election app"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<your-repo>.git
   git push -u origin main
   ```
4. In the repo on GitHub: **Settings → Pages → Source**, select the `main`
   branch and `/ (root)` folder, then save.
5. GitHub will give you a URL like
   `https://<your-username>.github.io/<your-repo>/` within a minute or two.

## Local testing

Just open `index.html` directly in a browser, or serve it locally:

```bash
python3 -m http.server 8000
```

then visit `http://localhost:8000`.

## Using the app

1. Open the site and click **Election admin**. First visit creates an
   admin passcode (min. 4 characters) — share it only with election staff.
2. Under **Set up positions**, add each office (e.g. President) and its
   candidates. You can attach a photo per candidate (uploaded image or a
   pasted URL).
3. Under **Voter roster**, add voters one at a time or bulk-import a CSV
   with one line per voter: `student id,full name,password`.
4. Students go to **Vote now**, verify with their student ID + password,
   and vote once per position.
5. The admin's **Live results** tab shows running vote counts and
   percentages per candidate.

## Notes

- Passwords are hashed (SHA-256) before storage, but this is a lightweight
  scheme suited to a low-stakes classroom election — not a substitute for
  real authentication if the stakes are higher.
- Candidate photos are resized and stored as compressed data URLs, so no
  external image hosting is required for uploaded photos.
