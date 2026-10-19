# Phone Number Takeback — Spam Source Removal

## Purpose
A recycled phone number inherits its previous holder's digital footprint: business-directory listings, people-search records, data-broker files, recruiter databases, and political voter data. The new owner gets spam calls and texts meant for someone else. This skill removes the number **at the source** — every listing, broker record, and database that publishes or sells it — instead of blocking/filtering on the phone.

Use placeholders throughout a run: `<PHONE>`, `<CURRENT_OWNER>`, `<OWNER_EMAIL>`, `<OWNER_ADDRESS>`, `<PREV_OWNER>` (filled in only after Phase 1 identifies them). **Never write real identifiers into a shared/published copy of this skill or its notes.**

## When to use
- The user gets calls/texts addressed to a different person on their own number.
- The user asks to "stop the spam for good", "clean up my number", or remove their number from directories/brokers.
- NOT for ordinary telemarketing to the user themselves (that is just Do Not Call + carrier tools); this playbook is for **wrong-person spam caused by a previous holder**.

## Hard constraint (ask first, then honor it)
Many users cannot use phone-side blocking/filtering (e.g., they must receive calls from unknown numbers — schools, doctors, clients). Default to **source removal only**; do not propose call-filtering apps, "silence unknown callers", or carrier spam shields unless the user explicitly wants them.

## Operating rules
1. **Diagnose before deleting.** Every spam stream has a source. Removing a source you misidentified wastes the one lever you have (some flows are single-use).
2. **Log everything** in a working folder: `listings.md` (every place the number appears), `status.md` (dated actions, confirmations, case numbers), `other-sources.md` (sweep inventory). Future sessions resume from these files.
3. **Batch every human-only step** (CAPTCHAs, code approvals) into one short session with the user. Directory and broker sites are heavily bot-walled; an automated browser will burn hours if it retries alone.
4. **Verification windows are real.** Most removals take days (directories ~3 days, broker requests ~45 days, state platforms up to 90 days for profile updates). Record the promised window and re-verify after it passes; a submitted request is not a removed record.
5. **Codes and CAPTCHAs:** relay one-time codes through the protected channel when the platform supports it; split-box code fields must be filled one digit at a time (bulk fill fails on them); hCaptcha/reCAPTCHA/Cloudflare checks on people-search sites usually require the user to click once inside the live browser session — after that, the session's clearance cookie makes later pages pass.
6. **Direct contact with the previous holder is the weakest lever.** They often cannot edit recruiter/broker copies of their own data, and frequently don't respond. Prefer institutional channels (platform privacy teams, state systems, directory support). Try direct contact once at most, in parallel, never as the critical path.
7. Nothing is purchased, claimed, or sent without the user's approval of the exact action/wording, per the user's standing rules.

## Phase 0 — Classify the spam
Sort 2–4 weeks of spam (ask the user for screenshots/samples) into streams; each implies a source class:
- **Party/event/business inquiries** → the previous holder ran a business under this number → business directories (Phase 4).
- **Job/recruiter texts** (often naming the previous holder, sometimes a specific role/industry) → resume databases and recruiter lists (Phase 6).
- **Political texts** (candidate/PAC, often a specific county/state) → voter-file data sold by political data vendors (Phase 5). The county named usually matches the previous holder's registration.
- **Healthcare/finance offers tied to a profession** → professional directories and licensing-adjacent databases (check in Phase 1; many turn out clean and are fed by the resume stream instead).

## Phase 1 — Identify the previous holder
1. Web-search the number in quotes, plus the business names seen in spam. Interview/profile articles ("Meet X of <Business>") often print the number next to a full name — one such article can unlock the whole map.
2. Check professional registries when a profession is implicated (state license lookup, NPI registry for healthcare): they confirm identity, license numbers, and practice addresses — and often show a *different* phone, proving the directory isn't the source.
3. People-search sites (Phase 3) may attach the number to an even earlier holder's name. Treat that as a secondary record to remove, not necessarily the main actor.
4. Output: previous holder's name(s) (incl. maiden/married variants), business name(s), city/county of registration, profession. Every later phase keys off these.

