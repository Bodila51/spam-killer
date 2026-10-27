# GDPR / UK GDPR request: erasure, objection, source of data

Use one letter per company. Fill each `{{PLACEHOLDER}}` before showing the draft to the user.
Send ONLY after the user says yes to this exact draft, and ONLY to a privacy/DPO contact
you verified on the company's own website (privacy policy or "Do Not Sell / Privacy" page).

---

**Subject:** Data subject request – erasure (Art. 17), objection to direct marketing (Art. 21(3)) and source of data (Art. 15(1)(g)) – {{FULL_NAME}}

To: {{COMPANY_NAME}} – Data Protection Officer / Privacy Team
{{COMPANY_PRIVACY_CONTACT}}

Date: {{DATE}}

Dear Sir or Madam,

I am writing to exercise my rights under the General Data Protection Regulation (EU) 2016/679
{{OR: "the UK GDPR and the Data Protection Act 2018"}} in respect of personal data about me
that you hold or publish, including but not limited to:

- Full name: {{FULL_NAME}}
- Phone number: {{PHONE}}
- Email address: {{EMAIL}}
- Listing / profile URL(s), if known: {{LISTING_URLS_OR "not known"}}

**1. Objection to direct marketing – Article 21(2) and 21(3).**
I object to the processing of my personal data for direct marketing purposes, including
profiling related to direct marketing. Under Article 21(3), once I object my data must no longer
be processed for those purposes. This objection is absolute and does not require a balancing test.

**2. Erasure – Article 17(1).**
I request the erasure of all personal data concerning me, without undue delay. At least the
following grounds apply: the data are no longer necessary for the purposes for which they were
collected (Art. 17(1)(a)); I object to the processing and there are no overriding legitimate
grounds, and I object under Article 21(2) (Art. 17(1)(c)); and/or the data have been processed
unlawfully (Art. 17(1)(d)). Where you have made my data public or disclosed it to others, please
take reasonable steps to inform those controllers of this request (Art. 17(2) and Art. 19).

**3. Source of the data – Article 15(1)(g).**
Before erasing, please tell me where you obtained my personal data (Art. 15(1)(g)), and the
recipients or categories of recipients to whom it has been disclosed (Art. 15(1)(c)).
Erasing the data does not remove your duty to answer this part.

Please respond within one month of receipt, as required by Article 12(3). If you need more
time you must tell me within that month and explain why.

To verify my identity you may reply to this email address or call the number above. Please do not
ask for more personal data than is necessary for verification (Art. 12(6)); I will not send
identity documents unless you explain why they are strictly necessary.

If I do not receive a satisfactory reply, I reserve the right to complain to the supervisory
authority ({{SUPERVISORY_AUTHORITY}}) under Article 77.

Yours faithfully,
{{FULL_NAME}}

---

## Filling notes for Claude

- `{{SUPERVISORY_AUTHORITY}}`: the user's country's data protection authority, for example
  Bulgaria: Commission for Personal Data Protection (CPDP, cpdp.bg); UK: Information
  Commissioner's Office (ICO, ico.org.uk); Germany: the relevant state authority;
  Ireland: Data Protection Commission. Confirm the name on the authority's own site.
- UK users: replace the regulation line with the UK GDPR / DPA 2018 wording. Article numbers stay the same.
- US companies with no EU establishment may ignore GDPR. Say so honestly, and add the US site's
  own opt-out link to the reply plan, plus the CCPA/state-law request form if the company offers one.
- Show the full draft with the phone number and email **masked** in the chat preview
  (`+359 *** *** *12`, `y***@gmail.com`). Put the real values only into the letter that is actually sent.
- Log each letter in `ledger.csv` with `status=letter_sent` and `due` = sent date + 1 month.
