# remove-my-number

A Claude Code skill (`skills/remove-my-number/SKILL.md`) that finds a user's own phone number and email
on people-search sites and helps remove them, plus GDPR letters for EU/UK users.

## Rules for working on this repo
- Never commit personal data. `ledger.csv` and `proof/` are gitignored; keep it that way.
  Test runs on real data write only to those paths.
- Opt-out URLs in `SKILL.md` must come from each site's own opt-out or privacy page, with the check date.
  Never add a guessed URL. When a flow changes, update the row and the "checked" date.
- Keep the safety rules in SKILL.md intact: own data only, yes before each submission, "Needs you" list
  (CAPTCHA, email links, codes, ID, payment are always the user's), masking in all output.
- README wording: honest limits. Do not use "first", "hack", "100%", "every", "guaranteed".
- Commit author: `Bodila51 <191466064+Bodila51@users.noreply.github.com>`.