## Phase 2 — Baseline registrations (do these first, they're free and broad)
1. **National Do Not Call Registry** (donotcall.gov): register the number; wait for the confirmation email and click it. Note the limits: it does not cover political, charitable, or survey calls, nor businesses with an existing relationship.
2. **State deletion platform — California DROP** (consumer.drop.privacy.ca.gov), the single highest-leverage step where available:
   - Flow: accept terms → identity verification via AccessCA (name, DOB, full address, email) → email verification code → build the request in "Create Request" → verify email (and phone, if added) → Review and submit → save the DROP ID from the confirmation page.
   - **Include every identifier**: name, DOB, ZIP, email, **and the phone number itself** — brokers match on the phone.
   - **Gotcha:** the app (Blazor) drops connections and reloads; a reload can silently lose a field (we lost the phone number this way and the request went in without it). Before submitting, re-read the Review screen; afterwards, check "My Data Profile" and add anything missing — additions trigger their own verification (SMS code for a phone) and process on the next 45-day cycle (up to 90 days).
   - One submitted request fans out to **all** registered brokers (hundreds) and future registrants; status page shows per-broker pending counts.
3. Check whether the user's state has an equivalent (a few states run opt-out portals); if not, Phase 5's per-vendor emails are the substitute.

## Phase 3 — People-search opt-outs
1. Build the target list from `listings.md`: search the number on each major people-search site (TruePeopleSearch, USPhonebook, FastPeopleSearch, Spokeo, BeenVerified, Whitepages, Radaris, ThatsThem, …). Record which names the number is attached to — expect the current owner **and** one or more previous holders.
2. Per site, expect a different wall: full-site Cloudflare (ThatsThem), DataDome (Yelp), looping reCAPTCHA (YellowPages), or hCaptcha at two separate steps (TruePeopleSearch). Plan for the user to pass checks inside the browser session; do not brute-force retries.
3. **TruePeopleSearch pattern** (representative of the email-link family):
   - Step A: "Do Not Sell / Right to Opt-out" requester form (requester's own name/email) → hCaptcha (human) → site emails a form link (valid 24h).
   - The link in the email is tokenized; if your mail tooling redacts tokens, ask the user to copy the link from their inbox and paste it — that is the reliable path.
   - Step B: the linked form identifies **one record per token** (name, phone, street address *or* DOB, ZIP — the record's own listed address works) → hCaptcha again (human) → submit → confirmation promises removal "in 3 days or less".
   - **One token = one record.** A second record (e.g., a previous holder's) needs a fresh run of the whole flow with a new email link; a reused token fails with "Request failed".
   - Decision point: removing previous holders' records is low-value (their records don't generate the spam; the big platforms in Phases 2/5 do). Offer it, let the user skip it.
4. Sites with a working self-serve opt-out (USPhonebook, FastPeopleSearch class): submit, save the confirmation, and re-verify after their stated window.

## Phase 4 — Business-directory cleanup
For each directory still showing the previous holder's business with the number:
1. Prefer the site's own "remove/close business" flow when it works logged-out.
2. **Closed-business phone route (very effective):** call the directory's business support line, state the business is permanently closed and the number now belongs to someone else, and ask for removal. Record the case number and the promised window (Yelp: 2–3 business days); verify on the promised date that the number is gone from the page.
3. Email/form removals (Manta, City-Data, BizNet-US class): send the removal request (template A), log the message ID, and watch the mailbox — replies can take up to 5 business days.
4. Known dead ends to log and stop retrying: listings "claimed" by the previous owner (YellowPages) where the claim blocks third-party edits and the feedback form's CAPTCHA is broken; sites with **no contact channel at all** (log as not removable via that site; the DROP request in Phase 2 is the backstop).

## Phase 5 — Data brokers & political data vendors
1. Send a wrong-number correction (template B) to the big political data vendors' privacy addresses (L2 — privacy@L2political.com; TargetSmart — privacy@targetsmart.com; The Data Trust — its "Your Privacy Choices" form). Ask them to detach `<PHONE>` from the previous holder's record and suppress it.
2. Set expectations in writing to the user: voter-file data comes from **government voter rolls**, which third parties cannot amend or delete; vendors can at best suppress a number in their own products, and some (i360 class) have **no consumer channel at all** — the state DROP request is the only lever there.
3. Political texts are exempt from Do Not Call. Practical mitigation: reply STOP per sender (teach the user; an agent usually cannot send SMS). Volume from a wrong registration typically collapses right after the relevant Election Day.

## Phase 6 — Resume & recruiter databases
1. Evidence first: a cooperative recruiter (or the user's own spam screenshots) can reveal which platform hosts the previous holder's resume (the big merged platform is CareerBuilder + Monster). Recruiters themselves cannot edit a candidate's resume.
2. Email the platform's privacy/compliance address from its own Privacy Notice (CareerBuilder/Monster: riskandcompliance@careerbuilder.com) with template C: identify the resume holder, the wrong number embedded in it, a dated spam instance as proof, and request removal/correction.
3. Expect the ceiling: if the platform answers "account holder only", the record stays — note it in `status.md` and stop; the remaining recruiter spam decays as the resume ages out of searches.

## Phase 7 — Sweep, verify, monitor
1. **Full web sweep** for the number in quotes: nonprofit/vendor PDFs, event pages, archived listings. Anything found gets a removal email (template A) — one real find per sweep is typical (e.g., a museum's old vendor-list PDF).
2. Verify the negatives too: professional directories (WebMD, Psychology Today, Zocdoc, Healthgrades, NPI) often list **no phone or a different phone** — record them as clean so nobody re-chases them.
3. **Not removable — log and move on:** editorial articles (interviews) and government records (public contract PDFs, the voter roll itself). De-indexing requests to search engines rarely apply to phone numbers.
4. Re-verify each channel after its window (directories ~3 days; Yelp case date; brokers at the 45-day mark; DROP status page). Keep a dated verification line per item in `status.md`.
5. Ongoing: watch the user's inbox for replies from every emailed channel and brief the user when one lands; new spam instances get logged with sender number and stream classification — a *new* stream means a *new* source, go back to Phase 0 for it.

## Templates

### A — Directory/listing removal request (email)
Subject: Removal request — business listing for a closed business / wrong phone number
"Hello — the listing for <BUSINESS NAME> shows phone number <PHONE>. That business is permanently closed, and the number has been reassigned to me (<CURRENT_OWNER>, <OWNER_EMAIL>). I am not affiliated with the business. Please remove the listing (or at minimum the phone number) from your site. Thank you."

### B — Data-vendor wrong-number correction (email)
Subject: Wrong-number correction and suppression request — <PHONE>
"Hello — your data associates phone number <PHONE> with <PREV_OWNER> of <COUNTY/STATE>. That number was reassigned and now belongs to <CURRENT_OWNER> in <STATE>, who has no connection to that record. Please detach <PHONE> from that person's record in your data and suppress the number from future products/sales. I can confirm ownership of the number if needed. Thank you."

### C — Resume-platform compliance request (email)
Subject: Request to remove a third party's phone number from a resume — <PHONE>
"Hello — a resume on your platform for <PREV_OWNER> contains phone number <PHONE>. The number was reassigned to me (<CURRENT_OWNER>); I continue to receive recruiter calls/texts intended for them (most recent: <DATE, SENDER, ROLE>). The resume holder has not responded to contact. Please remove or correct the phone number in that resume. Thank you."

## Deliverables per run
- `status.md` updated with every action, confirmation, case number, promised window, and verification date.
- A short user brief: what was removed, what is pending and until when, what is not removable and why, and the (batched, minimal) list of anything only the user can do.
