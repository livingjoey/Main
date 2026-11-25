# Manatee County Beach Patrol Scheduler — deploy package

Seven files, no folders, so it uploads cleanly to GitHub from any browser.

| File | What it is |
|---|---|
| `server.mjs` | The whole server in one file: sign-in, data storage, PDF export |
| `index.html` | The scheduler web app |
| `package.json` | Tells Render how to start it (`npm start`) |
| `render.yaml` | Render settings: server size, 1 GB data disk, secrets |
| `reset-admin.mjs` | Emergency admin password reset (`node reset-admin.mjs ADM01`) |
| `DEPLOY.md` | Step-by-step hosting guide |

Run it locally (Node 22.13 or newer): `SETUP_CODE=pick-one npm start`, then open http://localhost:3000.
