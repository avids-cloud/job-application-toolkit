# Cover Letter Artifact

You are rendering a cover letter as a Claude artifact from a JSON object produced by `apply/apply.md`.

This prompt is designed to produce the same output every time, given the same input. Every visual decision is locked in the HTML and JavaScript template below, parameterised by the `format` block in the JSON. Your job has exactly one step: take the JSON the user pastes in and substitute it into a single placeholder.

You do not write any JavaScript. You do not modify the CSS. You do not change any HTML other than that one substitution.

## What the user will give you

The user will paste the same JSON object that `apply/apply.md` produced for the CV artifact. The cover letter renderer reads the `format`, `cover_letter`, and `header` blocks. Any other keys are ignored.

If the user has not pasted JSON, ask them for it. If the JSON is malformed, stop and ask them to re-run `apply/apply.md`. Do not attempt to fix the JSON yourself.

## What you produce

Create a single Claude artifact of type `text/html`. The artifact's content is the file below, with one and only one substitution: replace `__APPLICATION_JSON__` with the JSON the user pasted.

You must not edit anything else in this file.

## The artifact file

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<title>Cover letter</title>
<style>
  :root {
    --ink: #1a1a1a;
    --muted: #555555;
    --rule: #d6d6d6;
    --page-width: 210mm;
    --page-padding: 22mm;
    --font: Arial, "Helvetica Neue", Helvetica, sans-serif;
    --body-size: 11pt;
  }
  * { box-sizing: border-box; }
  html, body {
    margin: 0;
    padding: 0;
    background: #f0f0f0;
    font-family: var(--font);
    color: var(--ink);
    font-size: var(--body-size);
    line-height: 1.55;
  }
  .toolbar {
    position: sticky;
    top: 0;
    background: #ffffff;
    border-bottom: 1px solid var(--rule);
    padding: 10px 16px;
    display: flex;
    gap: 8px;
    z-index: 10;
  }
  .toolbar button {
    font: inherit;
    padding: 8px 14px;
    border: 1px solid var(--ink);
    background: var(--ink);
    color: #ffffff;
    cursor: pointer;
  }
  .toolbar button.secondary {
    background: #ffffff;
    color: var(--ink);
  }
  .page {
    width: var(--page-width);
    min-height: 297mm;
    margin: 16px auto;
    padding: var(--page-padding);
    background: #ffffff;
    box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  }
  body.letter .page { width: 216mm; min-height: 279mm; }
  .applicant-block { margin: 0 0 18px 0; }
  .applicant-name { font-weight: 700; font-size: 12pt; margin: 0 0 2px 0; }
  .applicant-line { margin: 0; font-size: 10.5pt; color: var(--muted); }
  .date-line { margin: 18px 0 18px 0; }
  .recipient-block { margin: 0 0 18px 0; }
  .recipient-line { margin: 0; }
  .salutation { margin: 18px 0 14px 0; }
  p.body { margin: 0 0 12px 0; text-align: left; }
  .signoff { margin: 22px 0 4px 0; }
  .signature-name { font-weight: 700; }
  @media print {
    body { background: #ffffff; }
    .toolbar { display: none; }
    .page {
      width: auto;
      min-height: auto;
      margin: 0;
      padding: 0;
      box-shadow: none;
    }
    @page { size: A4; margin: 22mm; }
    body.letter @page { size: Letter; margin: 22mm; }
  }
</style>
</head>
<body>

<div class="toolbar">
  <button onclick="window.print()">Print or save as PDF</button>
  <button class="secondary" onclick="copyText()">Copy as plain text</button>
</div>

<div id="root" class="page"></div>

<script>
  const DATA = __APPLICATION_JSON__;

  function el(tag, attrs, children) {
    const node = document.createElement(tag);
    if (attrs) {
      for (const k in attrs) {
        if (k === "class") node.className = attrs[k];
        else node.setAttribute(k, attrs[k]);
      }
    }
    if (children) {
      (Array.isArray(children) ? children : [children]).forEach(function (c) {
        if (c == null || c === false) return;
        if (typeof c === "string") node.appendChild(document.createTextNode(c));
        else node.appendChild(c);
      });
    }
    return node;
  }

  function applyFormat(fmt) {
    const root = document.documentElement.style;
    if (fmt.font) root.setProperty("--font", fmt.font + ", Arial, sans-serif");
    if (String(fmt.page_size).toLowerCase() === "letter") document.body.classList.add("letter");
  }

  function render(d) {
    applyFormat(d.format || {});

    const cover = d.cover_letter || {};
    const a = cover.applicant || {};
    const r = cover.recipient || {};
    const root = document.getElementById("root");
    root.innerHTML = "";

    // Applicant block
    const ab = el("div", { class: "applicant-block" });
    if (a.name) ab.appendChild(el("p", { class: "applicant-name" }, a.name));
    if (a.location) ab.appendChild(el("p", { class: "applicant-line" }, a.location));
    if (a.phone) ab.appendChild(el("p", { class: "applicant-line" }, a.phone));
    if (a.email) ab.appendChild(el("p", { class: "applicant-line" }, a.email));
    root.appendChild(ab);

    // Date
    if (a.date) root.appendChild(el("p", { class: "date-line" }, a.date));

    // Recipient block
    const rb = el("div", { class: "recipient-block" });
    if (r.hiring_manager) rb.appendChild(el("p", { class: "recipient-line" }, r.hiring_manager));
    if (r.company) rb.appendChild(el("p", { class: "recipient-line" }, r.company));
    if (r.address) rb.appendChild(el("p", { class: "recipient-line" }, r.address));
    if (rb.children.length) root.appendChild(rb);

    // Body
    if (cover.salutation) root.appendChild(el("p", { class: "salutation" }, cover.salutation));
    if (cover.opening) root.appendChild(el("p", { class: "body" }, cover.opening));
    if (cover.career_fit) root.appendChild(el("p", { class: "body" }, cover.career_fit));
    if (Array.isArray(cover.evidence)) {
      cover.evidence.forEach(function (ev) {
        if (ev) root.appendChild(el("p", { class: "body" }, ev));
      });
    }
    if (cover.closing) root.appendChild(el("p", { class: "body" }, cover.closing));
    if (cover.signoff) root.appendChild(el("p", { class: "signoff" }, cover.signoff));
    if (a.name) root.appendChild(el("p", { class: "signature-name" }, a.name));
  }

  function copyText() {
    const node = document.querySelector('.page');
    const t = node.innerText.trim();
    navigator.clipboard.writeText(t).then(
      function () { alert("Plain text copied. Paste into any application form field."); },
      function () { alert("Could not copy. Select the text manually."); }
    );
  }

  render(DATA);
</script>

</body>
</html>
```

## Your one job

Find this exact line in the file above:

```
  const DATA = __APPLICATION_JSON__;
```

Replace `__APPLICATION_JSON__` with the JSON object the user pasted. Do nothing else.

If the user's JSON is invalid, do not try to fix it. Stop and ask them to re-run `apply/apply.md`.

After publishing the artifact, give the user this note:

> If anything looks wrong, the fix is upstream. Edit `profile/voice.md` or `profile/cover-letter.md` and re-run `apply/apply.md` to regenerate the JSON. Do not edit the artifact directly.

## Voice safeguards

The voice rules are in `voice.md` and `cover-letter.md` and were applied by `apply.md` when it produced the JSON. Your job here is to not undo that work.

1. Render the JSON's text exactly as written. Do not adjust phrasing for "professionalism" or "polish."
2. Do not add greetings, sign-offs, postscripts, or filler that is not in the JSON.
3. Do not split a paragraph into bullets. Cover letters are prose.
4. Do not add headings or labels above the four prose sections. They flow as continuous letter copy.

## What you do not do

1. Do not change wording in the JSON.
2. Do not change CSS values in the template. Format-driven values are applied via CSS variables.
3. Do not add a company logo, accent colour, or decorative element.
4. Do not infer a recipient address from a website.
5. Do not change the date format. The date renders exactly as it appears in the JSON.
6. Do not change the print page size; the JavaScript reads `format.page_size`.
7. Do not add commentary or summary inside the artifact.
