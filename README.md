# Runn 🏃

> A guardrail repo for the streak.

Runn is a tiny GitHub Actions project that keeps your GitHub contribution
**green streak** alive. It runs on a schedule and, only if you haven't
already contributed that day, makes a small "guardrail" commit so your
streak never breaks — even on days when you don't have time to code.

## How it works

```
scheduled trigger (12:00 UTC + 18:00 UTC backup)
        │
        ▼
┌─────────────────────────────┐
│ Check GitHub search API for  │
│ commits authored by you today│
└─────────────────────────────┘
        │
        ▼
   contributed today?
      │            │
     yes          no
      │            │
      ▼            ▼
  do nothing   append timestamp
  (streak safe) to streak.log and
               commit + push
```

1. A scheduled workflow runs every day at **12:00 UTC** (with an **18:00 UTC**
   backup in case the main run fails).
2. It queries the GitHub commit search API to count commits you authored
   today.
3. If the count is **0**, it appends a timestamp line to `streak.log` and
   pushes a commit — that counts as a contribution for the day.
4. If you **already contributed**, it skips — no fake activity is created.

## Files

| File | Purpose |
|------|---------|
| `.github/workflows/streak.yml` | The Streak Keeper workflow |
| `streak.log` | Append-only log of guardrail commits (created on first run) |

## Setup

There is **no configuration required** — push this repo to GitHub and the
workflow is live. Just make sure:

- GitHub **Actions are enabled** for the repo
  (Settings → Actions → General → Allow all actions and reusable workflows).
- The `GITHUB_TOKEN` (auto-provided) has **write access**
  (Settings → Actions → General → Workflow permissions → Read and write).

## Changing the schedule

Edit the `cron` lines in `.github/workflows/streak.yml`:

```yaml
on:
  schedule:
    - cron: "0 12 * * *"   # main run
    - cron: "0 18 * * *"   # backup run
```

Cron uses **UTC**. GitHub's contribution graph is based on your profile's
timezone, so pick times that are safely before midnight *in your timezone*
(e.g. if you're UTC+5:30, 12:00 UTC = 17:30 IST, and the 18:00 UTC backup
= 23:30 IST).

> ⚠️ Remember: a commit made by the workflow at, say, 23:30 IST still counts
> for that IST day since the author date is the actual commit time. Keep at
> least one run before your local midnight so the guardrail lands on the
> right day.

## Manual trigger

You can also run it on demand (handy for testing or an emergency save):

1. Go to the repo's **Actions** tab.
2. Select **Streak Keeper** → **Run workflow**.
3. Optionally enter a username to check (defaults to the repo owner).

## Notes & caveats

- **Honest by default**: Runn only commits when you have *zero* commits
  that day, so it never inflates your graph with fake work on active days.
- **Noise**: on inactive days it does add a commit — that's the whole point,
  but be aware your history will show daily "chore" commits.
- **Search API coverage**: the check looks at commits GitHub attributes to
  your account. If you commit with an unlinked email, those won't count —
  make sure your git email matches a GitHub-linked email.
- **Rate limits**: the search API limit with a token is plenty for one call
  per run.
- **Other contribution types** (issues, PRs, reviews) also count toward your
  streak on GitHub — the guardrail commit is just a reliable fallback.

## Disabling

To stop the guardrail, either delete `.github/workflows/streak.yml` or
disable the workflow in the Actions tab (Actions → Streak Keeper → ⋯ → Disable).
