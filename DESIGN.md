# Design notes

A record of what was built, why, and the alternatives considered. Written so anyone reading the toolkit later can understand the choices behind it without re-deriving them.

## What this is, in one paragraph

A small system for writing tailored job applications using Claude. It exists because most CV-writing tools have three problems: they are black boxes, their output is inconsistent run to run, and your career data ends up on someone else's servers. The toolkit is a folder of markdown prompts plus a single anonymised example. No app, no install, no account.

## What was built

```
job-application-toolkit/
  README.md                       front door
  ONBOARDING.md                   atomic step-by-step walkthrough
  USAGE.md                        full reference walkthrough
  CONTRIBUTING.md                 design principles and rules for PRs
  DESIGN.md                       this file
  LICENSE                         MIT
  onboarding/
    onboard-executive.md          senior leader interview
    onboard-midcareer.md          5-15 year interview
    onboard-grad.md               early-career interview
  apply/
    apply.md                      per-role selection prompt with voice tripwire
    fit-assessment.md             standalone fit-assessment prompt
  render/
    cv-artifact.md                browser-rendered HTML CV, schema-aware
    cover-letter-artifact.md      browser-rendered HTML cover letter
  examples/
    example-profile/
      profile.md                  anonymised profile (Maya Chen, COO)
      voice.md, format.md, filter.md, cover-letter.md
    example-selection.json
  _audit/
    INVENTORY.md                  audit from this packaging pass
    audit.md                      file-by-file scoring (historical)
    architecture-review.md        canonical schema reference
    review-round-1.md             three-persona review log
    review-round-2.md             verification pass
    known-issues.md               unresolved issues with suggested fixes
```

## Architecture: the three layers

The system has three separated layers. Each has a single job. None is allowed to do another's work.

**Layer 1: source of truth.** Every fact about the candidate's career lives in `profile.md`. Each responsibility and achievement has a stable lowercase-with-dashes ID. The user edits the file directly when something changes. The toolkit never writes to layer 1 during application generation.

**Layer 2: selection and tailoring (`apply/apply.md`).** For each application, Claude reads `profile.md` plus the four rules files plus the job description, runs a fit assessment, and produces one JSON object. The JSON contains a tailored headline, a tailored summary paragraph, the IDs of which roles, responsibilities, achievements, and competencies to include, optional bullet-level rewrites for the rare case where a tailored framing genuinely helps, the cover letter prose, and the format spec the renderer should apply. After generating prose, apply.md scans its own output against the user's voice prohibition list and surfaces violations to the user before producing the final JSON.

**Layer 3: assembly.** The HTML artifacts in `render/` read the JSON (which already contains everything they need, including the format spec) and a JavaScript renderer in the browser produces the page. The user prints to PDF.

This separation is what makes the output consistent, the per-application token cost small, and the data ownership real.

## The canonical schema

The selection JSON produced by `apply/apply.md` is the canonical contract between layers 2 and 3. The full schema is documented in `_audit/architecture-review.md` and in `apply/apply.md`. The top-level shape:

```
{
  schema_version,
  format,
  header,
  tailored_summary,
  competencies, roles, early_career, education, awards, volunteering,
  sectors, platforms, languages,
  cover_letter,
  voice_check
}
```

Layout-relevant fields in the format spec:

- `page_size`, `page_length`
- `section_order`, `section_titles`
- `include_photo`, `include_personal_details`
- `headings_style`, `bullet_style`, `bullet_target_words`
- `role_layout` (split or merged), `include_company_bio`, `include_role_context`, `bold_lead_ins`
- `font`, `font_size_body`, `font_size_name`, `colour_hex`
- Four-side margins
- `older_roles_treatment`

Optional header fields:

- `header.photo_url` (rendered when `include_photo: true`)
- `header.personal_details` (rendered when `include_personal_details: true`)
- `header.languages` (rendered when `languages` appears in `section_order`)

## Design decisions and reasoning

### Markdown prompts, not an installable application

Distributing an app means hosting, accounts, terms of service, vendor lock-in. Every problem we set out to fix would come back. A folder of markdown files lives on the user's disk, can be opened in any editor, and works in any Claude conversation.

### Source-of-truth profile, not regenerated content

Regenerating the entire CV every application is wasteful and inaccurate (numbers drift, careful phrasing gets paraphrased away). Storing every fact once with stable IDs means the toolkit selects what to include rather than rewriting what already exists. A single `profile.md` is friendlier to a non-technical user than a multi-file structured tree, and markdown is universally readable.

### Co-create format.md, do not template-fill

The original onboarding handed users a fixed `format.md` template with predetermined headings. The user could change values inside but not change the shape. A senior leader who wanted a portfolio-style CV could not get one.

The fix: onboarding asks open questions about format (target roles, length, sections that matter, role layout, things they have been told, CVs they admire) and generates `format.md` from the answers, in the user's own words, with a machine-readable spec block at the bottom that the renderer reads.

