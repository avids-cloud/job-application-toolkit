# Onboarding

This walkthrough takes you from a clean clone to your first PDF in 13 steps. Active typing time is 20 to 30 minutes. The interview in step 5 adds another 30 to 45 minutes of conversation. You only do that once.

You need:

- Claude Code installed ([install instructions](https://docs.claude.com/en/docs/claude-code))
- A terminal
- A web browser (the render step uses claude.ai because the artifact pane only exists there)

## 1. Get the toolkit

If you cloned with git:

```
cd job-application-toolkit
```

If you downloaded the zip, unzip it and `cd` into the unzipped folder.

> ✓ Running `ls` in the folder lists `apply/`, `examples/`, `onboarding/`, `render/`.

## 2. Open Claude Code in this folder

```
claude
```

> ✓ You see the Claude Code interactive prompt (typically a `>` symbol). The folder name appears somewhere in the header.

## 3. Look at the example profile

Ask Claude:

```
Read examples/example-profile/profile.md and summarise its structure.
```

> ✓ Claude lists section headers like `## Identity`, `## Default headline`, `## Roles`, `## Competencies`. These are the sections your own profile will have at the end of step 6.

## 4. Pick the onboarding file that matches you

| You are | Use |
|---|---|
| VP, Director, Head of, C-suite, or board exposure | `onboarding/onboard-executive.md` |
| 5 to 15 years in your field, leading work or small teams | `onboarding/onboard-midcareer.md` |
| 0 to 5 years, including study and internships | `onboarding/onboard-grad.md` |

If you are between two, pick the lower one.

> ✓ You know which file to point Claude at in step 5.

## 5. Run the onboarding interview

In Claude Code, type:

```
Read onboarding/onboard-<tier>.md and run it as a conversation with me, starting from the file's first instruction.
```

(Replace `<tier>` with `executive`, `midcareer`, or `grad`.)

If Claude responds with a summary instead of starting the interview, paste this follow-up:

```
Now act as the prompt itself and ask me the first question.
```

Claude interviews you for 30 to 45 minutes about your market, career, voice, format, role filter, and cover letter.

> ✓ At the end, Claude shows you five files in the chat: `profile.md`, `voice.md`, `format.md`, `filter.md`, `cover-letter.md`. They are not yet on disk.

## 6. Save your profile to disk

Tell Claude:

```
Save those five files into a profile/ folder in this directory.
```

> ✓ `ls profile/` shows `profile.md`, `voice.md`, `format.md`, `filter.md`, `cover-letter.md`.

## 7. Find a real job ad

Pick a job you actually want to apply for. Copy the full job description text into your clipboard.

> ✓ The JD is on your clipboard.

## 8. Run the apply prompt

Open a new Claude Code conversation in the same folder. Type:

```
Read apply/apply.md. Use my profile/ folder. Here is the job description:

[paste the JD]
```

> ✓ Claude returns a fit assessment with one of three recommendations: "apply", "apply with caveats", or "do not apply".

## 9. Decide

If Claude says "do not apply", stop here. That is the point of the fit assessment.

Otherwise, reply:

```
Proceed.
```

> ✓ Claude generates the tailored headline, summary, and cover letter, runs the voice scan, then prints one JSON object inside a fenced code block.

## 10. Copy the JSON

Click the copy icon at the top right of the JSON code block. (If you do not see one, triple-click inside the block to select it, then copy.)

Keep this JSON somewhere safe (a scratch file, a sticky note app). You will paste it twice in the next two steps.

> ✓ The full JSON object is on your clipboard or saved.

## 11. Render the CV in claude.ai

Open https://claude.ai in your browser and start a new conversation. In your terminal, copy the contents of `render/cv-artifact.md` (e.g. `cat render/cv-artifact.md` then select-and-copy). Paste those contents into the claude.ai chat. In the same message, paste the JSON from step 10. Send.

> ✓ An artifact pane opens on the right of the claude.ai window with your rendered CV.

## 12. Render the cover letter

In the same claude.ai conversation, paste the contents of `render/cover-letter-artifact.md` plus the same JSON from step 10. Send.

> ✓ A second artifact pane opens with your cover letter.

## 13. Print to PDF

In each artifact pane, click the printer icon at the top of the artifact (or press `Cmd+P` on Mac, `Ctrl+P` on Windows or Linux). The browser print dialog opens. Choose "Save as PDF" as the destination. Save. Repeat for the cover letter.

> ✓ You have `cv.pdf` and `cover-letter.pdf` on your computer.

## You are done

Open the PDFs. Read them as if you were the hiring manager. If a number is wrong or a sentence does not sound like you, the fix is upstream: edit the relevant file in `profile/`, then re-run from step 8 for this application. Future applications use the new version automatically.

Every future application repeats steps 7 to 13. About 10 minutes per job, most of which is you reading and deciding.

For the full reference walkthrough including troubleshooting, see [USAGE.md](USAGE.md). For why each piece exists, see [DESIGN.md](DESIGN.md).
