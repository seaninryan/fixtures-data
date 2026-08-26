# Craughwell United fixtures — data

Machine-written. **Do not hand-edit `latest.json` or `changes.json`** — the next cron
run overwrites them.

| File | What it is |
|---|---|
| `latest.json` | The current snapshot: every upcoming fixture the league lists. |
| `changes.json` | One entry per run that saw a change, newest first, capped at 400. |
| `teams.json` | Squad labels and colours. **This one is yours to edit.** |

`git log` here is the fixture history — `git log -p latest.json` is every change the
league has ever made.

## How it gets written

`.github/workflows/check.yml` runs daily at 06:00 UTC. It checks out the code
(**[seaninryan/fixtures](https://github.com/seaninryan/fixtures)**), runs
`scripts/check.mjs` against the Galway FA endpoint, and commits whatever changed.

The cron lives here rather than in the code repo because this is where it needs write
access: a workflow's `GITHUB_TOKEN` writes only to its own repo, so nothing here needs a
personal access token. The trade is that `scripts/check.mjs` and the workflow that runs
it are versioned apart — change the script's env contract and change this workflow too.

The site reads these files directly over `raw.githubusercontent.com`, so a new snapshot
shows up at https://seaninryan.github.io/fixtures/ without any deploy.

A run that cannot parse fixtures, or sees the count halve, **fails and commits nothing**.
That is deliberate: a blocked request must never be recorded as "no fixtures", which the
next run would report as every fixture cancelled.

## Editing squad labels

Easiest from the site's **Squads** tab: edit, Copy JSON, paste into `teams.json` here.
Anything you set survives the cron — configured labels are never overwritten.

## Email alerts (optional)

Set `RESEND_API_KEY` and `ALERT_TO_EMAIL` as secrets **on this repo**, since that is
where the cron runs. Unset, the run just logs `email not configured`.
