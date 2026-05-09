# Toolkit Audit

A file-by-file critical pass on the existing job-application-toolkit. Every score is honest, not encouraging.

**Scoring legend:**
- **Usability (1-10):** Would a non-technical person succeed first try, with no Googling?
- **Consistency (1-10):** Would two identical runs of the same input produce identical output?

---

## README.md

**Summary:** Top-level entry point. Explains why the toolkit exists, the two-stage workflow (onboard once, apply per role), how to install Python dependencies, and how to contribute.

**Usability:** 5/10
**Consistency:** N/A (documentation, no output)

**Problems:**
1. Assumes the reader knows what a markdown prompt is, what YAML is, what a folder structure means, and how to "paste an entire file" into Claude. None of these are explained.
2. Assumes shell access ("Cowork mode") without explaining what Cowork mode is or how to use it.
3. The "local install" path requires a terminal, `pip`, `python --version`. Anyone who has never used a terminal stops here.
4. The role-layout block (TITLE | COMPANY | DATES) is asserted as a hard rule in README without explaining that the user's `format.md` cannot actually change it (because `render.py` is hard-coded against this layout).
5. Says "you own everything" but then hard-codes layout in `render.py`, which the user does not own in any meaningful sense.
6. No screenshot, no diagram, no example output. A reader cannot picture what they will get.
7. "Cowork mode does, by default" — undefined acronym for most readers.
8. The contributing rules ("Plain language. No corporate filler.") are good but appear at the bottom where contributors will not see them before reading every other file.

---

## USAGE.md

**Summary:** Step-by-step walkthrough from "I downloaded the folder" to "I sent the application," covering onboarding, applying, rendering, and a typical-run timing estimate.

**Usability:** 4/10
**Consistency:** N/A

**Problems:**
1. "Open the onboarding file in any text editor. Select all of it. Copy. Paste it into the Claude chat" assumes the reader has done this before. They have not.
2. "attach all the files with the paperclip button, or in Cowork mode tell Claude where the folder is on your computer" — three different attach mechanisms collapsed into one sentence with no screenshots.
3. "If you only have the new selection JSON and need a quick browser-based render, the cleanest path is to install Python" — admits the artifact fallback is broken without saying so plainly. A non-technical user trying to use the artifact will fail silently.
4. The "When something is wrong" troubleshooting table is good but assumes the user already knows where every file lives.
5. No instructions on how to actually "save the JSON as `selection.json`." Where? With what app? In what folder? Any text editor will save it as `.txt` by default on Mac/Windows.
6. "Open the resulting `.docx` files in Word." Assumes the user has Word. Mentions Pages and Google Docs as alternatives only later in passing.
7. "Re-run apply.md" shows up many times in troubleshooting. The user has to remember the entire flow each time.
8. No section explaining how to use the toolkit if you are NOT in NZ or UK and your CV conventions differ.

---

## DESIGN.md

**Summary:** Designer's notes. Records architectural decisions (three layers, YAML source-of-truth, select-don't-regenerate), the three rewrites the system went through, and open questions worth revisiting.

**Usability:** 7/10 (for a technical reader; not aimed at end users)
**Consistency:** N/A

**Problems:**
1. The "open questions" section (line 143-148) explicitly admits four unresolved structural issues, including that the HTML artifacts are now out of step with the new candidate-profile model. This is the audit's finding too, but DESIGN.md frames it as "worth revisiting" instead of "broken."
2. Claims that `render.py` "reads format rules from format.md" (line 75). It does not. It reads four small fields and ignores the rest. This is a documentation lie.
3. Says voice.md is "binding constraint" (line 102) but acknowledges later (line 121) that there is no validator. The text contradicts itself.
4. Section "What was deliberately not built" is honest about scope cuts but does not flag that the cuts include critical features (voice validation, true format reading).
5. No mention of the international audience problem: the design notes assume NZ/UK conventions throughout.

---

## apply.md

**Summary:** Per-application prompt. Reads candidate profile + JD, runs a fit assessment, produces a single selection JSON containing tailored headline/summary, IDs of selected content, and cover letter prose.

**Usability:** 6/10 (for the end user, who only pastes it; the prompt itself is well-written)
**Consistency:** 4/10 (output schema and tone are mostly stable, but Claude still has discretion on prose)

