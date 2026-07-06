# Creating a DM app on Windows (with the Windows-ready fork)

Step-by-step commands to scaffold, run, and load data for an Equinor Development
Framework app on **Windows**, using this Windows-fixed fork of `create-dm-app`
(which also pulls a Windows-fixed `dm-cli`). Most steps run in **PowerShell**;
only `reset-app.sh` needs **Git Bash**.

> Why the fork? The upstream `create-dm-app` template + `dm-cli` have several
> Windows issues (PowerShell scaffolding, backslash paths in DMSS addresses, a
> cp1252 `UnicodeEncodeError`, and a stale-version mismatch). This fork
> `@timothy.edward.kendon/create-dm-app` ships the fixes end-to-end.

## Prerequisites

- **Node.js** > 16
- **Docker** + **Docker Compose** v2 (`docker compose ...`)
- **Python ≥ 3.13** (the forked `dm-cli` / `development-framework-cli` requires it)
- **Git** (the scaffolder clones the template; `requirements.txt` installs `dm-cli` from GitHub)
- **Git Bash** (only needed to run `reset-app.sh`)

## 1. Create the app (PowerShell)

```powershell
npx "@timothy.edward.kendon/create-dm-app" my-app
cd my-app
```

The quotes around the scoped package name are required in PowerShell. This clones
the template and runs `npm install`.

## 2. Start the backend services (PowerShell)

```powershell
docker compose pull
docker compose up -d
```

Brings up DMSS, the job service, MongoDB, and Redis (detached). Leave these
running while you use the app.

## 3. Create a Python venv and install `dm-cli` (PowerShell)

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

`requirements.txt` installs the forked `dm-cli` (`development-framework-cli`)
directly from GitHub, so this needs Git + network access.

## 4. Upload the app data — `reset-app.sh` (Git Bash)

`reset-app.sh` is a Bash script (`curl`, `envsubst`, `source`), so run it from
**Git Bash**. Open Git Bash in the `my-app` folder, then:

```bash
source .venv/Scripts/activate
./reset-app.sh
```

This uploads the data sources, blueprints, entities, and recipes under `app/` to
DMSS and creates the application lookup. With the fork you do **not** need to set
`PYTHONIOENCODING` — UTF-8 output is handled.

> **Re-run `./reset-app.sh` after any change to files under `app/`.**

> If the script prints a non-zero exit code at the very end while everything
> above succeeded, that's a PowerShell/stderr artifact, not a real failure.
> Confirm success by loading the app (next step) or checking the DMSS log for
> `POST /api/application/... - 204`.

## 5. Start the web app (PowerShell)

```powershell
npm start
```

Open http://localhost:3000 — you should see the app header and the Explorer
listing the `DemoDS`, `WorkflowDS`, and `system` data sources. The dev server
hot-reloads on code changes; the docker services from step 2 must be running.

## 6. Build a production bundle (optional, PowerShell)

```powershell
npm run build
```

## Quick reference

| Task           | Shell      | Command                                                                               |
| -------------- | ---------- | ------------------------------------------------------------------------------------- |
| Scaffold       | PowerShell | `npx "@timothy.edward.kendon/create-dm-app" my-app`                                   |
| Start services | PowerShell | `docker compose pull; docker compose up -d`                                           |
| Python env     | PowerShell | `python -m venv .venv; .\.venv\Scripts\Activate.ps1; pip install -r requirements.txt` |
| Upload data    | Git Bash   | `source .venv/Scripts/activate; ./reset-app.sh`                                       |
| Run app        | PowerShell | `npm start` → http://localhost:3000                                                   |
| Stop services  | PowerShell | `docker compose down`                                                                 |

## Troubleshooting

- **Port already in use (3000 / 5001 / 5002)**: another DM app's stack or dev
  server is still running. Stop it with `docker compose down` in that app's folder
  and close its `npm start`.
- **`reset-app.sh` says `db:27017 Connection refused`**: MongoDB is still
  initialising (it re-inits on first start). Wait a few seconds and re-run.
- **`dm: command not found` in Git Bash**: the venv isn't active — run
  `source .venv/Scripts/activate` first (step 4).
- **Prefer not to open Git Bash?** Run the script from PowerShell via Git Bash
  with the venv on `PATH`:
  ```powershell
  $env:PATH = "$PWD\.venv\Scripts;$env:PATH"
  & 'C:\Program Files\Git\bin\bash.exe' ./reset-app.sh
  ```

## Notes on versions

This fork aligns everything to a validated set: `dm-core` `1.46.6`,
`dm-core-plugins` `1.60.2`, DMSS `v1.26.0`, dm-job `v1.6.4`. Once the upstream
fixes are merged, switch back to `@development-framework/create-dm-app` and the
published `dm-cli`.
