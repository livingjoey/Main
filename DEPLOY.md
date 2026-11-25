# Put the Beach Patrol Scheduler on the web (Render)

This puts the scheduler on its own web address, for example
`https://beach-patrol-scheduler.onrender.com`. Your admins sign in from any browser, all of them
see the same schedule, and it updates live.

**What it costs:** about **$7.25 a month** on Render. That's $7 for the smallest always-on server plus $0.25 for a 1 GB data disk.
A paid plan is needed because Render's free plan can't keep files and goes to sleep when nobody is using it.

**Time:** about 20 minutes. You need an email address and a payment card.

---

## Step 1 — Unzip the project

Unzip `beach-patrol-scheduler-deploy.zip`. You'll get a folder called `beach-patrol-scheduler` holding **7 files and no folders**:

`server.mjs` · `index.html` · `package.json` · `render.yaml` · `reset-admin.mjs` · `DEPLOY.md` · `README.md`

## Step 2 — Put the files on GitHub

Render installs apps from GitHub, a free site that stores code.

1. Go to **github.com** and create a free account (or sign in).
2. Click **+** (top right) → **New repository**.
   - Repository name: `beach-patrol-scheduler`
   - Choose **Private**.
   - Leave everything else as is → **Create repository**.
3. On the next page, click the link **uploading an existing file**.
4. Open the `beach-patrol-scheduler` folder on your computer, select **all 7 files** (Cmd+A on a Mac, Ctrl+A on Windows), and drag them onto the GitHub page. There are no folders, so any browser works.
5. Wait until all 7 files are listed → **Commit changes**.

> Check: the repository's main page should list `server.mjs`, `index.html`, `package.json` and `render.yaml` at the top level. Files left over from an earlier upload (like `tsconfig.json`) are harmless.

## Step 3 — Create the app on Render

1. Go to **render.com** → **Get Started** → sign up **with GitHub** (easiest).
2. In the Render dashboard click **New** → **Blueprint**.
3. Connect your GitHub account if asked, and give Render access to the `beach-patrol-scheduler` repository.
4. Select that repository. Render reads the `render.yaml` file and shows one web service,
   **beach-patrol-scheduler**, with a 1 GB disk.
5. Give the Blueprint a name (anything, e.g. "Beach Patrol") → **Deploy Blueprint** (or **Apply**).
   Add a payment card when asked.
6. Wait 3–5 minutes. Open the service → **Logs**. When you see
   `Beach Patrol Scheduler running`, it's live.

## Step 4 — Get your setup code

So nobody else can claim your scheduler before you do, the first setup asks for a one-time code.

1. In Render, open **beach-patrol-scheduler** → **Environment**.
2. Find **SETUP_CODE**, reveal it and copy it.

## Step 5 — Open it and set it up

1. At the top of the service page, click the web address (ending in `.onrender.com`).
2. You'll see **Set up the Beach Patrol Scheduler**:
   - Patrol name
   - First admin identifier (e.g. `ADM01`), display name and password
   - **Setup code**: paste the code from Step 4
   - Optionally tick the box for 76 placeholder lifeguards to try it out
3. Click **Create scheduler**. You're signed in.

## Step 6 — Add your other admins

Go to **Admins** → **Add admin** (up to 10 in total). Give each person:

- the web address,
- their identifier,
- their temporary password. They can change it under Admins → Reset password.

Bookmark the address in Chrome on each computer.

---

## Day to day

**Backups**

- Render takes an automatic copy of the data disk every 24 hours and keeps each copy for 7 days.
  You can restore one from the service's **Disks** tab.
- For your own record, sign in and visit `https://<your-address>/store/backup`. It downloads everything as a JSON file.
  It's worth doing at the end of each week. Admin passwords are not included.

**Staffing layouts**

A layout saves which stands and units are open and how many guards each one uses. It never saves who is assigned. Every admin can see and load every layout.

- **Make one:** open **Layouts**, then choose **New from standard setup** or **From a day…**, adjust the stands, and save.
- **Use one:** on the schedule, click **Layouts**. Pick a layout and the days it applies to, such as Weekend, Weekdays or single days. You can also repeat it every week up to a date.
- **Short-staffed day:** click **Stands** under that day to close stands or lower guard counts for that day only. **Save as layout…** keeps that setup for next time.
- **Auto-Fill:** it always skips closed stands and slots a layout doesn't use.

**Updating the app**

If you get new files, upload them to the same GitHub repository the same way, replacing the old ones. Render redeploys automatically.
The scheduler is unavailable for about a minute while it restarts, so update outside busy hours.

**Your own web address (optional)**

You can add one later under the service's **Settings → Custom Domains**. Render walks you through the DNS record.

**If something goes wrong**

| What you see | What to do |
|---|---|
| Deploy fails with a Node.js error | Check Environment → `NODE_VERSION` is `24.22.1`, then **Manual Deploy → Deploy latest commit**. |
| "The setup code is incorrect" | Copy SETUP_CODE again from Environment. Check there's no space at either end. |
| Forgot the only admin password | In Render open **Shell** and run `node reset-admin.mjs ADM01` (see below). |
| Page loads but says "Sign in required" in a loop | Clear the site's cookies for the address and sign in again. |

### Resetting a forgotten admin password

If every admin is locked out, open the service in Render → **Shell**, then run:

```
node reset-admin.mjs ADM01
```

It prints a new temporary password for that identifier. Sign in with it and change it straight away.

---

## Technical notes (for IT)

- **Server:** `server.mjs` is the whole server bundled into one file. It needs Node ≥ 22.13 and has no npm dependencies. `npm start` runs it, and it serves `index.html` from the same folder.
- **Data:** a single SQLite file at `DB_PATH` (the Render disk).
- **Source:** the readable source (TypeScript, tests and the build scripts) is in the separate source zip. Rebuild the two files from it with `npm run build:web` plus the esbuild bundle step in its README.
- **Security:**
  - Sign-in is checked on the server. Sessions use HttpOnly, SameSite=Strict, Secure cookies.
  - Changes require a same-site custom header.
  - Password hashes (PBKDF2-SHA256) are never sent to browsers.
  - Failed sign-ins are rate-limited.
  - Pages carry a strict Content-Security-Policy and `noindex`.
- **Limits:** one instance only, because a disk can't be shared between instances.
