---
name: remove-my-number
description: Find the user's OWN phone number and email on people-search sites, caller-ID apps and B2B contact databases, remove them one site at a time after the user's yes, unsubscribe from marketing mail in Gmail, point to the national do-not-call registry, and draft privacy-law letters by country of residence. Use "/remove-my-number recheck" for a monthly re-scan.
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
2. **Yes before each submission.** Never submit an opt-out form, send an email or a letter without the
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
4. Country of residence (decides which privacy law and do-not-call registry apply), and if US: city and state
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

### 3b-2. Caller-ID apps (most useful for non-US numbers)
Apps like Truecaller and GetContact build their directories from users' uploaded contact lists, so a number can be
listed with a name or tags other people saved for it. Whether a given number is listed is unknown until the user checks.
Never say a number "is" listed before seeing it. You never install these apps, never log in, never receive or type
the user's SMS/app codes unless the user types the code to you themselves.

| Service | Official route (checked 2026-10-27) | What the user does | GDPR |
|---------|-------------------------------------|--------------------|------|
| Truecaller | https://www.truecaller.com/unlisting → button "No, I want to unlist" → follow the form on the page | Completes the CAPTCHA/verification. The page states unlisting cannot be reversed and the user loses spam protection and caller ID; tell them before their yes. Seeing what name Truecaller shows needs the app or a login: that is the user's step. | Truecaller is an EU (Swedish) company: an Art. 15 request returns what it holds about the number. Get the DPO contact from its own privacy policy at run time. |
| GetContact | https://getcontact.com/en/manage → choose country, enter the number → Continue → verify the number | Receives and enters the verification code; then manages, hides or deletes the profile on the page that follows. The tags people saved for the number are visible only in the app after the user logs in. | GetContact names "Getverify LDA" in its footer (LDA is a Portuguese company form; confirm the controller in its privacy policy); footer link "Privacy Policy / GDPR". Art. 15 asks which tags/names are stored for the number. |

In Browser mode you may open these pages and fill the phone-number field after the user's yes for that service;
stop at each code, CAPTCHA or app login and put it on "Needs you".

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

## Step 4b: Inbox sweep (Gmail): less marketing mail, fewer old accounts

Offer this; run it only after the user's yes. Browser mode only, in the Gmail the user is **already signed in to**.
Never sign in, never ask for a password, never use a session you were not handed. If Gmail is not open and signed in,
say "open Gmail and sign in yourself, then tell me" and wait.

Privacy limits for this step:
- Read only what the message **list** shows: sender, subject, date. Do not open message bodies, except that the user may
  open one themselves. Never save subjects or bodies to files; the ledger gets sender domain, count and dates only.
- In the chat table show sender and counts, not subjects (subjects can contain personal details).

