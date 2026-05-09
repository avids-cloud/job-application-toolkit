# Known issues

Issues identified during the review loop that were not resolved before launch. Each one has a suggested fix for a future contributor.

## github-release/

### KI-1. `bullet_target_words` is a soft target, not enforced

**Severity:** MINOR

**Symptom:** The user sets `bullet_target_words: 70` in their format.md spec block. apply.md does not warn if a generated bullet is significantly above or below the target. The artifact does not adjust.

**Why this matters:** Mid-career and executive CVs in some markets use bullets of consistent length as a visual cue. A user who specifies 70 expects ~70.

**Suggested fix:** Add a post-generation check in apply.md Step 2 that counts words per bullet and flags any bullet outside `[0.5x, 1.5x]` of the target. Surface in the same channel as the voice tripwire output.

### KI-2. `older_roles_treatment` is documentation, not enforcement

**Severity:** MINOR

**Symptom:** The format spec has `older_roles_treatment: condensed | omitted`. The artifact does not act on it. To actually omit older roles, the user removes `early_career` from `section_order`. The field is therefore redundant with `section_order`.

**Why this matters:** Confusing for users who set `older_roles_treatment: omitted` and expect early career to disappear.

**Suggested fix:** Either (a) remove the field and document the right way to omit older roles in CONTRIBUTING.md (already done partially), or (b) make the artifact respect the field by skipping the early_career section when set to `omitted`.

### KI-3. Compensation floor question phrasing assumes the user has a number

**Severity:** MINOR

**Symptom:** Onboarding section 8 asks "Compensation floor. Total package, not just base." A non-technical career changer or someone re-entering the workforce may not have a number in mind and feel forced to invent one.

**Suggested fix:** Add to onboarding: "If you do not have a number in mind, that is fine — leave this blank. You can come back to it later."

### KI-4. Voice tripwire structural checks are still partly interpretive

**Severity:** MINOR

**Symptom:** Phrase prohibitions are deterministic substring matches. "Sentence patterns to avoid" rules where the user wrote a description rather than a literal phrase ("Never start a bullet with abstract noun phrases") cannot be substring-matched. Claude still has to apply judgement.

**Why this matters:** Two runs might disagree on whether a sentence violates a structural rule.

**Suggested fix:** Restrict "Sentence patterns to avoid" to substring-matchable patterns in onboarding. If the user wants a structural rule, document that it will be applied with model judgement and flag the run as having interpretive output.

### KI-5. No automatic page-length enforcement

**Severity:** MINOR

**Symptom:** `format.page_length` is documentation. The artifact does not warn if the rendered CV overflows. The user has to print to PDF and check.

**Suggested fix:** A small JS check in the artifact that measures the rendered height and warns if it overflows the expected page count. Soft warning, not a render block.

### KI-6. HTML artifact pagination still browser-dependent

**Severity:** MINOR

**Symptom:** Print-to-PDF in the browser produces slightly different pagination across Chrome, Safari, Firefox. The toolkit promises consistency; this is a limit of browser print implementations.

**Suggested fix:** Document the recommended browser for printing (Chrome). Or build a server-side renderer (would break the "no Python dependency" rule of the github-release).

### KI-7. No internationalisation of cover letter recipient block

**Severity:** MINOR

**Symptom:** German formal letters expect specific recipient block formatting (sender top-right or top-left, date right, recipient block left, with subject line `Bewerbung als ...`). The cover letter artifact uses a single Anglo-Saxon layout.

**Suggested fix:** Add a `cover_letter.layout` field to the format spec (`anglo`, `german`, `french`) and have the artifact apply the right layout.

## Existing system

### KI-8. PII in candidate-profile-template/

**Severity:** MAJOR (but acceptable for the existing system, which is the user's personal toolkit)

**Symptom:** The template contains the user's real name, phone number, email, and LinkedIn. If the toolkit is forked or shared, the PII goes with it.

**Why this matters:** Future contributors might fork from this template. The github-release version uses an anonymised example to fix this.

**Suggested fix:** The user can either (a) move their personal data to a separate `candidate-profile/` folder outside the toolkit and replace `candidate-profile-template/` with anonymised placeholder data, or (b) add `candidate-profile-template/` to `.gitignore` if/when they put this under version control.

### KI-9. HTML artifacts for the existing system are out of step with the new schema

**Severity:** MAJOR (acknowledged in DESIGN.md as an open question)

**Symptom:** `cv-artifact.md` and `cover-letter-artifact.md` in the existing system expect the older self-contained JSON schema. apply.md now produces the new selection-JSON shape. The fallback path is broken.

**Why this matters:** Documented in audit.md and architecture-review.md. The existing system has render.py as the canonical assembler; the artifacts have been kept for the case where the user has no Python.

**Suggested fix:** Either (a) update the existing-system artifacts to consume the new selection JSON (significant rewrite, similar to the github-release artifacts), or (b) deprecate them and tell the existing-system user to use the github-release artifacts instead, or (c) leave as-is and document the limitation in USAGE.md (current state).

### KI-10. render.py does not enforce page length

**Severity:** MINOR

**Symptom:** Same as KI-5 but on the Python side. python-docx does not page; Word does. If the content overflows 4 pages on A4, render.py still produces a 5-page document.

**Suggested fix:** A post-render check that opens the .docx and warns if page count exceeds spec.

### KI-11. Voice tripwire only applies to apply.md output, not to onboarding output

**Severity:** MINOR

**Symptom:** Onboarding produces voice.md with prohibitions but does not check the voice.md itself against any meta-rules. A user who lists "passionate about" in "Phrases I never use" but accidentally includes "passionate" in their summary paragraph during onboarding gets caught at apply time, not at onboarding time.

**Suggested fix:** Add a final-step voice scan in onboarding that checks the generated default_summary and competencies prose against the voice prohibitions just produced.

---

## Cycle limit

The reviewer/reviser loop ran two rounds. The user's spec allowed up to three. No CRITICAL or MAJOR issues remain in the github-release version. The MINOR issues above are documented for future contributors.
