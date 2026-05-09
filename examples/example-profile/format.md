# CV format: Maya Chen

## Why this format

I target Chief Operating Officer and VP Operations roles in B2B SaaS, fintech, and marketplace companies in London, Singapore, and major European cities. Hiring managers in these markets expect a structured, scannable CV that signals scale (revenue, headcount, geography) within the first half-page, then walks through transformation evidence by role. Three pages is the right length for a senior-leader CV in the UK and Europe; the alternative is dropping evidence I can defend, which weakens the application.

## Length and reasoning

Three pages, A4. Two pages is too tight to show the IPO work and the international expansion evidence both at FieldGrid. Four would dilute. The Stratton Hayes role gets condensed treatment because what matters is that I came from consulting, not the specifics of every engagement.

## Sections that matter most

In order of what would survive a forced cut:
1. Profile (sets the frame)
2. Professional experience (the evidence)
3. Key competencies (the lens hiring managers use to scan)
4. Awards (the OMX 40 Under 40 is a signal)
5. Education (the INSEAD MBA matters in this market)
6. Volunteering (signals board readiness)
7. Early career (one line is enough)

If forced to cut: volunteering goes first, then early career.

## Profile paragraph

Yes. Five sentences leading with the function and the strongest scale marker (cost-to-serve down 47 percent at FieldGrid, IPO readiness, international expansion across seven markets).

## Role layout

Split into Responsibilities and Achievements. Italic company bio underneath the role heading. One-line "Context:" above the bullets where the situation needed framing.

## Older roles

Most recent three roles get full treatment. Anything older than that condenses into a single one-line entry under Early career.

## Things to keep doing

- Title and company on one line, dates right-aligned. Recruiters scan this column.
- Bold the outcome at the start of achievement bullets. The number jumps out.
- Italic company bio. Tells the reader the size of the business in one line, separates company context from role context.
- Specific numbers throughout. "Approximately 47 percent" not "almost half."

## Things to never do

- Photo. UK convention.
- Hobbies section.
- "References available on request" line.
- Personal details (date of birth, marital status, nationality).
- Two columns. Breaks ATS parsing.
- Decorative elements, sidebars, or icons.

## Machine-readable spec

```yaml
schema_version: "1.0.0"
page_size: A4
page_length: 3
section_order:
  - profile
  - competencies
  - roles
  - early_career
  - education
  - awards
  - volunteering
section_titles:
  profile: "Profile"
  competencies: "Key competencies and recent achievements"
  roles: "Professional experience"
  early_career: "Early career"
  education: "Education and executive development"
  awards: "Awards and recognition"
  volunteering: "Volunteering and community"
  sectors: "Sector experience"
  platforms: "Platforms and frameworks"
  languages: "Languages"
include_photo: false
include_personal_details: false
headings_style: allcaps
bullet_style: solid
bullet_target_words: 60
role_layout: split
include_company_bio: true
include_role_context: true
bold_lead_ins: true
date_format: "MMM YYYY"
font: Arial
font_size_body: 10.5
font_size_name: 24
colour_hex: "1B3A5C"
margin_top_inches: 0.85
margin_bottom_inches: 0.85
margin_left_inches: 0.85
margin_right_inches: 0.85
older_roles_treatment: condensed
```
