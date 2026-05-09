# Contributing

Pull requests welcome. The toolkit is built on a few load-bearing principles. If your contribution conflicts with one of them, the PR will not be accepted. The principles are short. Read them before you write.

## Design principles

### 1. Co-create, never prescribe

Onboarding is a conversation that produces files in the user's own terms. It is not a form, a wizard, or a fill-in-the-blanks template.

If you find yourself adding a prompt that says "Choose A, B, or C" without asking the user why, you are prescribing. Rewrite as an open question that captures the user's reasoning.

### 2. Select, do not regenerate

The candidate's career history lives in `profile.md`. The apply prompt selects what to include for a given job and writes a tailored headline, summary, and cover letter. It does not regenerate the career history.

If you find yourself rewriting bullets or restating numbers in apply.md, you are regenerating. The fix is to update `profile.md`, not to override.

There are two carve-outs: the headline and the summary paragraph are regenerated per application because their job is to set the frame for this specific role. Cover letter prose is also written fresh per application because it is genuinely about the application. Everything else is selection.

### 3. Claude decides content, code decides format

Claude is responsible for what goes in the document: which roles, which achievements, which competencies, what the headline says. Claude is not responsible for layout: section order, font, margins, role layout.

Layout lives in `format.md`'s machine-readable spec block. The artifacts read that block and apply every value. A two-line CSS change in an artifact is fine; an artifact that interprets format rules is not.

### 4. Every prompt must be self-contained

A reader picking up `onboarding/onboard-grad.md` should be able to use it without having read `apply/apply.md` or anything else. Every prompt explains its inputs, its outputs, and its rules.

This rule is non-negotiable because users open one prompt at a time. If a prompt assumes context from another file, users get lost.

### 5. No runtime dependencies

The toolkit must work end-to-end with only:

- A Claude conversation
- A web browser

Nothing else. No `pip install`. No terminal. No locally executed scripts. The artifacts produce PDFs through the browser's print dialog.

### 6. Adaptable by default

Country, industry, and seniority are variables, not assumptions.

Specifically:

- **Country:** the onboarding prompt asks the user's target market in section 0 and adapts every later question. Page length, photo, personal details, profile summary, page size, spelling, date format are all market-dependent.
- **Industry:** the prompts ask about the user's function and scale markers in their industry's terms. They do not assume tech, finance, consulting, or any other sector.
- **Seniority:** the three onboarding tiers cover early-career, mid-career, and executive. Within each tier, the conversation works for the full range. The exec prompt works equally for a first-time VP and a thirty-year CTO.

If your contribution adds an assumption that breaks one of these dimensions (e.g. "this market expects A" without asking which market), it will not be accepted.

## How to add an onboarding tier

If you think there is a gap in the existing three (career returner, founder, academic-to-industry, public-sector-to-private), add `onboarding/onboard-<tier>.md`.

Structure:

1. Section 0: Market and conventions. Ask which country / region. Hold the right defaults in mind for the rest of the conversation.
2. Sections 1-5: Career-specific questions for this tier. Different from the existing three, because this is what justifies the new file.
3. Section 6: Voice. Same shape as the existing onboarding files.
4. Section 7: Format. Same shape, with sensible defaults for this tier.
5. Section 8: Filter. Same shape.
6. Section 9: Cover letter. Same shape.
7. Section 10: Output. Produce `profile.md`, `voice.md`, `format.md`, `filter.md`, `cover-letter.md`.

Match the prose tone of the existing files: smart friend asking good questions, not a form.

## How to add a new market

Open the existing onboarding files. Find section 0. Add or sharpen the guidance for the new market. Specifically:

- Page length norms.
- Photo expectations.
- Personal details expected or off-limits.
- Profile summary norms.
- Page size (A4 vs Letter).
- Spelling convention.
- Date format.
- Any signal hiring managers in this market look for that is unusual elsewhere.

Be specific. "Asia-Pacific" is not a market. "Singapore for senior tech roles" is.

## How to add a new output format

The existing artifacts produce browser-rendered HTML for print-to-PDF. To add LaTeX, plain text, Markdown, or any other format:

1. Add a new file under `render/`. Name it for the format: `render/cv-latex.md`, `render/cv-plaintext.md`.
2. The new file is a Claude prompt that takes the same JSON shape as the existing artifacts and produces the new format.
3. The format spec from `format.md` should be honoured: if the user's spec says A4, the LaTeX class should be A4. If allcaps headings, the LaTeX style should reflect that.
4. Document the new format in `render/`'s README (or in the file itself if no README exists).

The JSON schema that all output prompts consume is locked. Do not change it without a corresponding update to `apply/apply.md` and the schema_version field.

## How to add a new language

Translate an onboarding file end to end. Save as `onboarding/onboard-<tier>-<lang>.md` (e.g. `onboarding/onboard-grad-de.md`). Translate the apply prompt similarly.

Adapt the example phrases in the voice section to phrases that make sense in the target language. The point of the voice test is to give the user a concrete choice; the choice has to feel real in their language.

Keep the file structure of the original: same sections, same order, same headings. Translators are welcome to localise the section names but the sequence must match the English version.

## How to file an issue

If you find a bug or have a suggestion that is not a code change, open an issue. Include:

- What you tried.
- What you expected.
- What happened.
- Which Claude environment (claude.ai, desktop app, Cowork, Claude Code).
- Which onboarding tier you used.
- What is in your `profile/format.md`'s machine-readable spec block (the YAML at the bottom). Redact any PII.

## Soft-target fields in format.md

A few fields in `format.md`'s machine-readable spec are documentation rather than hard rules. The artifacts do not enforce them. They exist so the user (and the apply prompt) have a stated target to aim at.

- `bullet_target_words`: a soft target for the average bullet's word count. The apply prompt may flag bullets that are far from the target, but neither the prompt nor the artifact will rewrite to hit it.
- `older_roles_treatment`: documents intent. To actually omit older roles, remove `early_career` from `section_order`. To condense them, leave `early_career` in.
- `date_format`: the format the user has chosen for dates in their profile.md. The user is responsible for writing dates in this format throughout profile.md. The artifact renders dates verbatim and does not parse them.

If you find one of these fields is being treated as a hard rule somewhere in the toolkit, that is a bug. File an issue.

## What we will not accept

- Changes that introduce a Python, Node, or any other runtime dependency.
- Changes that move format decisions out of `format.md` into prompt logic.
- Changes that assume a default country, industry, or seniority.
- Changes that make a prompt depend on context from another file (every prompt must be self-contained).
- Voice or tone changes that contradict the user's voice file. Voice files are the source of truth for tone.
- Adding a "personality" or AI-sounding sentence pattern to any prompt. Plain language, short sentences, no em dashes.