**Problems:**
1. **Schema mismatch with cv-artifact.md and cover-letter-artifact.md.** apply.md produces:
   ```
   { tailored: {headline, summary}, selection: {...}, cover_letter: {...} }
   ```
   But cv-artifact.md expects:
   ```
   { header, profile, competencies, roles, early_career, education, awards, volunteering, sectors, platforms, voice_meta, format_meta }
   ```
   These are completely different shapes. The artifact path is broken end-to-end.
2. **Voice.md is "binding" but never enforced.** The prompt says hard rules, but there is no programmatic check. Claude is expected to police itself.
3. **No voice tripwire.** After generating the cover letter prose, the prompt does not scan its own output against the prohibition list. The em-dash check exists only in the cover-letter-artifact.md fallback (which is broken anyway).
4. **`include_sections` allow-list is fixed in the prompt.** If the user's `format.md` requires a "publications" section, apply.md tells the user it is "not yet supported" and asks how to handle it. This is a friction point with no path forward.
5. The cover letter structure (opening, career_fit, evidence, closing) is described in prose, not enforced as a schema. Two runs may interpret "evidence paragraph" differently.
6. "Salutation per voice.md" — voice.md does not have a salutation rule by default. So Claude has to guess.
7. "Do not echo the job description's language back" is good intent but un-checkable.
8. "Do not soften gaps in the fit assessment" — cannot be enforced by the prompt itself.

---

## render.py

**Summary:** Deterministic .docx renderer. Loads the candidate-profile folder + selection JSON, assembles a CV or cover letter, writes a .docx file with locked fonts, colours, margins, and section structure.

**Usability:** 3/10 (requires Python, terminal, pip; non-technical users cannot run it)
**Consistency:** 9/10 (same input produces identical output; this is the strongest part of the system)

**Problems:**
1. **`parse_format_rules` reads almost nothing.** It pulls `page_length` (defaults to 4 — and is then ignored entirely; render.py does not enforce page length), `section_order` (defaults to a hard-coded list — and is then ignored unless `format.md` text matching produces a result, which it never does), `headings_allcaps` (a string-match heuristic), `colour_hex` (regex on the whole file), and `bold_lead_ins` (string match). Every other rule in `format.md` is decorative.
2. **Section order comes from `selection.include_sections`, not from `format.md`.** This means `format.md` says one thing and the renderer follows the selection JSON. The user thinks they are setting section order in their format file; they are not.
3. **Hard-coded layout rules.** `build_role` enforces TITLE | COMPANY | DATES on one line, italic company bio, "Responsibilities" then "Achievements." `format.md` cannot change this without editing `render.py`.
4. **Hard-coded font, margins, font sizes.** FONT = "Arial". Margins = 0.85 inch. None of this is in `format.md`.
5. **Hard-coded SECTION_TITLES dict.** If a user's format.md says "Skills" not "Key competencies and recent achievements," `render.py` ignores the rename.
6. **Visual divergence from cv-artifact.md.** render.py uses Arial, 0.85" margins, 24pt name. cv-artifact.md uses Helvetica Neue, 18mm margins, 24pt name. Same input produces different documents depending on which renderer is used.
7. **No page-length enforcement.** Claims 4 pages on A4 but does nothing if the content overflows.
8. **No graceful failure if YAML is malformed.** `_load_yaml` returns None silently for missing files, but a malformed YAML file will throw an opaque traceback.
9. **No validation of selection JSON shape.** Missing keys default silently to defaults, which can mask user errors.

---

## render.md

**Summary:** Wrapper prompt that has Claude run `render.py` for the user inside a Cowork-like session, with shell access. Tells Claude to install dependencies, save the selection JSON, run the renderer, hand back the files.

**Usability:** 5/10 (assumes shell access is available; vague on what to do if it is not)
**Consistency:** 7/10 (same JSON in, same docx out, given the renderer works)

**Problems:**
1. "If you do not have shell access, tell the user they need to either run the renderer locally themselves … or use the HTML artifact prompts as a fallback." The HTML artifact path is broken (schema mismatch). This recommendation is misleading.
2. Tells Claude to run `pip install -r requirements.txt` without a venv. On a system Python, this can fail on macOS Sonoma+ unless `--break-system-packages` is used (the prompt mentions this fallback, fine).
3. Assumes the toolkit folder path is "obvious from the conversation," which it usually is not.
4. No instruction to check Python version. The script needs 3.9+; an older Python will error on the `from __future__ import annotations` plus `dict[str, Any]` style annotations.
5. No instruction on what to do if `render.py` itself errors (other than "surface the full error text").

