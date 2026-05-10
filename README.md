# Job Application Toolkit

A folder of Claude prompts that helps you build a CV and cover letter tailored to a specific role, without the output drifting between runs or your career data leaving your machine. Built with NZ tech and operations professionals in mind.

## Why this exists

I mentor a lot of students and early-career people. The NZ market right now is tight, and the same people who would have walked into a job two years ago are sending out twenty applications and hearing nothing back. A lot of that comes down to applications that are generic, applications that miss the obvious mismatch with the role, or applications that sound like everyone else's. This toolkit is what I use to help them apply more carefully. I am putting it on GitHub because there are far more people in that position than I can mentor one to one.

It is not a CV writer. It is a structured way to keep your career history in one place, decide whether a role is worth applying for, and produce tailored documents that match how you actually talk.

## What's in here

```
job-application-toolkit/
├── ONBOARDING.md                  step-by-step first-run walkthrough
├── USAGE.md                       full reference walkthrough
├── DESIGN.md                      why each piece exists
├── CONTRIBUTING.md                rules for PRs
├── onboarding/
│   ├── onboard-executive.md       interview for VP / Director / C-suite
│   ├── onboard-midcareer.md       interview for 5-15 years in
│   └── onboard-grad.md            interview for 0-5 years
├── apply/
│   ├── apply.md                   per-role selection prompt with voice scan
│   └── fit-assessment.md          standalone fit check
├── render/
│   ├── cv-artifact.md             HTML CV renderer for print-to-PDF
│   └── cover-letter-artifact.md   HTML cover letter renderer
└── examples/
    ├── example-profile/           anonymised profile (Maya Chen, COO)
    └── example-selection.json     sample apply output
```

## Quick start

You need [Claude Code](https://docs.claude.com/en/docs/claude-code) installed plus a browser for the render step.

```
git clone https://github.com/avids-cloud/job-application-toolkit
cd job-application-toolkit
claude
```

In Claude Code, run the onboarding interview that matches your seniority:

```
Read onboarding/onboard-midcareer.md and run it as a conversation with me, starting from the file's first instruction.
```

Replace `midcareer` with `executive` or `grad` if either fits better. Claude will interview you for 30 to 45 minutes about your career, voice, format, role filter, and cover letter rules. At the end it produces five files. Save them into a folder named `profile/` in the repo.

To apply for a job, start a new Claude Code conversation in the same folder:

```
Read apply/apply.md. Use my profile/ folder. Here is the job description:

[paste the JD]
```

Claude returns a fit assessment. If it says do not apply, stop. Otherwise tell it to proceed. It produces one JSON object inside a fenced code block. Copy it.

To render: open https://claude.ai, paste the contents of `render/cv-artifact.md` plus the JSON in one message. A CV artifact opens in the side panel. Click the print icon, save as PDF. Repeat with `render/cover-letter-artifact.md`.

`ONBOARDING.md` walks the same steps with a verification check at each stage.

## What it's good at

- Senior tech and operations roles. The example profile is a COO; the executive and mid-career flows are tuned for that kind of work.
- Keeping your numbers and your wording stable across applications. Claude selects from your master profile rather than regenerating your career every run.
- Catching obvious misfits before you spend an evening tailoring a CV. The fit assessment is honest by design, not encouraging.
- Letting you write in your own voice. The voice tripwire scans generated prose against your prohibition list before the JSON is produced.

## What it isn't

- A magic application generator. If the underlying fit between you and the role is weak, no amount of tailoring fixes that. The toolkit will tell you so.
- A replacement for thinking. You read the output, decide whether you can defend every claim in interview, and edit when something is off.
- Hands-off. You maintain `profile.md` by hand. New role, new achievement, new competency: edit the file. The toolkit does not write back to it.
- Polished for non-Anglo cover letter conventions. German formal recipient blocks and a few similar layouts are not yet supported. Page length and bullet length are soft targets, not enforced. See `_audit/known-issues.md` for the full list.
- Most useful for people with a few roles to choose between. The grad onboarding flow exists, but the toolkit reaches its full value when there is real career history to select from.

## Tailoring it to your own career

The repo ships with `examples/example-profile/`, an anonymised profile for a COO called Maya Chen. Look at it to see the shape of a finished profile. Do not edit it.

Your own data lives in a `profile/` folder you create. The onboarding interview produces it. After that, treat `profile.md` as a living document. New role: add a `### Role:` block. New achievement: add a bullet under the right role. The IDs in square brackets (e.g. `[id: r1]`) are stable references the apply prompt uses; do not rename them once set.

If you want a different default market, the onboarding asks for one in section 0. Conventions for page length, photo, personal details, and date format adapt accordingly. NZ, Australia, UK, US, Germany, France, Singapore, Netherlands, and India are covered to some degree out of the box.

If the onboarding interview does not quite fit your function or industry, change it. Open `onboarding/onboard-<tier>.md` in Claude Code and ask Claude to adapt the file for your role and vertical (research scientist in pharma, product designer in fintech, developer relations in open-source, whatever you do). The structure of the interview stays the same; the questions get sharper for your context. Same goes for `apply/apply.md` if your industry has signals worth checking for.

## Built on Claude

This toolkit is designed for [Claude](https://claude.ai) specifically and works best inside [Claude Code](https://docs.claude.com/en/docs/claude-code), where Claude can read and write your `profile/` files directly. The render step uses claude.ai because Claude artifacts only exist in the browser. Other LLMs may run the prompts, but no other model has been tested.

## Contributing

PRs welcome. Particularly useful contributions:

- Support for additional markets. Everywhere not in the list above is open. See "How to add a new market" in `CONTRIBUTING.md`.
- Onboarding tiers for situations the existing three do not cover well: career returner, founder, academic-to-industry, public-sector-to-private.
- Additional output formats (LaTeX, plain text, anything that consumes the same JSON).
- Translations.

Read `CONTRIBUTING.md` first. The toolkit has load-bearing principles (co-create not prescribe, select not regenerate, voice as constraints, no runtime dependencies) that any contribution has to respect.

## License

[MIT](LICENSE).
