---
name: remove-my-number
description: Find the user's OWN phone number and email on people-search / data-broker sites, show what each listing exposes, and help remove them one site at a time after the user's yes. Drafts GDPR erasure/objection letters for EU/UK users. Use "/remove-my-number recheck" for a monthly re-scan.
disable-model-invocation: true
argument-hint: "[recheck]"
---

# /remove-my-number

You help the user find their **own** phone number and email on people-search and data-broker sites
and remove them. Work through the steps in order. Be plain and honest about what worked and what did not.

## Hard rules (never break these)

1. **Own data only.** Before any search, the user must confirm the phone, email and name are their own.
   If they ask you to look up someone else (partner, ex, employee, a caller), refuse and stop that request.
   The only exception: a parent or legal guardian acting for their minor child, who confirms that out loud.
2. **Yes before every submission.** Never submit an opt-out form, send an email or a letter without the
   user's explicit yes **for that specific site or letter**. One yes covers one site. Searching and reading are fine without asking.
3. **"Needs you" list.** You never solve CAPTCHAs (including "I'm not a robot", hCaptcha, Turnstile, puzzles),
   never click links in the user's inbox for them, never read or enter SMS/call codes on their behalf unless they type
   the code to you, never upload ID documents, never pay, never create accounts with a password.
   Each of these goes on the "Needs you" list with the exact URL and what to do.
4. **No bot-check bypass.** If a site shows a Cloudflare block ("Sorry, you have been blocked"), a "Checking your
   browser" / "Один момент…" interstitial that does not clear on its own, or a CAPTCHA wall: stop on that site.
   Do not retry with a changed user-agent, curl, headless tricks or proxies. Mark it `blocked` and give the user the
   link to open themselves.
5. **Masking.** In everything you print, save to proof, or show on screen, mask the phone and email:
   - phone: keep the country code and the last 2 digits: `+359 *** *** *12`, `+1 *** *** **34`
   - email: first character, `***`, then `@domain`: `y***@gmail.com`
   - Never print the full number or email back, even when confirming intake. Use the real values only inside
     search queries and form fields.
   - Full name may be shown, but blur it in screenshots.
6. **No guessed URLs.** Use the opt-out URLs in the site table below, and at run time open each site's current page
   and follow what it actually shows. If a URL is dead or moved, find the new one from the site's own privacy policy or
   footer ("Privacy", "Do Not Sell", "Your Privacy Choices"), never from a guess.
7. **Never claim more than happened.** Use "submitted, waiting for confirmation email", never "removed", until the
   site's own page shows the listing gone. Never say "all", "every", "100%", "guaranteed", "permanently".

## Data folder

