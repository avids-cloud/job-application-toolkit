# How to use this toolkit

A walkthrough from "I just downloaded the folder" to "I sent the application." If you have already done onboarding, skip to "Apply for a role."

## What this is, in one paragraph

A folder of plain text prompts you use with Claude. There is no app to install. There is no account to create. You do onboarding once to produce your `profile/` folder. Every job application after that is one short Claude conversation. The output is a CV and cover letter you print from your browser.

## Before you begin

You need access to a Claude conversation. Any of these work:

- **claude.ai in a browser** (free, paid, or pro). The simplest way.
- **The Claude desktop app** (Mac or Windows).
- **Claude Code** (the CLI). Most efficient because Claude can read and write your files directly.
- **Cowork mode** (Claude Code, the desktop app inside a project folder, or claude.ai with Cowork enabled). Same advantage as Claude Code: Claude can read and write your files.

If you are using claude.ai in a browser without Cowork, you upload files manually (paperclip icon). If you are using anything that has access to your file system (Cowork, Claude Code, or the desktop app inside a folder), Claude can open the files itself when you point it at them.

## A note on text editors

The toolkit uses plain text files: `.md` (Markdown) and `.json`. Some editors silently change the format and break things.

**Editors that work:**
- TextEdit on Mac, set to Plain Text Mode (Format > Make Plain Text) before saving.
- Notepad on Windows (the basic one, not WordPad).
- Any code editor: VS Code, Sublime Text, vim, Nano, Cursor, Zed.
- Claude Code or the Claude desktop app working in a folder. Claude can save files directly.

**Editors that will break the file. Do not use:**
- Microsoft Word. Saves as `.docx` even if you change the extension; the toolkit cannot read it.
- Google Docs. Same problem unless you "Download as plain text (.txt)" and rename the file.
- Apple Pages. Same.

If a file you saved is not working, open it in a different editor. If the contents look like the markdown you pasted, it is fine. If you see XML, hidden formatting marks, or anything that does not match what you pasted, it was saved in the wrong format.

## How to copy a code block from a Claude response

Throughout the workflow, Claude gives you fenced code blocks (text inside triple-backticks). To copy one:

1. Hover over the code block. A small "Copy" button appears in the top-right corner.
2. Click "Copy". The whole block (without the backticks) is now on your clipboard.
3. Paste it into the next Claude conversation, or into a text editor.

This is how you move the JSON between the apply prompt and the artifact prompts, and how you move file contents from the onboarding prompt into your text editor.

## Stage one: onboarding (do this once)

This takes about thirty minutes for an early-career person, twenty for a mid-career person, forty-five for an executive. It produces a `profile/` folder containing your career history and your rules.

### 1.1 Pick the right onboarding file

| You are | Use |
|---|---|
| A senior leader (VP, Director, Head of, C-suite) with board or executive exposure | `onboarding/onboard-executive.md` |
| Five to fifteen years into your field, leading work or small teams | `onboarding/onboard-midcareer.md` |
| In your first job, just out of study, or have done internships and projects | `onboarding/onboard-grad.md` |

If you are between two of them, pick the lower one. You can always rerun a higher one later when your situation changes.

### 1.2 Run the prompt

Start a new Claude conversation. Open the onboarding file in any text editor. Select all of it. Copy. Paste it into the Claude chat as your first message. Send.

Claude will start by asking which market (country) you are targeting. The answer changes the right defaults for everything that follows: page length, photo, personal details, page size, spelling. Pick the country where you are most likely to apply for jobs. If the answer is more than one, pick the primary one and run onboarding again later for the second.

After the market question, Claude works through eight or nine sections. Answer them naturally. If a question is thin, push back. If you do not know the answer, say so. Honest material beats perfect material.

### 1.3 Save the profile folder

When Claude is finished, it produces a series of files. Each one is labelled with its path inside `profile/`, like `profile/voice.md` or `profile/profile.md`.

To save them on your computer:

1. **Create a folder named `profile`** somewhere you will not lose it. Your home folder, Documents, or wherever you keep work files.
2. **For each file Claude produces:** open your text editor (TextEdit on Mac, Notepad on Windows, or any code editor like VS Code). Paste the contents Claude gave you. Save with the exact filename Claude said.
3. **On Mac specifically:** TextEdit defaults to rich text format. Switch it to plain text via Format > Make Plain Text before saving, otherwise it saves as `.rtf` instead of the format you want.
4. **On Windows specifically:** Notepad does not show file extensions by default. When saving, change "Save as type" to "All Files" and type the extension yourself (e.g. `voice.md`, not `voice.md.txt`).
5. **In Cowork mode or Claude Code:** Claude can save the files for you. Tell Claude where you want the `profile/` folder and Claude writes each file directly.

The structure when you are done:

```
profile/
  profile.md       full career history
  voice.md         tone rules, prohibitions
  format.md        CV layout, locked
  filter.md        what makes a role worth applying for
  cover-letter.md  cover letter rules
```

The `examples/example-profile/` folder shows what a finished profile looks like. Compare yours against it as a sanity check.

You are done with stage one. You only do this again if your situation changes (new country, big career pivot) or you want to rebuild from scratch.