---

## cv-artifact.md

**Summary:** HTML artifact CV renderer. Designed to be the "shell-free" path: paste a JSON object, Claude substitutes it into a single template slot, the artifact renders the CV in the browser with print-to-PDF.

**Usability:** 6/10 (browser path is friendly, but only if the JSON works)
**Consistency:** 8/10 (within a single browser session and after the substitution; cross-browser pagination still drifts)

**Problems:**
1. **Schema is incompatible with apply.md.** Expects `{header, profile, competencies, roles[…], early_career, education, awards, volunteering, sectors, platforms, voice_meta, format_meta}`. apply.md produces `{tailored, selection, cover_letter}`. The artifact will never receive a JSON it can render unless the user manually translates it. DESIGN.md acknowledges this; the artifact still ships unfixed.
2. **Different visual rules from render.py.** Helvetica Neue vs Arial, 18mm margins vs 0.85", same name size by coincidence. The "consistent output" promise breaks across renderers.
3. **Role layout differs from render.py.** Artifact puts company on left, dates on right at the top, title on a separate line below; render.py puts title-pipe-company on left, dates right, with company bio italic next. Two different "locked" layouts.
4. **No voice tripwire here either** (despite the file claiming voice safeguards in section 9). The "stop and tell the user" instruction for em-dash detection is interpreted by Claude, not enforced.
5. **`__CV_JSON__` substitution is fragile.** A literal `__CV_JSON__` string anywhere in the JSON body would break the substitution. Unlikely but unhandled.
6. **Print-to-PDF pagination is browser-dependent.** The promise of consistency does not hold across Chrome / Safari / Firefox.

---

## cover-letter-artifact.md

**Summary:** HTML artifact cover-letter renderer. Same pattern as cv-artifact.md.

**Usability:** 5/10
**Consistency:** 7/10

**Problems:**
1. **Schema incompatible with apply.md.** Expects `{applicant: {name, location, phone, email, date}, recipient, salutation, opening, career_fit, evidence, closing, signoff, voice_meta}`. apply.md produces a `cover_letter` key with `recipient`, `salutation`, `opening`, etc., but no `applicant` block — header info lives in the candidate profile, not in this JSON. Manual translation required.
2. **The em-dash tripwire is interpretive.** "If the JSON contains an em dash and the user's voice file forbade them … stop and tell the user." Claude either notices or does not. There is no regex.
3. **Visual rules differ from render.py letter rendering.** Letter font sizes, margins, line height all diverge.
4. Same `__COVER_LETTER_JSON__` fragility.

---

## requirements.txt

**Summary:** Pins `python-docx==1.1.2` and `PyYAML>=6.0`.

**Usability:** 8/10 (single line, standard pip)
**Consistency:** 10/10 (pinned)

**Problems:**
1. PyYAML is loose-pinned (`>=6.0`); python-docx is hard-pinned. Inconsistent.
2. No mention of Python version requirement.

---

## onboard-executive.md

**Summary:** ~430-line interview prompt for senior leaders. Eight sections covering arc, scale markers, transformation scope, board exposure, governance, voice, format, filter. Outputs ten files into the candidate-profile folder.

**Usability:** 7/10 (the conversation works; the YAML output instructions assume the user can save files with paths)
**Consistency:** 5/10 (the conversation will produce different files for different runs of the same person, because the questions are open-ended; YAML format itself is consistent)

