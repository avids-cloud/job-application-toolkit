# Architecture Review

A direct answer to each of the six questions from Phase 2 of the brief. Findings inform the Priority 1-4 fixes that follow.

---

## 1. Is the three-layer separation intact?

**No. The separation is documented in DESIGN.md but is violated in two places.**

The three layers, as designed:
- **Layer 1 (source of truth):** `candidate-profile/` YAML files.
- **Layer 2 (selection and tailoring):** `apply.md` produces a JSON object naming what to include.
- **Layer 3 (assembly):** `render.py` reads layer 1 + layer 2 and produces a `.docx`.

**Violations:**

**Violation A: render.py does layer 2's job for section order.** When `selection.include_sections` is missing, render.py falls back to a hard-coded list. Fine. But when `format.md` says one section order and `selection.include_sections` says another, the JSON wins silently. The user expects format.md to be the source of truth for layout (it is called "format.md"); in practice it is not.

**Violation B: Layout rules live in render.py, not in format.md.** render.py hard-codes the role layout (TITLE | COMPANY | DATES, italic company bio, Responsibilities-then-Achievements). format.md describes this layout in prose, but render.py does not parse it; the prose is informational only. So layer 3 contains layout decisions that should be in layer 1 (or at minimum a parsable spec at the boundary).

**Violation C: apply.md regenerates the headline and summary.** This is documented as a deliberate exception (the "two pieces of fresh prose per application" carve-out). It is the right exception, but the boundary of what apply.md may regenerate is not enforced anywhere. apply.md could theoretically expand the carve-out and the system would still appear to work. There is no policy file that says "only headline and summary may be regenerated; everything else is selection."

**Violation D: cv-artifact.md and cover-letter-artifact.md sit outside the three-layer model entirely.** They each take a JSON shape that is neither the candidate-profile contents nor the new selection JSON. They are a fourth path that bypasses layers 1 and 2 and substitutes its own ad-hoc data shape.

---

## 2. Is the JSON schema consistent across apply.md, cv-artifact.md, and cover-letter-artifact.md?

**No. There are three different schemas. The defined schema below is the one that proceeds.**

What each file declares:

**apply.md produces:**
```
{
  "tailored": { "headline": str, "summary": str },
  "selection": {
    "competencies": [id...],
    "roles_detailed": [{ id, include_challenge, responsibility_ids[], achievement_ids[], achievement_overrides{} }],
    "roles_condensed": [id...],
    "include_sections": [section_key...]
  },
  "cover_letter": {
    "date": str,
    "recipient": { company, role, hiring_manager, address },
    "salutation": str,
    "opening": str,
    "career_fit": str,
    "evidence": [str, str?],
    "closing": str,
    "signoff": str
  }
}
```

**cv-artifact.md expects:**
```
{
  "header": { name, headline, location, phone, email, linkedin, extra_links },
  "profile": str,
  "competencies": [{ label, evidence }],
  "roles": [{ company, company_context, title, dates, location, challenge, responsibilities[], achievements[] }],
  "early_career": [...], "education": [...], "awards": [...], "volunteering": [...],
  "sectors": [str...], "platforms": [str...],
  "voice_meta": { person, spelling, date_format },
  "format_meta": { page_length, section_order, bullet_style, bold_lead_ins, headings, colour_hex }
}
```

**cover-letter-artifact.md expects:**
```
{
  "applicant": { name, location, phone, email, date },
  "recipient": { company, role, hiring_manager, address },
  "salutation": str,
  "opening": str,
  "career_fit": str,
  "evidence": [str, str?],
  "closing": str,
  "signoff": str,
  "voice_meta": { person, spelling }
}
```

These are three different shapes. The artifacts cannot consume what apply.md produces. The fallback path is broken.

### The single canonical schema (locked, nothing else proceeds until this is the standard)

The selection JSON produced by apply.md is the canonical shape. All renderers (Python, HTML artifact, future formats) must consume this shape, then resolve content from the candidate-profile folder. The artifacts must be updated to read this schema and pull the actual data from the candidate-profile folder (or, in a true Python-free path, from a flattened sidecar embedded in the JSON).

