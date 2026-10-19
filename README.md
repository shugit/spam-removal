# Phone Number Takeback — Spam Source Removal (Agent Skill)

An agent skill for eliminating wrong-person spam on a recycled phone number by removing the number **at the source** — business directories, people-search sites, data brokers, resume databases, and political data vendors — instead of blocking or filtering on the phone.

## Why
When a carrier reassigns a number, the new owner inherits the previous holder's footprint: their business listings, broker records, and voter-file data keep circulating, and the spam (party bookings, recruiters, political texts) keeps coming addressed to a stranger. Phone-side filters don't fix this — and many people can't use them anyway, because they must receive calls from unknown numbers (schools, doctors, clients). This playbook cleans the sources.

## What the skill covers
- **Diagnosis**: classify spam streams (business / recruiter / political / professional) and trace each to its source class.
- **Previous-holder identification** from public traces (interviews, registries, directory records).
- **Baseline registrations**: National Do Not Call Registry; California's DROP platform (full walkthrough, including the field-loss-on-reload gotcha and the My Data Profile fix).
- **People-search opt-outs**: the two-step email-link family (e.g. TruePeopleSearch), one-token-one-record rule, and the CAPTCHA walls to expect.
- **Business-directory cleanup**, including the closed-business phone-call route that directories actually honor.
- **Data brokers & political vendors**: correction/suppression emails, and honest ceilings — voter rolls are government records no vendor can delete.
- **Resume databases**: compliance-team requests with proof of misdirected recruiter contact.
- **Verification discipline**: every removal has a processing window; the skill tracks confirmations and re-verifies.

## Usage
Give `SKILL.md` to your AI agent (it follows the common Agent Skills layout: a single `SKILL.md` playbook). The agent works through the phases, keeps `listings.md` / `status.md` logs, batches every human-only step (CAPTCHAs, verification codes) into one short session with you, and reports what was removed, what is pending, and what is not removable.

All names, numbers, and addresses in the skill are placeholders. Never publish a filled-in copy with real identifiers.

## License
MIT
