# CV Artifact

You are rendering a CV as a Claude artifact from a JSON object produced by `apply/apply.md`.

This prompt is designed to produce the same output every time, given the same input. Do not use your own judgement on layout, fonts, colours, spacing, or section structure. Every visual decision is locked in the HTML and JavaScript template below, parameterised by the `format` block in the JSON. Your job has exactly one step: take the JSON the user pastes in and substitute it into a single placeholder.

You do not write any JavaScript. You do not modify the CSS. You do not change any HTML other than that one substitution.

## What the user will give you

The user pastes a JSON object that follows the schema produced by `apply/apply.md`. Top-level keys: `schema_version`, `format`, `header`, `tailored_summary`, `competencies`, `roles`, `early_career`, `education`, `awards`, `volunteering`, `sectors`, `platforms`, `cover_letter`, `voice_check`.

If the user has not pasted JSON, ask them for it. If the JSON is malformed, stop and ask them to re-run `apply/apply.md`. Do not attempt to fix the JSON yourself.

## What you produce

Create a single Claude artifact of type `text/html`. The artifact's content is the file below, with one and only one substitution: replace `__APPLICATION_JSON__` with the JSON the user pasted.

You must not edit anything else in this file. The CSS, the JavaScript, the HTML structure are all final.

## The artifact file

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<title>CV</title>
<style>
  :root {
    --accent: #1B3A5C;
    --ink: #1a1a1a;
    --muted: #555555;
    --rule: #d6d6d6;
    --page-width: 210mm;
    --page-height: 297mm;
    --page-padding-top: 22mm;
    --page-padding-right: 22mm;
    --page-padding-bottom: 22mm;
    --page-padding-left: 22mm;
    --font: Arial, "Helvetica Neue", Helvetica, sans-serif;
    --body-size: 10.5pt;
    --name-size: 24pt;
  }
  * { box-sizing: border-box; }
  html, body {
    margin: 0;
    padding: 0;
    background: #f0f0f0;
    font-family: var(--font);
    color: var(--ink);
    font-size: var(--body-size);
    line-height: 1.4;
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
  .status-banner {
    padding: 8px 16px;
    background: #fff7e6;
    border-bottom: 1px solid #f5cc88;
    color: #8a4b00;
    font-size: 12px;
  }
  .page {
    width: var(--page-width);
    min-height: var(--page-height);
    margin: 16px auto;
    padding-top: var(--page-padding-top);
    padding-right: var(--page-padding-right);
    padding-bottom: var(--page-padding-bottom);
    padding-left: var(--page-padding-left);
    background: #ffffff;
    box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  }
  body.letter { --page-width: 216mm; --page-height: 279mm; }
  header.cv-header { display: flex; justify-content: space-between; align-items: flex-start; gap: 18px; margin-bottom: 14px; }
  header.cv-header.no-photo { justify-content: center; text-align: center; }
  .header-text { flex: 1; }
  header.cv-header.no-photo .header-text { text-align: center; }
  h1.name {
    font-size: var(--name-size);
    font-weight: 700;
    color: var(--accent);
    margin: 0 0 4px 0;
    letter-spacing: 0.2px;
  }
  .headline {
    font-size: 11pt;
    font-weight: 600;
    margin: 0 0 6px 0;
  }
  .contact {
    font-size: 9.5pt;
    color: var(--muted);
    margin: 0 0 4px 0;
  }
  .contact a { color: var(--muted); text-decoration: none; }
  .personal-details {
    font-size: 9.5pt;
    color: var(--muted);
    margin: 0;
  }
  .photo {
    width: 30mm;
    height: 30mm;
    object-fit: cover;
    border: 1px solid var(--rule);
  }
  h2.section {
    font-size: 12pt;
    font-weight: 700;
    color: var(--accent);
    margin: 16px 0 8px 0;
  }
  body.headings-allcaps h2.section {
    text-transform: uppercase;
    letter-spacing: 0.6px;
  }
  body.headings-titlecase h2.section {
    text-transform: capitalize;
  }
  .profile-text { margin: 0 0 4px 0; }
  .competency { margin: 0 0 8px 0; }
  .competency .label { font-weight: 700; }
  .role { margin: 0 0 14px 0; page-break-inside: avoid; }
  .role-head {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    gap: 12px;
    margin-bottom: 2px;
  }
  .role-title-company { font-size: 11pt; }
  .role-title-company .title { font-weight: 700; }
  .role-title-company .company { font-weight: 700; color: var(--accent); }
  .role-dates { font-size: 10pt; white-space: nowrap; }
  .role-context-italic {
    font-size: 9.5pt;
    font-style: italic;
    color: var(--muted);
    margin: 2px 0 4px 0;
  }
  .role-challenge { margin: 4px 0; font-size: var(--body-size); }
  .role-challenge .label { font-weight: 700; }
  .sublabel {
    font-weight: 700;
    color: var(--accent);
    margin: 8px 0 4px 0;
    font-size: var(--body-size);
  }
  ul.bullets-solid { list-style: disc; padding-left: 18px; margin: 4px 0; }
  ul.bullets-dash { list-style: none; padding-left: 0; margin: 4px 0; }
  ul.bullets-dash li::before { content: "– "; }
  ul.bullets-hollow { list-style: circle; padding-left: 18px; margin: 4px 0; }
  ul.bullets-solid li, ul.bullets-dash li, ul.bullets-hollow li {
    margin: 0 0 4px 0;
  }
  ul.bullets-solid .lead-in,
  ul.bullets-dash .lead-in,
  ul.bullets-hollow .lead-in,
  ul.bullets-solid .outcome,
  ul.bullets-dash .outcome,
  ul.bullets-hollow .outcome { font-weight: 700; }
  body.no-bold-leadins ul .lead-in { font-weight: 400; }
  .row {
    display: flex;
    justify-content: space-between;
    gap: 12px;
    margin: 0 0 4px 0;
  }
  .award { margin: 0 0 4px 0; }
  .languages-list { margin: 0; padding: 0; list-style: none; }
  .languages-list li { display: inline; margin-right: 14px; }
  .languages-list .lang-name { font-weight: 700; }
  @media print {
    body { background: #ffffff; }
    .toolbar, .status-banner { display: none; }
    .page {
      width: auto;
      min-height: auto;
      margin: 0;
      padding: 0;
      box-shadow: none;
    }
    @page { size: A4; margin: 16mm; }
    body.letter @page { size: Letter; margin: 16mm; }
  }
</style>
</head>
<body>

<div class="toolbar">
  <button onclick="window.print()">Print or save as PDF</button>
  <button class="secondary" onclick="copyText()">Copy as plain text</button>
</div>

<div id="status-banner-slot"></div>
<div id="root" class="page"></div>

<script>
  const DATA = __APPLICATION_JSON__;

  // ---------- helpers ----------
  function el(tag, attrs, children) {
    const node = document.createElement(tag);
    if (attrs) {
      for (const k in attrs) {
        if (k === "class") node.className = attrs[k];
        else if (k === "html") node.innerHTML = attrs[k];
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
  function escapeHtml(s) {
    return String(s == null ? "" : s)
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");
  }
  function inchesToMm(inches) { return inches * 25.4; }

  // ---------- format application ----------
  function applyFormat(fmt) {
    const root = document.documentElement.style;
    if (fmt.colour_hex) root.setProperty("--accent", "#" + String(fmt.colour_hex).replace("#", ""));
    if (fmt.font) root.setProperty("--font", fmt.font + ", Arial, sans-serif");
    if (fmt.font_size_body) root.setProperty("--body-size", fmt.font_size_body + "pt");
    if (fmt.font_size_name) root.setProperty("--name-size", fmt.font_size_name + "pt");
    if (fmt.margin_top_inches != null) root.setProperty("--page-padding-top", inchesToMm(fmt.margin_top_inches) + "mm");
    if (fmt.margin_right_inches != null) root.setProperty("--page-padding-right", inchesToMm(fmt.margin_right_inches) + "mm");
    if (fmt.margin_bottom_inches != null) root.setProperty("--page-padding-bottom", inchesToMm(fmt.margin_bottom_inches) + "mm");
    if (fmt.margin_left_inches != null) root.setProperty("--page-padding-left", inchesToMm(fmt.margin_left_inches) + "mm");

    document.body.classList.remove("headings-allcaps", "headings-titlecase", "headings-sentencecase");
    if (fmt.headings_style) document.body.classList.add("headings-" + fmt.headings_style);
    if (fmt.bold_lead_ins === false) document.body.classList.add("no-bold-leadins");
    if (String(fmt.page_size).toLowerCase() === "letter") document.body.classList.add("letter");
  }

  function showStatusBanner(fmt) {
    const status = fmt && fmt.format_status;
    if (!status || status === "loaded") return;
    const slot = document.getElementById("status-banner-slot");
    const message = status === "defaults_used"
      ? "Your format.md spec block was missing. Defaults are being used."
      : "Your format.md spec block was incomplete. Defaults are filling in missing fields.";
    slot.appendChild(el("div", { class: "status-banner" }, message));
  }

  function sectionTitle(fmt, key, fallback) {
    const titles = (fmt && fmt.section_titles) || {};
    return titles[key] || fallback;
  }

  // ---------- section renderers ----------
  function bulletList(items, fmt, kind) {
    const klass = "bullets-" + (fmt.bullet_style || "solid");
    const ul = el("ul", { class: klass });
    items.forEach(function (b) {
      let html = "";
      if (kind === "responsibility") {
        if (b.lead_in) html += '<span class="lead-in">' + escapeHtml(b.lead_in) + '</span> ';
        if (b.rest) html += escapeHtml(b.rest);
      } else {
        if (b.outcome) html += '<span class="outcome">' + escapeHtml(b.outcome) + '</span> ';
        if (b.context) html += escapeHtml(b.context);
      }
      ul.appendChild(el("li", { html: html }));
    });
    return ul;
  }

  function renderProfile(d, fmt) {
    if (!d.tailored_summary) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "profile", "Profile")));
    section.appendChild(el("p", { class: "profile-text" }, d.tailored_summary));
    return section;
  }

  function renderCompetencies(d, fmt) {
    if (!Array.isArray(d.competencies) || !d.competencies.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "competencies", "Key competencies")));
    d.competencies.forEach(function (c) {
      const labelText = (c.label || "") + (c.scale_marker ? " (" + c.scale_marker + ")" : "");
      const html = '<span class="label">' + escapeHtml(labelText) + ':</span> ' + escapeHtml(c.evidence || "");
      section.appendChild(el("p", { class: "competency", html: html }));
    });
    return section;
  }

  function renderRoles(d, fmt) {
    if (!Array.isArray(d.roles) || !d.roles.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "roles", "Professional experience")));
    d.roles.forEach(function (r) {
      const block = el("div", { class: "role" });

      const head = el("div", { class: "role-head" });
      const left = el("span", { class: "role-title-company" });
      left.innerHTML =
        (r.title ? '<span class="title">' + escapeHtml(r.title) + '</span>' : '') +
        (r.title && r.company ? ' &nbsp;|&nbsp; ' : '') +
        (r.company ? '<span class="company">' + escapeHtml(r.company) + '</span>' : '');
      head.appendChild(left);
      head.appendChild(el("span", { class: "role-dates" }, r.dates || ""));
      block.appendChild(head);

      if (fmt.include_company_bio !== false && r.company_context) {
        block.appendChild(el("p", { class: "role-context-italic" }, r.company_context));
      }
      if (fmt.include_role_context !== false && r.challenge) {
        block.appendChild(el("p", {
          class: "role-challenge",
          html: '<span class="label">Context:</span> ' + escapeHtml(r.challenge)
        }));
      }

      const resps = Array.isArray(r.responsibilities) ? r.responsibilities : [];
      const achs = Array.isArray(r.achievements) ? r.achievements : [];

      if (fmt.role_layout === "merged") {
        const merged = resps.concat(achs.map(function (a) {
          return { lead_in: a.outcome, rest: a.context };
        }));
        if (merged.length) block.appendChild(bulletList(merged, fmt, "responsibility"));
      } else {
        if (resps.length) {
          block.appendChild(el("p", { class: "sublabel" }, "Responsibilities"));
          block.appendChild(bulletList(resps, fmt, "responsibility"));
        }
        if (achs.length) {
          block.appendChild(el("p", { class: "sublabel" }, "Achievements"));
          block.appendChild(bulletList(achs, fmt, "achievement"));
        }
      }

      section.appendChild(block);
    });
    return section;
  }

  function renderEarlyCareer(d, fmt) {
    if (!Array.isArray(d.early_career) || !d.early_career.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "early_career", "Early career")));
    d.early_career.forEach(function (e) {
      const html =
        '<span><strong>' + escapeHtml(e.title || "") + '</strong>' +
        (e.company ? ', ' + escapeHtml(e.company) : '') +
        '</span><span>' + escapeHtml(e.dates || "") + '</span>';
      section.appendChild(el("div", { class: "row", html: html }));
    });
    return section;
  }

  function renderEducation(d, fmt) {
    if (!Array.isArray(d.education) || !d.education.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "education", "Education")));
    d.education.forEach(function (ed) {
      const left = '<strong>' + escapeHtml(ed.qualification || "") + '</strong>' +
        (ed.institution ? ', ' + escapeHtml(ed.institution) : '') +
        (ed.notes ? '. ' + escapeHtml(ed.notes) : '');
      const html = '<span>' + left + '</span><span>' + escapeHtml(ed.dates || "") + '</span>';
      section.appendChild(el("div", { class: "row", html: html }));
    });
    return section;
  }

  function renderAwards(d, fmt) {
    if (!Array.isArray(d.awards) || !d.awards.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "awards", "Awards")));
    d.awards.forEach(function (a) {
      const html = '<strong>' + escapeHtml(a.name || "") + '</strong>' +
        (a.year ? ' (' + escapeHtml(String(a.year)) + ')' : '') +
        (a.context ? '. ' + escapeHtml(a.context) : '');
      section.appendChild(el("p", { class: "award", html: html }));
    });
    return section;
  }

  function renderVolunteering(d, fmt) {
    if (!Array.isArray(d.volunteering) || !d.volunteering.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "volunteering", "Volunteering")));
    d.volunteering.forEach(function (v) {
      const left = '<strong>' + escapeHtml(v.role || "") + '</strong>' +
        (v.organisation ? ', ' + escapeHtml(v.organisation) : '') +
        (v.note ? '. ' + escapeHtml(v.note) : '');
      const html = '<span>' + left + '</span><span>' + escapeHtml(v.dates || "") + '</span>';
      section.appendChild(el("div", { class: "row", html: html }));
    });
    return section;
  }

  function renderSectors(d, fmt) {
    if (!Array.isArray(d.sectors) || !d.sectors.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "sectors", "Sector experience")));
    section.appendChild(el("p", null, d.sectors.join(", ")));
    return section;
  }

  function renderPlatforms(d, fmt) {
    if (!Array.isArray(d.platforms) || !d.platforms.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "platforms", "Platforms and frameworks")));
    section.appendChild(el("p", null, d.platforms.join(", ")));
    return section;
  }

  function renderLanguages(d, fmt) {
    const langs = (d.header && d.header.languages) || [];
    if (!Array.isArray(langs) || !langs.length) return null;
    const section = el("section");
    section.appendChild(el("h2", { class: "section" }, sectionTitle(fmt, "languages", "Languages")));
    const ul = el("ul", { class: "languages-list" });
    langs.forEach(function (l) {
      const html = '<span class="lang-name">' + escapeHtml(l.language || "") + '</span>' +
        (l.level ? ' (' + escapeHtml(l.level) + ')' : '');
      ul.appendChild(el("li", { html: html }));
    });
    section.appendChild(ul);
    return section;
  }

  // ---------- main render ----------
  function render(d) {
    const fmt = d.format || {};
    applyFormat(fmt);
    showStatusBanner(fmt);

    const sectionOrder = (Array.isArray(fmt.section_order) && fmt.section_order.length)
      ? fmt.section_order
      : ["profile", "competencies", "roles", "early_career", "education", "awards", "volunteering"];

    const root = document.getElementById("root");
    root.innerHTML = "";

    // Header
    const h = d.header || {};
    const headerEl = el("header", { class: "cv-header" + (fmt.include_photo && h.photo_url ? "" : " no-photo") });
    const headerText = el("div", { class: "header-text" });
    headerText.appendChild(el("h1", { class: "name" }, h.name || ""));
    if (h.headline) headerText.appendChild(el("p", { class: "headline" }, h.headline));

    const contactBits = [];
    if (h.location) contactBits.push(escapeHtml(h.location));
    if (h.phone) contactBits.push(escapeHtml(h.phone));
    if (h.email) contactBits.push('<a href="mailto:' + escapeHtml(h.email) + '">' + escapeHtml(h.email) + '</a>');
    if (h.linkedin) contactBits.push('<a href="' + escapeHtml(h.linkedin) + '">' + escapeHtml(h.linkedin) + '</a>');
    if (Array.isArray(h.extra_links)) h.extra_links.forEach(function (l) {
      contactBits.push('<a href="' + escapeHtml(l) + '">' + escapeHtml(l) + '</a>');
    });
    headerText.appendChild(el("p", { class: "contact", html: contactBits.join(" | ") }));

    if (fmt.include_personal_details && h.personal_details) {
      const pd = h.personal_details;
      const pdBits = [];
      if (pd.date_of_birth) pdBits.push(escapeHtml(pd.date_of_birth));
      if (pd.place_of_birth) pdBits.push(escapeHtml(pd.place_of_birth));
      if (pd.nationality) pdBits.push(escapeHtml(pd.nationality));
      if (pd.marital_status) pdBits.push(escapeHtml(pd.marital_status));
      if (pdBits.length) headerText.appendChild(el("p", { class: "personal-details", html: pdBits.join(" | ") }));
    }

    headerEl.appendChild(headerText);
    if (fmt.include_photo && h.photo_url) {
      headerEl.appendChild(el("img", { class: "photo", src: h.photo_url, alt: "Photo" }));
    }
    root.appendChild(headerEl);

    // Sections
    const renderers = {
      profile: function (data) { return renderProfile(data, fmt); },
      competencies: function (data) { return renderCompetencies(data, fmt); },
      roles: function (data) { return renderRoles(data, fmt); },
      early_career: function (data) { return renderEarlyCareer(data, fmt); },
      education: function (data) { return renderEducation(data, fmt); },
      awards: function (data) { return renderAwards(data, fmt); },
      volunteering: function (data) { return renderVolunteering(data, fmt); },
      sectors: function (data) { return renderSectors(data, fmt); },
      platforms: function (data) { return renderPlatforms(data, fmt); },
      languages: function (data) { return renderLanguages(data, fmt); },
    };

    sectionOrder.forEach(function (s) {
      const fn = renderers[s];
      if (!fn) return;
      const node = fn(d);
      if (node) root.appendChild(node);
    });
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

Replace `__APPLICATION_JSON__` with the JSON object the user pasted. Do nothing else. Do not modify the CSS, the JavaScript, or any HTML.

If the user's JSON is invalid (will not parse), do not try to fix it. Stop and ask them to re-run `apply/apply.md`.

After publishing the artifact, give the user this note:

> If anything looks wrong in the output, the fix is upstream. Edit `profile/profile.md`, `profile/format.md`, or `profile/voice.md` and re-run `apply/apply.md` to regenerate the JSON. Do not edit the artifact directly.

## What you do not do

1. Do not change wording in the JSON. The text the artifact renders is the text the JSON contains.
2. Do not change CSS values in the template. The format spec controls layout via CSS variables; that is by design.
3. Do not add icons, dividers, sidebars, or any visual element not already in the template.
4. Do not introduce a second column.
5. Do not add a "References available on request" line.
6. Do not change the print page size; the JavaScript reads `format.page_size` and applies the right `@page` rule.
7. Do not add commentary, summary, or explanation to the artifact itself. The artifact is just the CV.
8. Do not split, join, or paraphrase any text from the JSON. Render it as-is.