```jsonc
{
  "schema_version": "1.0.0",

  "tailored": {
    "headline": "string, one line",
    "summary": "string, 4-5 sentences"
  },

  "selection": {
    "competencies": ["competency_id", "..."],
    "roles_detailed": [
      {
        "id": "role_id",
        "include_challenge": true,
        "responsibility_ids": ["r1", "r2"],
        "achievement_ids": ["achievement_id", "..."],
        "achievement_overrides": {
          "achievement_id": { "outcome": "string", "context": "string" }
        }
      }
    ],
    "roles_condensed": ["early_career_id", "..."],
    "include_sections": [
      "profile", "competencies", "roles", "early_career",
      "education", "awards", "volunteering", "sectors", "platforms"
    ]
  },

  "cover_letter": {
    "date": "string in user format.md format",
    "recipient": {
      "company": "string",
      "role": "string",
      "hiring_manager": "string or empty",
      "address": "string or empty"
    },
    "salutation": "string",
    "opening": "string, one paragraph",
    "career_fit": "string, one paragraph",
    "evidence": ["string", "string (optional)"],
    "closing": "string, one short paragraph",
    "signoff": "string"
  },

  "voice_check": {
    "violations": [
      { "phrase": "string", "location": "string", "rule": "string" }
    ]
  }
}
```

The `voice_check` block is added to support the voice tripwire (Priority 2). It is filled by apply.md after generating the cover letter prose, by scanning against voice.md prohibitions. If empty, apply.md presents the JSON normally. If non-empty, apply.md surfaces the violations to the user before handing back the JSON.

For the artifact path (Priority 4 in the github-release): the artifact prompts must accept this schema. Either:
- The artifact resolves content from a sidecar `profile.json` produced once during onboarding, or
- The user pastes both the selection JSON and a flattened `profile.json` so the artifact has all data it needs.

**Decision required:** github-release must use the sidecar approach because Python-free and predictable. The existing system can keep render.py as the canonical assembler (it loads the YAML directly).

---

## 3. Where does the system rely on Claude's interpretation rather than explicit instruction or code?

Every instance, with file and intent:

| Where | What is interpretive | What it should be |
|---|---|---|
| `apply.md` Step 1 (fit assessment) | "If a hard dealbreaker from filter.md is present, your recommendation must be do not apply" — Claude reads filter.md prose and decides | filter.md should have machine-checkable dealbreaker fields and apply.md should match against them deterministically |
| `apply.md` Step 2 (selection) | "Pick 5-7 competencies most relevant to the JD" — Claude decides | This is genuinely interpretive and the right place for it. Acceptable. |
| `apply.md` Step 2 (overrides) | "Use an override only when the candidate has stronger evidence … reordering or rephrasing genuinely improves" — Claude decides what counts | Acceptable but should be logged: when an override is used, apply.md should explain why in its hand-off paragraph |
| `apply.md` voice rules | "Voice.md is binding. Hard rules in the voice file are constraints, not preferences" — Claude self-polices | Should run a tripwire scan against voice.md prohibition list and report violations in the JSON |
| `apply.md` "no facts not already in profile" | Claude judges whether a sentence introduces a new fact | This is genuinely hard to enforce mechanically. Acceptable as long as the user reviews the output. The audit should flag this risk to the user. |
| `apply.md` cover letter structure | The opening/career_fit/evidence/closing pattern is described in prose | The schema enforces the keys; what each paragraph contains is interpretive. Add explicit per-paragraph rules in apply.md to tighten the variance. |
| `cv-artifact.md` substitution | "Take the JSON the user pasted, and substitute it into a single placeholder" — Claude does string surgery | Should be a deterministic substitution with validation that the JSON parsed first |
| `cv-artifact.md` em-dash check (in cover-letter-artifact.md) | "If the JSON contains an em dash and the user's voice file forbade them … stop and tell the user" — Claude reads voice.md and decides | Should be a regex check in the substitution step, not a Claude interpretation |
| `render.py parse_format_rules` | Text-matches lower-cased substrings ("title case", "bold lead-ins\nno") | format.md should have a structured machine-readable header (YAML frontmatter or a parsable block); render.py reads that |
| Onboarding voice question | "Do these example phrases sound like you?" — Claude generates the voice.md from the answer | The conversation is interpretive (correct), but the output must include the actual phrases the user pointed at as positive/negative examples. Currently it summarises them and the signal is lost. |
| Onboarding format question | Claude maps user's answers into the fixed format.md template | Should generate format.md from the user's actual words, not by filling a fixed template |
| `apply.md` `include_sections` allow-list | "If the user's format.md describes a section that is not on the list, do not invent a key. Tell the user the toolkit does not yet support that section" | Should be a defined extensibility mechanism, not a refusal |

---

## 4. Does onboarding co-create the user's CV structure?

**No. It hands the user a fixed format.md template.**

All three onboarding files (executive, midcareer, grad) ask the user a series of yes/no questions about format preferences (page length, photo, bullet style, date format, etc.). They then generate a format.md file with a fixed shape: ten predetermined headings, in a predetermined order, regardless of the user's answers.

The user can change values inside the template (e.g. page length 4 → 3) but cannot change:
- The set of headings
- The structure of the file
- What rules are expressed
- What rules are unexpressed

