# /remove-my-number

A Claude Code skill that looks for **your own** phone number and email on people-search and
data-broker sites, shows you what each listing exposes, and helps you remove it, one site at a
time and only after your yes. For EU and UK residents it also drafts GDPR letters (erasure,
objection to direct marketing, source of data) for you to approve and send.

> Own data only. The skill refuses to look up anyone else.

## What it does

1. **Intake.** It asks for your phone number, email, full name and city/state or country, and you confirm they are yours.
2. **Mode check.** It checks what this Claude Code can use:
   - **Browser mode**: Claude in Chrome or Playwright is available. Claude opens the sites and fills the opt-out forms for you.
   - **Manual mode**: no browser tool. Claude searches with WebSearch/WebFetch and gives you each site's opt-out link marked **"do this yourself"**.
   It always tells you which mode it is in.
3. **Find.** It searches the main US people-search sites (Spokeo, Whitepages, BeenVerified, PeopleLooker, Intelius and its sister sites, PeopleFinders, TruePeopleSearch, FastPeopleSearch, USPhoneBook, SearchPeopleFree, ThatsThem), plus a plain web search for your number and email on any site, and the official removal pages of the caller-ID apps Truecaller and GetContact (useful outside the US). Radaris is listed as gone: its domain was seized by a US court in August 2026 and shows a table: site, listing link, what the listing shows, how to opt out.
4. **Remove.** For each site you say yes or no. Claude fills the form. CAPTCHAs, confirmation emails, phone codes, ID uploads and payments go to a **"Needs you"** list. Claude never solves CAPTCHAs, never pays and never uploads ID.
5. **GDPR (EU/UK).** It drafts Art. 17 erasure, Art. 21(3) direct-marketing objection and Art. 15(1)(g) source-of-data letters, which companies must answer within one month (Art. 12(3)). It shows you each draft and sends nothing without your yes. It writes only to privacy contacts verified on the company's own site.
6. **Ledger.** Each step is logged to a local `ledger.csv`. Proof screenshots go to `proof/` with form fields and personal text blurred. Your number and email are masked in all chat output (`+359 *** *** *12`, `y***@gmail.com`).
7. **Recheck.** Listings can come back. Claude Code cannot schedule itself, so the skill saves a one-line re-run command and shows you how to run it monthly with Task Scheduler or cron.

## Install

The skill is a folder: `SKILL.md` plus `templates/`. Copy the whole folder.

**Windows (PowerShell)**

```powershell
Copy-Item -Recurse -Force .\skills\remove-my-number "$env:USERPROFILE\.claude\skills\remove-my-number"
```

The skill ends up at `%USERPROFILE%\.claude\skills\remove-my-number\SKILL.md`.

**macOS / Linux**

```bash
mkdir -p ~/.claude/skills && cp -R skills/remove-my-number ~/.claude/skills/
```

The skill ends up at `~/.claude/skills/remove-my-number/SKILL.md`.

For browser mode, install and connect the Claude in Chrome extension, or have Playwright available.
Without a browser tool the skill still runs in manual mode.

## Use

In Claude Code, type:

```
/remove-my-number
```

To re-run later (for example monthly):

```bash
claude "/remove-my-number recheck"
```

A recheck reads your `ledger.csv`, searches again, and flags listings that came back.

## Honest limits

- **Mostly US sites.** The site list covers US people-search brokers. Non-US numbers (for example +359) often show up on few or none of them. EU/UK users usually get more out of the GDPR letters.
- **Some sites block visitors outside the US.** From a non-US connection, several free people-search sites show a block page. The skill marks them `blocked` and does not try to get around it.
- **Some opt-outs are limited by US state law.** PeopleFinders, for example, says its opt-out applies to residents of US states with privacy laws.
- **Listings can return.** Brokers re-import data. Re-run monthly.
- **It does not stop all spam calls.** Removing listings cuts one source; your number may already be in lists that were sold earlier, or in leaked data the skill cannot reach.
- **It needs you.** CAPTCHAs, confirmation emails, SMS or call codes are yours to complete. Some sites ask for an account or ID; the skill tells you and leaves the choice to you.
- **Flows change.** The opt-out links in the skill were checked on the date shown next to each site. At run time Claude reopens each site's current page and follows what it actually shows.
- **Not legal advice.** The GDPR letters are templates. US companies without an EU presence may not reply.

## Privacy

All files stay on your machine: `ledger.csv` and `proof/` are gitignored and never uploaded by the skill.
Searches are made from your browser or through Claude's web tools, so the sites you search do see those queries.

## License

MIT