### Unsubscribe
1. Open Gmail's own **Manage subscriptions** view: left menu → "More" → "Manage subscriptions"
   (Google help: https://support.google.com/mail/answer/15621070; Google says it is rolling out gradually, so it may be missing).
2. If it is missing, find senders with Gmail search (operators from https://support.google.com/mail/answer/7190):
   `category:promotions newer_than:6m`, `list:` addresses, `category:updates newer_than:6m`. Count messages per sender.
   Search results are sorted by relevance and their pages may not advance when you change the page. If that happens,
   page through the **Promotions tab** itself (`#category/promotions`, then `/p2`, `/p3`…; newest first, 50 per page),
   wait for the first row to change before reading, and stop at the first date older than your time window.
   Read rows from the list only (sender address and name from the row, date from the date cell). For large mailboxes,
   aggregate in the page and pull results out in small chunks.
3. Show a table: `# | sender | messages (6 months) | last date`. The user picks which ones.
4. For each picked sender, after the user's yes for that sender, click **Gmail's own "Unsubscribe" button**
   (in Manage subscriptions, or the "Unsubscribe" link Gmail shows next to the sender name). Never click unsubscribe links
   inside the email body. Google notes it can take a few days to take effect.
5. **Suspected scam or phishing** (fake bank/parcel/prize, lookalike domains, urgent payment requests): do not unsubscribe
   and do not click anything inside. Offer Gmail's **Report spam** (https://support.google.com/mail/answer/1366858) and
   **Block "sender"** from the three-dot menu (https://support.google.com/mail/answer/8151), each after the user's yes.

### Old accounts (where your email was used to sign up)
1. Search the list view: `subject:(welcome OR verify OR confirm OR activate OR "your account")`, then
   `subject:(welcome OR verify) older_than:2y`. Group by sender domain = service.
2. For each service note the earliest date you can see. Show `# | service | earliest email | still sending?`.
3. Breaches: ask the user to check their email at https://haveibeenpwned.com (free lookup on the site; do it with them in
   the browser if they agree). Flag services whose names appear in the breach list. Never show or look for passwords.
4. For services the user wants gone: find the **account deletion page on the service's own site** (help center,
   "delete account"). The JustDeleteMe list (github.com/jdm-contrib/jdm, MIT licence) can be a starting hint, but only show
   a link after you open it on the service's own domain. Deleting an account needs the user's login: that is **their step**
   (put it on "Needs you"). If there is no deletion page, offer an erasure letter (Step 5).
5. Log to `ledger.csv`: `site` = service domain, `method` = `gmail_unsubscribe` / `report_spam` / `account_delete` / `letter`.

## Step 4c: B2B contact databases

Sales-contact databases (ZoomInfo, Apollo, Lusha and others) sell phone numbers and emails to sales teams worldwide.
Use `data-brokers-b2b.md` next to this file: for each company, after the user's yes, open its official opt-out or
privacy-request page and submit with the user's email (and phone if the form asks). Same rules as Step 4: the user
handles email links, codes and CAPTCHAs; no account creation, no ID upload, no payment. Most of these do not show a
profile without an account, so submit the opt-out directly, and in countries with an access right add an access + source
letter (Step 5). Log `method` = `b2b_optout`.

## Step 4d: National do-not-call registry

Look up the user's **country of residence** in `regions/` (see Step 5) and its "Do-not-call" entry. If an official
registry exists, give the link and the steps, and offer to open it after the user's yes. Registering (codes, confirmations,
government sign-ins) is the user's step. Say plainly that these registries cut legitimate telemarketing and do not stop
scam calls. If there is no registry, say so and point to the letters in Step 5. Log `method` = `dnc_registry`.

## Step 5: Letters by country of residence

Pick the file in `regions/` for the user's country of residence (not nationality):
- `regions/europe.md`: EU/EEA (GDPR, with the national authority list), UK, Switzerland, Ukraine, Turkey
- `regions/americas.md`: USA (California first, with DROP; other states), Canada, Brazil, Mexico, Argentina
- `regions/asia-pacific.md`: Australia, New Zealand, Japan, South Korea, Singapore, India
- `regions/africa.md`: South Africa
Each section gives the law, the rights (access + source, erasure, stop marketing), the legal deadline, the regulator, the
do-not-call option and a letter. EU/EEA and UK letters use `templates/gdpr-request.md`. For any other country, or when a
section says so, use `templates/letter-generic.md`: polite, no law cited, **no deadline claimed**.

Offer letters to:
- each company found in Steps 3–4c that holds the user's data,
- any company the user names that calls, texts or emails them with marketing.

For each letter:
1. Find the privacy/DPO contact **on the company's own website** (privacy policy). Show the user where you found it.
   If you cannot verify one, say so and do not send.
2. Write the letter in the company's language (English for international companies). Use the deadline from the region
   section only; never invent one.
3. Show the full draft with phone and email **masked**.
4. Send only after the user's yes for that letter: by the user's own mail client (give them a `mailto:` link
   or the text to paste), or a connected mail tool if one exists and they agree.
5. Log `status` = `letter_sent`, `due` = sent date + the region's deadline (blank if none).
Be honest: a company outside the user's legal system may ignore the letter. A reply that names the source of the data is
the best evidence the user can get.

## Step 6: Summary

End with:

| Found | Submitted | Needs you | Blocked / manual | Unsubscribed | Do-not-call | Letters sent |
|-------|-----------|-----------|------------------|--------------|-------------|--------------|

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