For example, a designer who wants project tiles, a sidebar with skills, or a portfolio-style layout cannot describe that in format.md. The template has no place for it. Even if they describe it in prose, render.py cannot read it.

**This is a Phase 3 Priority 1 fix.** The onboarding must:
- Interview the user about format through open-ended conversation
- Reflect their answers back as enforceable rules in their own words
- Generate format.md as a locked specification produced from the conversation, not from a template
- Define a schema for format.md that render.py can parse, so the file is genuinely the source of truth

**Cover letter structure:** Onboarding currently never asks about cover letters. There is no cover-letter-format.md. The cover letter structure is fixed inside apply.md (opening, career_fit, evidence, closing). Phase 3 Priority 3 adds this as a co-created locked structure.

---

## 5. Does render.py read format rules from format.md?

**Almost no.**

The `parse_format_rules` function reads:
- `colour_hex`: regex `#[0-9a-f]{6}` over the file. Picks the first match.
- `headings_allcaps`: text match on "title case" (negative) and absence of "all caps" (positive).
- `bold_lead_ins`: text match on "bold lead-ins\nno" or "bold lead-ins: no".
- `page_length` and `section_order`: defaults; not parsed from the file.

Everything else in format.md (bullet style, bullet length, date format, tense, role layout, what to include, what to exclude) is decorative.

**The user is misled.** They write detailed rules in format.md, the rendered CV does not match them, and they have no way to know why.

**This is a Phase 3 Priority 1 fix.** Either:
- Give format.md a parsable schema (YAML frontmatter or structured block) and have render.py read every field, or
- Replace format.md prose with a `format.yaml` that has typed fields, and keep a small `format.md` notes file for the user-facing prose.

The first option preserves the "your data is plain markdown you can read and edit" promise. The fix should add a `## Machine-readable spec` block at the bottom of format.md with a fenced YAML code block, and have render.py parse that block. Hard-coded defaults remain as fallbacks if the spec block is missing or partial.

---

## 6. Are there files that duplicate each other or could be merged?

**Yes, in three places.**

**Duplication A: Three onboarding files.** `onboard-executive.md`, `onboard-midcareer.md`, `onboard-grad.md` are 80% the same. Sections 6 (Voice), 7 (Format), 8 (Filter) and the entire Output section are near-verbatim copies. Differences:
- Section 1-5 framing (arc/scale-markers vs delivery/progression vs education/projects)
- Sample sentences in the voice test
- Defaults in format (page length 4 vs 2 vs 1)

**Recommendation:** Do not merge. Three files are user-facing and each one is "the file you pick." Merging into one with branches makes the user pick a path inside the prompt, which is worse UX. But the duplicated content (voice section, format section, filter section, output section) should be sourced from a shared partial via a documented include pattern, or copy-edited together so drift does not happen.

For the github-release version, the three files should remain three but should literally share text where possible, and the duplicated parts should be flagged in CONTRIBUTING.md so contributors keep them in sync.

**Duplication B: cv-artifact.md and cover-letter-artifact.md substitution boilerplate.** Both files have a near-identical "substitution rules / what you must not do / handoff" structure. Could share a header about Claude's role in artifact rendering, but the templates themselves diverge intentionally.

**Not worth merging** — the duplication is mostly framing, not logic.

**Duplication C: README.md and USAGE.md overlap.** README has a "full workflow" numbered list; USAGE has the same workflow at length. The numbered list in README should compress to a one-paragraph "see USAGE.md" reference, removing the duplicated steps. Or USAGE.md folds into README and the file is removed.

**Recommendation:** Keep both, but compress README's workflow to a one-paragraph summary that links to USAGE.md. The duplication is a maintenance burden right now.

---

## Summary of architectural decisions for Phase 3

1. **Adopt the canonical schema in section 2** as the single shape produced by apply.md and consumed by render.py and (after a rewrite) the artifacts.
2. **Define a machine-readable block in format.md.** render.py reads every field. format.md is the actual source of truth for layout, with hard-coded defaults only as fallbacks.
3. **Onboarding co-creates format.md and voice.md from conversation, not from a template.**
4. **Voice tripwire in apply.md.** Regex/string-match scan after generation; surface violations in the JSON.
5. **Cover letter structure is co-created during onboarding.** Defaults exist but are negotiable.
6. **filter.md gets a small machine-readable dealbreaker section** so apply.md can match deterministically.
7. **Schema versioning.** The selection JSON gains a `schema_version` field so future changes are non-breaking.
8. **Compress README's workflow section to a link to USAGE.md** to remove duplication.
9. **HTML artifacts are deferred for the existing system** — they remain broken and are noted in known issues. The github-release version builds artifacts that consume the canonical schema cleanly, with a sidecar profile.json.

End of architecture review. Proceeding to Phase 3 fixes in priority order.