### Format.md is genuinely the source of truth for layout

Earlier renderers text-matched a few fields and ignored the rest. The user thought they owned the format. They did not. The fix: a structured YAML spec block at the bottom of `format.md`, parsed by the artifacts. Hard-coded defaults remain as fallbacks only.

### Voice as constraints, not vibes

A voice file that says "warm, direct" is unenforceable. A voice file that says "never use 'results-oriented', never start a bullet with 'was responsible for', never use em dashes" is enforceable.

The fix: onboarding extracts real voice signals by asking the user for writing samples, asking what others say about their voice, asking what phrases they hate seeing on CVs, and asking them to read their current CV aloud. The output `voice.md` is a constraint document with explicit prohibition lists in the user's own terms.

### Voice tripwire as part of apply.md

Voice rules are useful only if they are enforced. Without enforcement, `voice.md` is documentation that drifts. The fix: apply.md scans every piece of generated prose against the prohibition lists before presenting the JSON, surfaces violations with suggested fixes, and waits for the user to choose how to handle each one.

### Cover letter as locked structure

Cover letters fail in predictable places, generic openings, "I would welcome the opportunity to discuss further" closings, filler phrases. Letting Claude pick the shape per application produces inconsistent output. The fix: a `cover-letter.md` file co-created during onboarding that locks the structure (length, opening pattern, closing pattern, salutation form, signoff form) and the prohibition list specific to cover letters.

### Headline and summary are regenerated; everything else is selected

The headline and the profile paragraph have to be tailored per application. They are the only places where a few hundred characters of fresh prose actually changes the application materially. Everything else benefits more from selection (which roles, which achievements) than from rewriting.

### Browser HTML for output

Browser print-to-PDF has zero install cost. Pagination varies slightly across browsers; the toolkit recommends Chrome.

### Adaptable for country, industry, and seniority

The original onboarding silently assumed UK/NZ conventions. International users got the wrong defaults.

The fix: onboarding section 0 asks the user's target market and adapts every later question. Page length, photo, personal details, profile summary, page size, spelling, date format are all market-dependent. Section titles are configurable in `format.section_titles` so a French CV can use French headings, a German CV can use German headings.

The schema supports `header.photo_url` for markets that expect a photo, `header.personal_details` for markets that expect DOB / nationality / marital status, and `header.languages` for markets where multilingual ability is a major signal.

The three onboarding tiers (executive, midcareer, grad) cover seniority. Within each tier, the conversation works for the full range. The exec prompt works equally for a first-time VP and a thirty-year CTO.

## What was deliberately not built

**A web UI.** Skipped on purpose. The whole point is that the user's data does not leave their machine.

**Automated voice validation across all forms.** apply.md runs a substring tripwire on its own output. Onboarding does not yet scan its own output (KI-11). A fuller validator using the user's voice samples to train a model is out of scope.

**A `publications` or `patents` section.** The section allow-list is fixed. Anything outside the list is either folded in or the toolkit tells the user it is not yet supported. Letting Claude invent new section keys produces silent rendering bugs.

**A native PDF or .docx renderer.** The artifacts produce browser-rendered HTML. Adding a server-side PDF library would expand setup complexity for marginal benefit. Adding a Python `.docx` renderer was tried and removed during packaging because it required a parallel data schema and conflicted with the "no install" goal.

**Multi-locale cover letter layouts.** Documented as a known issue (KI-7). German-style formal recipient blocks are not yet supported.

## How the design evolved

This toolkit went through four structural rewrites.

**Refactor 1:** the JSON schema across apply.md and the artifacts was inconsistent; the artifacts could not consume what apply.md produced. Fix: defined a single canonical schema and rewrote apply.md to produce it.

**Refactor 2:** `format.md` was decorative and the renderer only read four trivial fields. Fix: defined a machine-readable YAML spec block at the bottom of `format.md`; updated the renderer to parse it and apply every field; pushed hard-coded defaults to be fallbacks only.

**Refactor 3:** voice rules were unenforced; format choices were template-filled rather than co-created; cover letter structure was hidden inside apply.md prose. Fix: rewrote onboarding for all three tiers to extract real voice signals, co-create `format.md` from conversation, and produce a separate `cover-letter.md`. Added a voice tripwire to apply.md.

**Refactor 4 (packaging):** consolidated a dual-implementation history (a personal Python toolkit and a portable HTML toolkit) down to one schema and one rendering path. Removed the Python renderer and YAML profile structure. Anonymised example data. Added `ONBOARDING.md` for first-time users.

## Open questions worth revisiting

The remaining open questions are documented in `_audit/known-issues.md`. The notable ones:

- KI-7: cover letter layout for non-Anglo markets (German, French formal).
- KI-1 / KI-5: soft-target fields (`bullet_target_words`, `page_length`) could be enforced with post-render checks.
- KI-11: voice tripwire could extend to onboarding output, not just apply output.