**Problems:**
1. **format.md is not co-created; it is filled in.** The format section asks specific yes/no questions ("page length?", "photo?", "bullet style?") and then the user is handed a fixed template structure. This is a form, not a conversation. The Phase 1 spec calls this out: "It must co-create. Flag clearly if it does not."
2. **format.md template is hardcoded.** Even if the user wants a wildly different format (e.g. a portfolio-style CV with project tiles), the template forces ten predetermined headings.
3. **voice.md asks good questions but the output template is generic.** "How I want to sound" / "Phrases I use" / "Phrases to avoid" — the prohibition list is not explicitly required to be specific. A user who answers "I don't like buzzwords" gets a useless rule.
4. **filter.md template assumes financial/professional services framing.** Sectors-in-and-out, ownership types (PE, founder-led), board access — these are senior-leader-in-large-org assumptions. A senior leader in NFP, government, or academia would find the categories awkward.
5. **No country/market question.** Asks NZ/UK/US/AU only as a "spelling and conventions" subquestion in the voice section. Does not ask the bigger question: what market are you targeting, and what does a CV in that market expect (page length, photo, personal info, sequence, etc.)?
6. **No co-creation of cover letter structure.** Onboarding produces voice.md and format.md but never asks about cover letter preferences (length, opening style, formality, signoff).
7. **YAML output assumes user is comfortable with paths and editors.** "Save them with the exact paths shown above" is fine for a developer, intimidating for a CFO who has never opened a text editor.
8. **The example phrase voice test (A/B/C) is very good** but the output voice.md does not preserve which one the user picked or why. The signal is lost.
9. **No section asking the user to share two or three pieces of writing they are proud of.** The Phase 1 spec calls for this and it is missing.
10. **Sample sentences are NZ/UK biased ("transformed the technology function," "board-trusted capability"). A US tech operator might not recognise the register.

---

## onboard-midcareer.md

**Summary:** ~410-line interview prompt for 5-15-year professionals. Same shape as the executive prompt, with mid-career framing (delivery track record, progression story, team scope rather than board exposure).

**Usability:** 7/10
**Consistency:** 5/10

**Problems:**
1. Same format.md and voice.md template-fill issue as the executive prompt.
2. Same sample-sentence Anglocentric framing.
3. Same lack of country/market specificity.
4. **Heavy duplication with onboard-executive.md.** Sections 6, 7, 8 and the output section are near-verbatim copies. Maintenance burden, drift risk.
5. **No co-creation of cover letter structure.**
6. The "progression story" section (4) is good and unique to this tier.
7. "Photo on the CV? (Default no)" — defaults are NZ/UK norms. In Germany or Japan, photo is expected.

---

## onboard-grad.md

**Summary:** ~420-line interview prompt for early-career people (0-5 years). Adds a Projects section and an Ambition framing section.

**Usability:** 7/10
**Consistency:** 6/10

**Problems:**
1. Same format.md and voice.md template-fill issue.
2. Same heavy duplication with the other two onboarding files.
3. **Education section assumes university degree.** A self-taught developer or a tradesperson moving into tech would need to skip half of section 2.
4. **Bias toward white-collar trajectories.** "Hackathons. University clubs. Personal coding projects. A blog." All very tech-graduate-shaped. A nursing or accounting graduate would have nothing to volunteer here.
5. **NCEA reference (line 61) is NZ-specific** without flagging it for international readers.
6. **Default page length 1.** Hard-coded as right answer in the prompt. In some markets, a 2-page CV at this stage is normal.

---

## candidate-profile-template/config.yaml

**Summary:** Example identity, default headline, default summary, target roles, sectors, platforms. Contains the candidate's real personal data.

**Usability:** 8/10
**Consistency:** 10/10

**Problems:**
1. **Real personal data in a public template.** Phone number, email, full name, LinkedIn. If this folder is checked into git or distributed, it leaks PII.
2. The template should be either obviously fake placeholders (NAME GOES HERE) or anonymised.

---

## candidate-profile-template/competencies.yaml, roles/, early-career.yaml, education.yaml, awards.yaml, volunteering.yaml

**Summary:** Worked example of every YAML structure. Demonstrates stable IDs, scale markers, evidence prose, role layout.

**Usability:** 8/10
**Consistency:** 10/10

**Problems:**
1. Same PII issue as config.yaml.
2. Same structural concern: this template is the only documentation of the YAML schema. There is no schema file, no JSON Schema, no validator. A user editing the YAML directly can introduce subtle errors that only surface at render time.
3. role files have inline YAML comments at the top (good), but the comments are mostly framing ("apply.md never invents a new achievement") rather than schema description.

---

## candidate-profile-template/voice.md

**Summary:** Example voice file. "Warm, direct, confident." Lists phrases to use, phrases to avoid, hard rules (no em dashes, no invented numbers), sentence patterns, and three voice-test sentences.

**Usability:** 9/10 (if the user follows the format)
**Consistency:** 5/10 (Claude interprets these rules at apply time; phrase prohibition is a soft check)

