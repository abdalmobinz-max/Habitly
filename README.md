# Habitly Pro — Habit Tracker

A single-page habit tracking app with Google sign-in and cloud sync, built with Firebase (Authentication + Firestore). Track daily habits, see monthly trends, and pick up your data on any device once you're signed in.

## Features

- Google sign-in (Firebase Authentication)
- Cloud-synced data per user (Firestore) — no local-only mode
- Dashboard with daily / weekly / monthly completion stats
- Full habit tracker table by month
- Day-by-day history chart with a detail breakdown per day
- Editable habit list
- JSON backup export / import
- Autosaves every 15 seconds and on page close

## Tech stack

- Plain HTML / CSS / JavaScript (no build step, no framework)
- [Firebase](https://firebase.google.com/) — Authentication (Google provider) + Firestore

## Project structure

```
.
└── index.html   # the entire app: markup, styles, and logic
```

## Setup

### 1. Create a Firebase project

1. Go to the [Firebase console](https://console.firebase.google.com/) and create a new project.
2. Add a **Web app** to the project and copy the config object it gives you.

### 2. Enable Authentication

1. In the Firebase console, go to **Authentication → Sign-in method**.
2. Enable the **Google** provider.
3. Go to **Authentication → Settings → Authorized domains** and add every domain you'll run this on (e.g. `localhost`, your GitHub Pages domain, your custom domain).

### 3. Create Firestore

1. Go to **Firestore Database** and click **Create database** (production mode is fine).
2. Go to the **Rules** tab and set:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

This restricts every user to reading and writing only their own `users/{uid}` document.

### 4. Add your Firebase config to `index.html`

Open `index.html` and find this block near the top of the `<script type="module">` section:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

Replace the values with the config from your own Firebase web app (Project settings → General → Your apps).

> **Note:** A Firebase web `apiKey` is not a secret — it's safe to commit and expose in client-side code. Access to your data is controlled entirely by the Firestore security rules above, not by hiding this key.

## Running locally

No build step needed. Either:

- Open `index.html` directly in a browser, **or**
- Serve it with any static server, e.g.:

```bash
npx serve .
```

If you use Google sign-in locally, make sure `localhost` (and the port, if your setup requires it) is listed under Authorized domains in Firebase.

## Deploying

### GitHub Pages

1. Push this repo to GitHub.
2. Go to **Settings → Pages**, set the source to your default branch, and save.
3. Add the resulting `*.github.io` URL to **Authentication → Settings → Authorized domains** in Firebase.

### Firebase Hosting (alternative)

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

## Data model

Each signed-in user has one Firestore document at `users/{uid}`:

```json
{
  "profile": { "uid": "...", "displayName": "...", "email": "...", "photoURL": "..." },
  "habits": ["Read for 30 minutes", "Workout", "..."],
  "data": { "2026-09": { "0-3": 1, "2-3": 1 } },
  "lastSaved": "2026-09-03T12:00:00.000Z",
  "updatedAt": "<server timestamp>"
}
```

`data` keys are `"YYYY-MM"` → `"habitIndex-dayOfMonth"` → `1` (completed). Missing keys mean not completed.

## Backups

Use **Settings → Export backup** to download a JSON snapshot, and **Settings → Import** to restore one. Useful before making major changes or switching Firebase projects.

## License

Add a license of your choice (e.g. MIT) if you plan to make this repo public.