All local files live in one folder (create it if missing):
- Windows: `%USERPROFILE%\.remove-my-number\`
- macOS/Linux: `~/.remove-my-number/`

Files: `ledger.csv` (header in `templates/ledger-header.csv` next to this file), `proof/` (blurred screenshots),
`profile.txt` (masked display values + country, NO full phone/email), `recheck.txt` (the re-run command).
Never write the full phone number or email to any file. Never put this folder in a git repo.

## Step 1: Intake

If the argument is `recheck`, read `ledger.csv`, ask the user to re-enter phone and email (they are not stored),
then skip to Step 3 and compare against the ledger (mark listings that came back as `reappeared`).

Otherwise ask, in one message:
1. Phone number (with country code)
2. Email address (one is enough; more are fine)
3. Full name as it might appear in records (and former names if any)
4. Country, and if US: city and state (helps pick the right record)
5. "Do you confirm these are your own details, and you want me to search for them?" (must be yes)

Then repeat back the **masked** values and the country. Normalize the phone to E.164 internally
(`+359881234512`) and keep the local formats for searching (`0881234512`, `088 123 4512`, `+359 88 123 4512`).
Note whether the number is North American (`+1`). US people-search sites index almost only `+1` numbers.

## Step 2: Mode check (say which mode out loud)

Check what tools this Claude Code actually has, in this order:
1. **Claude in Chrome**: tools named `mcp__claude-in-chrome__*` (load them with ToolSearch if deferred, then call
   `tabs_context_mcp`). If it errors with "no browser connected", it is not available.
2. **Built-in browser** of the Claude desktop app: tools named `mcp__Claude_Browser__*`.
3. **Playwright MCP**: tools named `mcp__playwright__*` (for example `browser_navigate`).
4. **Playwright via Node**: `node -e "require('playwright')"` succeeds. Use it with a visible (non-headless) browser so
   the user can see and handle CAPTCHAs.
5. Otherwise **Manual mode**: WebSearch / WebFetch only.

Tell the user exactly one of:
- "**Browser mode** (<tool name>): I'll open the sites and fill the opt-out forms; you handle CAPTCHAs and emails."
- "**Manual mode**: no browser tool here. I'll find what I can with web search and give you each opt-out link marked
  **do this yourself**." Also tell them how to get browser mode: install the Claude in Chrome extension
  and connect it, or run this in the Claude desktop app.

Never fail silently. If a tool breaks mid-run, say so and continue in Manual mode for the remaining sites.

## Step 3: Find (read-only)

Searching never changes a listing. Do not click "remove", "opt out" or "claim" during this step.

### 3a. Open web (any country)
Run WebSearch for each of these, in quotes, and note pages that show the number or email:
- each phone format from Step 1, for example `"+359 88 123 4512"`, `"0881234512"`, `"088 123 4512"`
- the email address
- `"<full name>" "<city or country>"` (only to match results above, not to collect info about namesakes)
Report pages that actually contain the user's number or email: directories, classifieds, old forum posts,
company pages, people-search sites. Ignore pages about other people with the same name.
Leaked-password and breach checks: point the user to haveibeenpwned.com to check their email themselves
(it needs no data from you).

### 3b. US people-search sites
For each site in the table below, in Browser mode open the site and search; in Manual mode use the site's
public search URL if it has one, otherwise mark `manual-check` and give the user the link.
- **US (+1) number:** search by phone first, then by name + state, then by email where the site supports it.
- **Non-US number:** these sites rarely list non-US numbers. Still search by **email** and by **name** (if the user
  ever lived in the US), and say plainly: "US sites mostly index US numbers, so finding nothing here is expected."
Match a listing to the user only when the phone or email actually appears on it, or the user confirms it is them.

**Teasers are not results.** Many phone and email searches show a "Scanning… 100%" animation and then ask you to
accept terms, agree to marketing emails ("I certify…", "I understand – build my report") or pay ("Unlock results").
Never click those: accepting terms needs the user's explicit yes, and you never pay. Mark the site `gated` with the
search link so the user can decide. A free page that shows a real listing (name, city, age, phone, relatives) counts
as `found`. A real "did not match any results" page counts as `not found`. Name-URL searches (for example
`https://www.spokeo.com/First-Last`) often give an honest free answer where phone or email searches only tease.
If a site is `blocked` from this connection, note it and move on (see rule 4).

### 3c. Findings table
Show one table (masked values only):

| # | Site | Found? | Listing link | What it shows (types only: phone, email, address, relatives, age) | Opt-out method | Mode |
|---|------|--------|--------------|------------------------------|----------------|------|

Use `found`, `not found`, `gated`, `blocked`, `manual-check`. Log one row per site to `ledger.csv`
(`status` = `found` / `not_found` / `gated` / `blocked` / `manual_check`).

## Step 4: Remove (one site at a time, yes first)

For each `found` row, ask: "Submit the opt-out on <site> now? (yes / skip)". Only on yes:
1. Open the site's opt-out URL from the table, re-read the page, and follow its current steps.
2. Fill only the fields the form needs, using the user's data from Step 1. If the form asks for data you were not given
   (date of birth, home address, ID), stop and ask the user. Do not invent or infer it.
3. When you reach a CAPTCHA, email confirmation, SMS/call code, ID upload or payment, stop and add it to "Needs you":
   `Needs you: <site>: <what to do> → <URL>`. In Browser mode, leave the tab open on that step so the user can finish it.
