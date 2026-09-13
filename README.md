# Runn 🏃

> A guardrail repo for the streak.

Runn is a small GitHub Actions project that protects the GitHub contribution streak for **suryaraj09**. If no commit by that account is found during the current India Standard Time (IST) day, it creates an automated guardrail commit to maintain continuity.

## How it works

1. The workflow runs daily at **12:00 UTC (17:30 IST)**.
2. A backup runs at **17:00 UTC (22:30 IST)**, leaving time for normal GitHub Actions scheduling delays.
3. It searches GitHub for commits authored by `suryaraj09` during the current IST calendar day.
4. If no commit exists and no guardrail was already recorded, it appends a timestamp to `streak.log` and pushes a commit.
5. Guardrail commits use the account's GitHub noreply address, so GitHub can attribute them to `suryaraj09`.

The log check independently prevents the backup run from creating a duplicate while GitHub's commit-search index catches up.

## Features

✨ **Automated Streak Protection** - Ensures your contribution graph never breaks
🔄 **Dual Scheduling** - Primary and backup runs for reliability
📧 **Email Notifications** - Optional Gmail alerts when guardrails are deployed
🔐 **Secure** - Uses GitHub Actions secrets for sensitive data
📝 **Transparent Logging** - Append-only log of all guardrail commits

## Files

| File | Purpose |
|---|---|
| `.github/workflows/streak.yml` | Scheduled streak-keeper workflow |
| `streak.log` | Append-only record of guardrail commits |
| `CONTRIBUTING.md` | Contribution guidelines |

## Requirements

In **Settings → Actions → General**:

- Actions must be enabled.
- Workflow permissions must be set to **Read and write permissions**.

For commits to appear on the contribution graph, the repository's default branch must remain `main`, and the commit email must stay linked to the GitHub account.

## Manual run

Open **Actions → Streak Keeper → Run workflow** to test or run the guardrail on demand.

## Email notifications

Runn can be configured to send an email only after it finds no commits and successfully creates a guardrail commit. It does not send mail when normal activity is detected or when the backup run skips.

Gmail delivery uses these GitHub Actions repository secrets:

| Secret | Purpose |
|---|---|
| `MAIL_USERNAME` | Gmail account used to send the notification |
| `MAIL_PASSWORD` | 16-character Google App Password |
| `MAIL_TO` | Notification recipient |

For this repository, `MAIL_USERNAME` and `MAIL_TO` are configured for `suryarajjadeja09@gmail.com`. `MAIL_PASSWORD` must be added privately under **Settings → Secrets and variables → Actions**.

Create the password at [Google App Passwords](https://myaccount.google.com/apppasswords). Two-step verification must be enabled on the Google account. Never use a regular Gmail password, place credentials only in repository secrets.

## Important limitation

The repository-scoped `GITHUB_TOKEN` can reliably search public commits. It cannot inspect unrelated private repositories. A day containing only commits in another private repository may therefore trigger the guardrail.

## Disabling

Disable **Streak Keeper** from the Actions tab or remove `.github/workflows/streak.yml`.

## License

MIT License - See LICENSE file for details

## Author

Created and maintained by [@suryaraj09](https://github.com/suryaraj09)
