# Job Application Toolkit

A folder of markdown prompts that turns Claude into your job application copilot. Onboard once. Apply for any job in 10 minutes. Your data stays on your machine.

## Who this is for

Job applicants who want a CV and cover letter that sound like them, look the same every time, and do not end up on a vendor's server. Works for any country, any industry, any seniority from grad to C-suite.

## Why this exists

Most CV tools are black boxes that embellish your career, give different output for the same input, and store your data on their servers. This toolkit is the smallest possible thing that fixes those problems: plain markdown prompts plus a profile folder you own. Claude is the runtime.

## Start here

Follow [ONBOARDING.md](ONBOARDING.md). Thirteen numbered steps from clean clone to first PDF. Active typing time is 20 to 30 minutes plus the one-time onboarding interview.

If you want the full reference walkthrough see [USAGE.md](USAGE.md). For why each piece exists see [DESIGN.md](DESIGN.md). To contribute see [CONTRIBUTING.md](CONTRIBUTING.md).

---

## What is in this repo

```
job-application-toolkit/
├── ONBOARDING.md               start here
├── README.md                   you are here
├── USAGE.md                    full reference walkthrough
├── DESIGN.md                   why every piece exists
├── CONTRIBUTING.md             rules for PRs
├── LICENSE                     MIT
├── onboarding/
│   ├── onboard-executive.md    senior leaders
│   ├── onboard-midcareer.md    5-15 years
│   └── onboard-grad.md         0-5 years
├── apply/
│   ├── apply.md                per-job selection prompt
│   └── fit-assessment.md       standalone fit check
├── render/
│   ├── cv-artifact.md          CV renderer (HTML)
│   └── cover-letter-artifact.md
└── examples/
    ├── example-profile/        anonymised profile (Maya Chen, COO)
    └── example-selection.json  example apply output
```

## How it works in two stages

**Stage 1: onboarding (one time, 30 to 45 minutes).** A guided conversation with Claude produces your `profile/` folder containing your career history (`profile.md`), tone rules (`voice.md`), CV layout (`format.md`), role filter (`filter.md`), and cover letter rules (`cover-letter.md`). Save the folder on your machine.

**Stage 2: applying (every job, about 10 minutes).** Paste `apply/apply.md`, your profile folder, and the job description into a Claude conversation. Claude tells you whether to apply. If yes, it produces a JSON object with the tailored content. Paste that JSON plus `render/cv-artifact.md` (and again with `render/cover-letter-artifact.md`) into claude.ai to get your rendered documents. Print to PDF.

Claude never regenerates your career history. It selects from facts that already exist in your profile. The renderer assembles the document. This is what makes the output consistent and your data stay yours.

## Adapting to your market

CV conventions vary. Page length, photo, personal details, profile summary, page size, spelling, date format depend on country. The onboarding asks your target market in section 0 and adapts every later question. See [USAGE.md](USAGE.md) for market specifics.

## Contributing

PRs welcome. Read [CONTRIBUTING.md](CONTRIBUTING.md) first. The toolkit has load-bearing principles (co-create not prescribe, select not regenerate, voice as constraints, no runtime dependencies) that any contribution must respect.

## License

[MIT](LICENSE). Use it, fork it, sell services on top of it. If you find a bug, file an issue.