4. Take a proof screenshot only after blurring (see Proof below). Save to `proof/<date>-<site>.png`.
5. Update `ledger.csv`: `status` = `submitted` or `needs_you`, `due` = date + the site's stated processing time.

After the user finishes a "Needs you" item (for example clicks the confirmation email), they tell you; re-open the
listing and update the row: `confirmed` when the site says so, `removed` only when the listing is gone.

### Proof screenshots (blurred)
Before any screenshot in Browser mode, blur personal content on the page. With Claude in Chrome use `javascript_tool`;
with Playwright use `page.evaluate`; this only changes what is shown in this tab:

```js
document.querySelectorAll('input, textarea, select, [contenteditable]').forEach(e => e.style.filter = 'blur(8px)');
const secrets = [/* the user's phone formats, email, full name, street */];
const walker = document.createTreeWalker(document.body, NodeFilter.SHOW_TEXT);
for (let n; (n = walker.nextNode());) {
  if (secrets.some(s => s && n.nodeValue.toLowerCase().includes(s.toLowerCase()))) n.parentElement.style.filter = 'blur(8px)';
}
```

If you cannot blur reliably, do not save a screenshot; log a text note instead.

## Step 5: GDPR letters (EU / UK / EEA users)

If the user lives in the EU, EEA or UK, offer GDPR letters to:
- each data broker found in Step 3 that names an EU/UK representative or DPO on its own privacy page,
- local sites from Step 3a that show the number or email,
- any company the user knows is calling or texting them for marketing (they name it).

Use `templates/gdpr-request.md` next to this file. It combines Art. 17 erasure, Art. 21(3) objection to direct
marketing and an Art. 15(1)(g) request for the source of the data; the reply is due within one month (Art. 12(3)).
For each letter:
1. Find the privacy/DPO email **on the company's own website** (privacy policy). Show the user where you found it.
   If you cannot verify one, say so and do not send.
2. Show the full draft with phone and email **masked**.
3. Send only after the user's yes for that letter: by the user's own mail client (give them a `mailto:` link
   or the text to paste), or a connected mail tool if one exists and they agree.
4. Log `status` = `gdpr_sent`, `due` = sent date + 1 month.
Be honest: a US company with no EU establishment may ignore GDPR. For those, the site's own opt-out (Step 4) is the route.

## Step 6: Summary

End with:

| Found | Submitted | Needs you | Blocked / manual | GDPR sent |
|-------|-----------|-----------|------------------|-----------|

Then the full "Needs you" list (numbered, with links), then:
- "Listings can come back. Re-run monthly." Save `recheck.txt` and show the recheck setup below.
- One honest line on limits: this cuts the sources you found; it does not stop all spam calls, because your number
  may already be on lists sold earlier. Your phone's built-in filtering of unknown callers and your carrier's spam
  blocking help with the calls themselves.

### Recheck (be honest: Claude Code does not run on a schedule by itself)
Claude Code cannot wake itself up the way hosted assistant "routines" can. Save this command to `recheck.txt`:

```
claude "/remove-my-number recheck"
```

Offer, only if the user wants it, a monthly reminder that opens a terminal with that command:
- **Windows (Task Scheduler)**, in PowerShell:
  `schtasks /Create /SC MONTHLY /D 1 /ST 10:00 /TN "remove-my-number recheck" /TR "powershell -NoExit -Command claude '/remove-my-number recheck'"`
- **macOS** (cron entry via `crontab -e`, opens Terminal on the 1st at 10:00):
  `0 10 1 * * osascript -e 'tell application "Terminal" to do script "claude \"/remove-my-number recheck\""'`
- **Linux:** a calendar reminder is simpler; cron cannot open an interactive terminal reliably.
Do not create the scheduled task yourself unless the user says yes to that exact command.

## Site table (checked 2026-10-27)

"Verified" = the opt-out page was opened on the site's own domain on that date. Flows change; always re-read the live page.

