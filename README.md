# USPTO Office Action Response Skill

A Claude skill that helps an attorney analyze, strategize, and draft a response to a USPTO Office Action on a trademark application the attorney filed. It is built for practicing lawyers, not for first-time filers: it skips basic explanations, flags weaknesses and counterarguments without being asked, and refuses to guess at citations.

## What it does

Given an Office Action (non-final or final), the skill produces:

1. **Record check.** Current TSDR status for the application and every cited registration or prior-pending application.
2. **Deadline computation.** Trigger date, governing rule, day-count, nominal and rolled dates, extension availability, and consequence of default, in a standard block for every deadline.
3. **Issue-by-issue triage.** Each refusal or requirement sorted into curable by amendment, requires argument and evidence, or fatal or near-fatal.
4. **Candid strategy memo.** Options for each contested issue (argue, amend, disclaim, divide, substitute specimen, Supplemental Register, amend basis, suspend), with strongest argument, strongest counterargument, evidence needed, probability range, scope-of-protection effects, and cost.
5. **Draft response.** A .docx in the user's voice, organized by issue and formatted for transfer into TEAS.
6. **Verification pass.** An independent check of every cite, rule, and deadline before the draft is delivered.
7. **Filing handoff.** Filing checklist, docket entry, and follow-up reminders.

## Issues covered

- Section 2(d) likelihood-of-confusion refusals
- Section 2(e) descriptiveness and geographic refusals
- Specimen refusals
- Identification of goods and services requirements
- Disclaimer requirements
- Ownership and applicant-entity issues
- Requests for information under 37 C.F.R. § 2.61(b)
- Prior-pending-application advisories and suspension notices
- Final refusals: request for reconsideration versus appeal to the TTAB

## What it does not cover

- Filing a new application
- Post-registration maintenance filings (Sections 8, 9, 15)
- TTAB inter partes proceedings
- Foreign trademark offices

## Workflow

| Step | Task | Where it runs |
|---|---|---|
| 1 | Intake and record check | Parallel subagents (one per cited mark) |
| 2 | Deadline computation | Main thread |
| 3 | Issue-by-issue triage and examiner-evidence audit | Main thread, with one audit subagent |
| 4 | Strategy memo and evidence gathering | Main thread, with parallel evidence subagents |
| 5 | Client decision point | Main thread (stops for the attorney's go-ahead) |
| 6 | Draft the response | Main thread, single pass |
| 7 | Verification pass | Fresh subagent, then main-thread reconciliation |
| 8 | Filing handoff and tracking | Main thread |

## Parallel agents

Subagents are used only when the Office Action raises a 2(d) or 2(e) refusal or three or more issues. For a simple specimen refusal, identification requirement, or disclaimer requirement, the skill works directly.

| Agent | Task | Why it is isolated |
|---|---|---|
| A | Cited-mark and record research, one agent per cited registration | Lookups are fully independent |
| B1-B4 | Third-party registrations, third-party use, dictionary and usage evidence, trade channels | Research-heavy and independent |
| C | Examiner-evidence audit | Sees only the Office Action and the examiner's evidence, so it is not biased by strategy |
| D | Verification pass | Has not seen the drafting, so it checks citations instead of confirming them |

Deadline computation, triage, strategy, the client decision point, and drafting stay in the main thread. Strategy decisions interact with each other, and parallel drafters produce inconsistent voice and contradictory arguments.

## Built-in safeguards

- **No invented facts.** Parties, serial numbers, dates, amounts, and examiner names appear as bracketed placeholders until retrieved.
- **No guessed citations.** Unverified TMEP sections and rule subsections appear as `[TMEP § ___ - verify]`.
- **Confidentiality.** Client names and confidential facts stay out of every search query and every subagent brief. Subagents get generic descriptions of goods, services, and mark type only. Public serial and registration numbers are permitted for TSDR lookups.
- **Source confidence ratings.** Every substantive response ends with a section rating each case, statute, rule, deadline, date, dollar figure, and quotation as VERIFIED, RECALLED-HIGH, RECALLED-LOW, or UNVERIFIABLE. Nothing is marked VERIFIED unless the source was actually retrieved.
- **Deadline discipline.** The issue date controls, not the date of receipt. The skill calendars the nominal date, treats weekend roll-forward as a margin only, and flags any deadline within 14 days or already past.
- **Fees omitted.** The skill uses `[CURRENT FEE - verify on USPTO fee schedule]` rather than a remembered figure.

## Installation

1. Copy `SKILL.md` into a folder named `uspto-office-action-response` in your Claude skills directory.
2. Confirm the skill appears in your skill list.
3. Start a conversation, attach or link the Office Action, and ask for an analysis or draft response.

## Dependencies and pairings

- **Voice.** Drafts that leave the office use a voice skill (referenced as `my-voice-skill`). Replace the reference with your own voice skill, or remove it.
- **Letterhead.** `law-firm-letterhead` is applied only for cover letters or client advisories. Replace or remove it as needed.
- **Connectors (optional).** Document retrieval from MyCase, Dropbox, Gmail, or Google Drive, and Google Calendar for deadline events.
- **Tools.** Web search and fetch for research. If TSDR cannot be reached with those tools, the skill reports it and falls back to a browser.

## Customizing

The skill is written for a Texas civil litigator, so the ground rules, voice, and output formats reflect one attorney's preferences. Before adopting it:

- Revise the "Purpose" and "Ground rules" sections for your practice and jurisdiction.
- Replace the voice and letterhead references.
- Adjust the source-confidence format if you prefer a different convention.
- Update the deadline table whenever the USPTO changes response periods, extensions, or fees.

## Limitations

- **Rules change.** The response period for Office Actions changed in December 2022. The deadline table flags every rule for verification against current text. Do not rely on it without checking the current C.F.R. and USPTO guidance.
- **Madrid Protocol (Section 66(a)) applications.** The skill does not state a response period for these. It directs the user to confirm.
- **Not legal advice.** The skill supports an attorney's own professional judgment. The attorney remains responsible for every filing, declaration, and deadline.
- **TSDR access.** Subagent access to TSDR through the available tools has not been tested. Expect to use a browser fallback in some sessions.

## License

MIT. See [LICENSE] (LICENSE) 

## Copyright 

Copyright (c) 2026 Bradley B. Clark

## Author

<a href="https://www.x.com/bradleybclark" target="_blank" rel="noopener">Bradley B. Clark</a><p>
<a href="https://www.bradleybclark.com" target="_blank" rel="noopener">Bradley B. Clark Law Group, P.C.</a>