**Problems:**
1. **The good prohibition list is the right idea, but it is not enforced.** No regex check, no automated scan. This is the "voice tripwire" gap.
2. "Drove (overused)" — the rule is qualified (overused) which makes enforcement ambiguous. Hard rules should be hard.
3. "Genuinely, honestly, straightforward" forbids three words including "straightforward" — but "straightforward" appears legitimately in some contexts. Risk of false positives if a tripwire ever exists.

---

## candidate-profile-template/format.md

**Summary:** Example layout spec. Page length 4. Section order numbered. Bullet style, length, bold lead-ins, date format, tense, headings, colour, role layout.

**Usability:** 9/10 (read like a spec sheet)
**Consistency:** 3/10 (most of these rules are not actually read by render.py)

**Problems:**
1. **render.py only reads `page_length` (then ignores it), `section_order` (then ignores it because selection.include_sections wins), `headings_allcaps` (string match), `colour_hex` (regex), `bold_lead_ins` (string match).** Every other rule in this file (bullet length, bullet style, date format, tense, role layout) is window dressing.
2. The user is given a false sense of control. They edit format.md, the output does not change, they do not know why.
3. No schema for format.md, so render.py cannot reliably parse it. Currently it text-matches lower-cased substrings, which is fragile.

---

## candidate-profile-template/filter.md

**Summary:** Example role filter. Seniority range, sectors in/out, ownership types, geography, comp floor, culture signals, dealbreakers, what kind of role they are most drawn to.

**Usability:** 9/10
**Consistency:** 5/10 (Claude interprets at apply time)

**Problems:**
1. apply.md uses this for the fit assessment. Good. But there is no schema, so apply.md text-matches and reasons over it. A user could write filter.md in any prose shape and break the matching.
2. "Dealbreakers" are described in plain English. Without machine-readable structure, apply.md cannot enforce them deterministically. Two runs may reach different conclusions about whether a JD trips a dealbreaker.

---

## Summary table

| File | Usability | Consistency | Critical issues |
|---|---|---|---|
| README.md | 5 | N/A | Assumes technical reader; international audience ignored |
| USAGE.md | 4 | N/A | Assumes Claude/file-handling knowledge; vague at key steps |
| DESIGN.md | 7 | N/A | Documentation contradicts the code in places |
| apply.md | 6 | 4 | JSON schema mismatch with artifacts; no voice enforcement |
| render.py | 3 | 9 | Format rules ignored; non-technical user cannot run |
| render.md | 5 | 7 | Recommends broken artifact fallback |
| cv-artifact.md | 6 | 8 | Schema incompatible with apply.md; visual divergence from render.py |
| cover-letter-artifact.md | 5 | 7 | Same schema issue |
| requirements.txt | 8 | 10 | Minor pin inconsistency |
| onboard-executive.md | 7 | 5 | Format/voice template-fill not co-creation |
| onboard-midcareer.md | 7 | 5 | Same as executive; heavy duplication |
| onboard-grad.md | 7 | 6 | Same; tech-grad bias |
| candidate-profile-template/ | 8 | 10 | PII in public template; no schema validator |
| voice.md template | 9 | 5 | Prohibitions not enforced |
| format.md template | 9 | 3 | Most rules are decorative |
| filter.md template | 9 | 5 | Free-form prose, not machine-checked |

## Top issues by severity

**CRITICAL** (blocks the system from working as documented):
1. JSON schema mismatch between apply.md and the two artifacts. The fallback path is broken end to end.
2. format.md is decorative. Most rules in it have no effect on output.
3. No voice tripwire. "Voice.md is binding" is a marketing claim, not a feature.

**MAJOR** (the system works but the user is misled or limited):
4. render.py has hard-coded layout that the user cannot change without editing Python.
5. Onboarding hands the user format.md and voice.md templates rather than co-creating them.
6. International users are silently assumed to want NZ/UK conventions.
7. Heavy duplication across the three onboarding files.
8. PII in the candidate-profile-template/ folder.
9. The HTML artifacts visually diverge from render.py output.

**MINOR** (rough edges):
10. requirements.txt pin inconsistency.
11. No page-length enforcement.
12. No YAML schema, no validator.
13. No country/market question in onboarding.
14. Cover letter structure not enforced as a schema.

---

End of audit. Ready to proceed to architecture review.