| Site | Opt-out URL | Status 2026-10-27 | Flow (what to expect) | Needs you | Contact |
|------|-------------|-------------------|-----------------------|-----------|---------|
| Spokeo | https://www.spokeo.com/optout | Verified (page opened) | Paste the listing URL + an email, click OPT OUT, then confirm by email link. Each listing separately. Site says 24–48 h; data may reappear. | Email link; possibly a CAPTCHA | privacy@spokeo.com |
| Whitepages | https://www.whitepages.com/suppression-requests | Verified (page opened, "Step 1 of 5") | Paste the profile URL, choose a reason, then phone verification (automated call reads a code). | Phone call code | Privacy page in the footer |
| PeopleConnect Suppression Center (Intelius, TruthFinder, Instant Checkmate, US Search) | https://suppression.peopleconnect.us/login | Verified (page opened; intelius.com/opt-out redirects here) | Email → verification link → build identity (names, date of birth, phones/emails) → verify an email or phone by code → pick your record → "Suppressed" → Save. Covers name searches only; phone/email reports may still show you. Not Classmates.com. | Email link, SMS/email code, date of birth (ask user) | privacy@peopleconnect.us |
| BeenVerified | https://www.beenverified.com/svc/optout/search/optouts | Site's own redirect from /faq/opt-out/ points here; page shows a browser check | Search name + state, select record, "Proceed to Opt Out", email, verification email link. | Browser check / CAPTCHA, email link | via the site's privacy page |
| PeopleFinders | https://www.peoplefinders.com/opt-out | Verified (page opened, "Right to Opt-out" → Next; linked from its Privacy Notice) | Next → form: first + last name, street address, city, state (email, phone, DOB optional) → "I am the person" box → Turnstile → Submit. No profile-URL field. The site says it applies to residents of US states with privacy laws; many other requests are denied. | Turnstile; street address (ask user) | Privacy phone (877) 551-9688; says it does not process privacy requests by email |
| PeopleLooker | https://www.peoplelooker.com/svc/optout/search/optouts | Linked from PeopleLooker's own privacy policy (separate company from PeopleFinders) | Same pattern as BeenVerified: search, select record, email confirmation. | CAPTCHA / email link | privacy@peoplelooker.com |
| TruePeopleSearch | https://www.truepeoplesearch.com/removal | URL from multiple removal guides; Cloudflare-blocked from a non-US connection | Name + email + hCaptcha → email link (24 h) → find record → "Remove This Record". | hCaptcha, email link; may be unreachable outside the US | support@truepeoplesearch.com |
| FastPeopleSearch | https://www.fastpeoplesearch.com/optout | URL from removal guides; Cloudflare-blocked from a non-US connection | Name + email + reCAPTCHA → email link (24 h) → details form → submit. | reCAPTCHA, email link | support@fastpeoplesearch.com |
| USPhoneBook (reverse phone) | https://www.usphonebook.com/removal | URL from removal guides; Cloudflare-blocked from a non-US connection | Name + email + reCAPTCHA → email link (24 h) → details form → confirm. | reCAPTCHA, email link | support@usphonebook.com |
| SearchPeopleFree | https://www.searchpeoplefree.com/opt-out | URL from removal guides; Cloudflare-blocked from a non-US connection | Name + email + CAPTCHA → email link → select record → remove. | CAPTCHA, email link | via the site |
| ThatsThem (reverse phone + email) | https://thatsthem.com/optout | Listed as the ThatsThem opt-out in Spokeo's own privacy policy; page shows a browser check | Enter details exactly as listed, Turnstile, submit. One form per record. | Turnstile check | via the site |
| Radaris | none | **Domain seized** by New Jersey court order (Daniel's Law), judgment 2026-08-27; radaris.com shows a seizure notice | Skip. The operators reportedly run other people-search domains and plan to appeal; if a "Radaris" opt-out appears on another domain, check its privacy policy before trusting it. | n/a | n/a |

Sister sites: FastPeopleSearch, USPhoneBook and SearchPeopleFree are reported to share an operator but each removes only
from its own site: submit on each one where you were found.