## Stage two: apply for a role (do this for every application)

### 2.1 Start a new Claude conversation

Always start fresh. Do not reuse an old conversation. Each application is independent.

### 2.2 Paste the apply prompt and attach your profile

Open `apply/apply.md` in your text editor. Select all. Copy. Paste it into the Claude chat as your first message.

Then attach your `profile/` folder. The way you do this depends on your Claude environment:

- **Cowork mode or Claude Code:** Tell Claude where the folder is. For example: "My profile folder is at `/Users/janesmith/Documents/profile`." Claude reads the files itself.
- **Desktop app inside a folder:** Same as Cowork. Point Claude at the folder.
- **claude.ai browser without Cowork:** Click the paperclip icon. Attach every file in `profile/`. There may be an upload limit; if so, attach `profile.md`, `voice.md`, `format.md`, `filter.md`, and `cover-letter.md` first; you will not need anything else.
- **Drag and drop into the chat:** Works in most desktop environments.

Send.

### 2.3 Paste the job description

In your next message, paste the job description. Any format is fine. Copy from the company's careers page, LinkedIn, an email, or a PDF.

### 2.4 Read the fit assessment

Claude produces a fit assessment. It tells you:

- Overall fit: Strong, Reasonable, Weak, or Do not apply.
- Match against your filter.
- The 3-5 strongest pieces of evidence from your profile that line up with the role.
- The 2-4 real gaps.
- A recommendation.

Read it carefully. If it says "do not apply" because of a hard dealbreaker, listen to that. The dealbreakers are what you wrote into your `filter.md`; if Claude says one is present, your past self decided this was a hard line.

If you decide to proceed, tell Claude "yes, proceed."

### 2.5 Read the voice check

After Claude generates the prose for the headline, summary, and cover letter, it scans its own output against the prohibition lists in your `voice.md` and `cover-letter.md`. If it finds anything, it surfaces the violations to you with suggested fixes.

For each flagged phrase, you can:

- Apply Claude's suggested fix.
- Tell Claude to leave it (your past self may have been too strict for this context).
- Stop and rewrite yourself.

Claude reruns the scan after applying changes. The check repeats until clean (or you accept the remaining flags).

### 2.6 Get the application JSON

Once the voice check is clean, Claude produces one JSON object inside a fenced code block. This is what feeds the artifacts. Copy it. Keep it in your conversation or save it.

## Stage three: render the documents

### 3.1 Open the CV artifact in a new Claude conversation (or the same one)

Open `render/cv-artifact.md`. Paste it into Claude. In the same message, paste the JSON from stage two.

Claude produces a Claude artifact (browser-rendered HTML) of your CV. The artifact has a "Print or save as PDF" button.

### 3.2 Open the cover letter artifact

Same pattern. Paste `render/cover-letter-artifact.md`. Paste the same JSON. Claude produces the cover letter artifact.

### 3.3 Print to PDF

In the artifact, click the "Print or save as PDF" button. Use your browser's print dialog to save as PDF. The artifact's CSS is set up for A4 (or Letter, whichever your `format.md` specifies) and removes the toolbar from the printed page.

## Stage four: review and send

Open the PDFs. Read them as if you were the hiring manager. Check:

1. Every claim is something you can defend in interview. If your `profile.md` is honest, your CV is honest. If a number looks wrong, the source is wrong, not the artifact.
2. The cover letter sounds like you. If a sentence does not, edit it directly in the artifact for this one application, and update `voice.md` if the same problem keeps appearing.
3. Numbers are correct.
4. Page count makes sense.

If you spot a structural problem (wrong section order, wrong layout, wrong dates format), the fix is upstream:

| What is wrong | Where to fix it |
|---|---|
| Voice is off in the cover letter | `profile/voice.md`. The phrase joins the prohibition list. |
| A whole role is missing or wrong | `profile/profile.md` (the role section) |
| An achievement is wrong | `profile/profile.md` (the role's Achievements bullets) |
| Wrong section order or wrong layout | `profile/format.md` (the machine-readable spec block at the bottom) |
| Wrong cover letter structure | `profile/cover-letter.md` |
| You keep applying for jobs that do not fit | `profile/filter.md`, especially the dealbreakers |
| You hate a phrase that keeps appearing | Add it to "Phrases I never use" in `voice.md`. The voice tripwire catches it next time. |

After fixing the upstream file, rerun the apply prompt. The fix propagates.

Send.

## A typical run, after the first one

1. New Claude conversation. Paste `apply/apply.md`. Attach your `profile/` folder. Paste the JD. (1 minute)
2. Read fit assessment. Decide. Tell Claude to proceed. (30 seconds)
3. Read voice check. Approve or fix any flagged phrases. (30 seconds, mostly empty)
4. Wait for the JSON. (30-60 seconds)
5. New Claude conversation (or the same one). Paste `render/cv-artifact.md`. Paste the JSON. (30 seconds)
6. Same for `render/cover-letter-artifact.md`. (30 seconds)
7. Print both artifacts to PDF. (1 minute)
8. Send. (1 minute)

Total: about ten minutes per application. Most of which is you reading and deciding.
