# create-dm-app

[![license-badge]][license]
[![on-push-main-badge]][on-push-main-workflow]
[![healthcheck-badge]][healthcheck-workflow]

A tool for quickly creating _data modelling_ applications.

## :zap: Getting started

See [application development](https://equinor.github.io/dm-docs/docs/guides/application-development) for how to get a data modelling app up and running on your local machine.

## :window: Windows setup

This fork ships Windows fixes (PowerShell scaffolding, POSIX-safe DMSS paths,
UTF-8 output, and a validated version set). Quick start:

```powershell
npx "@timothy.edward.kendon/create-dm-app" my-app     # PowerShell (quotes required)
cd my-app
docker compose pull; docker compose up -d
python -m venv .venv; .\.venv\Scripts\Activate.ps1; pip install -r requirements.txt
```

Then upload the app data from **Git Bash** (only this step needs Bash):

```bash
source .venv/Scripts/activate && ./reset-app.sh
```

Finally, back in PowerShell: `npm start` → http://localhost:3000.
See [WINDOWS.md](./WINDOWS.md) for the full step-by-step guide (prerequisites,
troubleshooting, quick-reference table).

[license-badge]: https://img.shields.io/badge/License-MIT-yellow.svg
[license]: https://github.com/equinor/create-dm-app/blob/main/LICENSE
[on-push-main-badge]: https://github.com/equinor/create-dm-app/actions/workflows/on-push.yaml/badge.svg
[on-push-main-workflow]: https://github.com/equinor/dm-core-packages/actions/workflows/on-push.yaml
[healthcheck-badge]: https://github.com/equinor/create-dm-app/actions/workflows/on-schedule-nightly.yaml/badge.svg
[healthcheck-workflow]: https://github.com/equinor/create-dm-app/actions/workflows/on-schedule-nightly.yaml

<a id="Contributing"></a>

## :+1: Contributing

If you would like to contribute, please read our [Contribution guide](https://equinor.github.io/dm-docs/contributing/).
