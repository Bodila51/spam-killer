# /spam-killer

A Claude Code skill that looks for **your own** phone number and email on people-search and
data-broker sites, shows you what each listing exposes, and helps you remove it, one site at a
time and only after your yes. It also unsubscribes you from marketing mail in Gmail, opts you out of
B2B sales-contact databases, points you to your country's official do-not-call registry, and drafts
privacy-law letters for your country of residence. The goal is honest: **less spam**, and fewer places
that sell your number and email.

> Own data only. The skill refuses to look up anyone else.

## What it does

1. **Intake.** It asks for your phone number, email, full name and country of residence (plus city/state in the US), and you confirm they are yours.
2. **Mode check.** It checks what this Claude Code can use:
   - **Browser mode**: Claude in Chrome or Playwright is available. Claude opens the sites and fills the opt-out forms for you.
   - **Manual mode**: no browser tool. Claude searches with WebSearch/WebFetch and gives you each site's opt-out link marked **"do this yourself"**.
   It always tells you which mode it is in.
3. **Find.** It searches the main US people-search sites (Spokeo, Whitepages, BeenVerified, PeopleLooker, Intelius and its sister sites, PeopleFinders, TruePeopleSearch, FastPeopleSearch, USPhoneBook, SearchPeopleFree, ThatsThem), plus a plain web search for your number and email on any site, and the official removal pages of the caller-ID apps Truecaller and GetContact (useful outside the US). Radaris is listed as gone: its domain was seized by a US court in August 2026. It shows a table: site, listing link, what the listing shows, how to opt out.
4. **Remove.** For each site you say yes or no. Claude fills the form. CAPTCHAs, confirmation emails, phone codes, ID uploads and payments go to a **"Needs you"** list. Claude never solves CAPTCHAs, never pays and never uploads ID.
5. **Gmail sweep.** In the Gmail you are already signed in to, it lists marketing senders (sender, count, last date; no message bodies), and after your yes clicks Gmail's own Unsubscribe button. Suspected scams get Report spam / Block instead. It also lists old accounts your email signed up for and links each one's official account-deletion page (deleting needs your login, so that step is yours).
6. **B2B contact databases.** Opt-out and privacy-request pages for ZoomInfo, Apollo, Lusha, SignalHire, Hunter, Kaspr, Cognism, Acxiom, Epsilon, LiveRamp and others: the companies that sell contact details to sales teams.
7. **Do-not-call registry.** For your country of residence, the official registry link and steps (for example donotcall.gov, TPS, DNCL, the Do Not Call Register, Registro delle Opposizioni, Não Me Perturbe, İYS), or a plain "there is none here". Registering is your step. These registries cut legitimate telemarketing, not scam calls.
8. **Letters by country law.** EU/EEA (GDPR, with the national authorities), UK, Switzerland, US states (starting with California, including the DROP platform), Canada, Brazil, Mexico, Argentina, Australia, New Zealand, Japan, South Korea, Singapore, India, South Africa, Turkey, Ukraine. Each has the law, your rights (access including where they got your data, erasure, stop marketing), the legal deadline, the regulator and a letter in the company's language. Other countries get a polite request with no deadline claimed. You see each draft; nothing is sent without your yes, and only to privacy contacts found on the company's own site.
9. **Ledger.** Each step is logged to a local `ledger.csv`. Proof screenshots go to `proof/` with form fields and personal text blurred. Your number and email are masked in all chat output (`+44 **** ****23`, `m***@gmail.com`).
10. **Recheck.** Listings can come back. Claude Code cannot schedule itself, so the skill saves a one-line re-run command and shows you how to run it monthly with Task Scheduler or cron.

## Install

The skill is a folder: `SKILL.md` plus `templates/`, `regions/` and `data-brokers-b2b.md`. Copy the whole folder.

**Windows (PowerShell)**

```powershell
Copy-Item -Recurse -Force .\skills\spam-killer "$env:USERPROFILE\.claude\skills\spam-killer"
```

The skill ends up at `%USERPROFILE%\.claude\skills\spam-killer\SKILL.md`.

**macOS / Linux**

```bash
mkdir -p ~/.claude/skills && cp -R skills/spam-killer ~/.claude/skills/
```

The skill ends up at `~/.claude/skills/spam-killer/SKILL.md`.

For browser mode, install and connect the Claude in Chrome extension, or have Playwright available.
Without a browser tool the skill still runs in manual mode.

## Use

In Claude Code, type:

```
/spam-killer
```

To re-run later (for example monthly):

```bash
claude "/spam-killer recheck"
```

A recheck reads your `ledger.csv`, searches again, and flags listings that came back.

## Honest limits

- **People-search sites are mostly American.** Non-US numbers (for example a UK +44 number) often show up on few or none of them. Outside the US, the letters, the do-not-call registry, caller-ID apps, B2B databases and the Gmail sweep usually do more.
- **Less spam, not zero.** Do-not-call registries and letters work on companies that follow the law. Scammers ignore both.
- **Some sites block visitors outside the US.** From a non-US connection, several free people-search sites show a block page. The skill marks them `blocked` and does not try to get around it.
- **Some opt-outs are limited by US state law.** PeopleFinders, for example, says its opt-out applies to residents of US states with privacy laws.
- **Listings can return.** Brokers re-import data. Re-run monthly.
- **It does not stop all spam calls.** Removing listings cuts one source; your number may already be in lists that were sold earlier, or in leaked data the skill cannot reach.
- **It needs you.** CAPTCHAs, confirmation emails, SMS or call codes are yours to complete. Some sites ask for an account or ID; the skill tells you and leaves the choice to you.
- **Flows change.** The opt-out links in the skill were checked on the date shown next to each site. At run time Claude reopens each site's current page and follows what it actually shows.
- **Not legal advice.** The letters are templates based on each law's official text, researched in October 2026. Companies outside your country's law may not reply. Laws change: India's rights, for example, are being phased in until 2027.

## Privacy

All files stay on your machine: `ledger.csv` and `proof/` are gitignored and never uploaded by the skill.
Searches are made from your browser or through Claude's web tools, so the sites you search do see those queries.

## License

MIT
