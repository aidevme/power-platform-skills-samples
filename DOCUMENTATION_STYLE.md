# Documentation Style Guide

This guide covers writing conventions for docs in this repo — sample project READMEs, setup guides, and reference pages for Power Platform skills built on the [power-platform-skills](https://github.com/microsoft/power-platform-skills) marketplace (canvas apps, code apps, MCP apps, mobile apps, model-driven apps, the Power Apps mobile extension, Power Automate, and Power Pages). It does not cover `presentations/` — those follow the speaker-notes format already established by `Canvas-App-Presentation-Notes.md` (numbered sections, **Talking points**, **Suggested time**).

## Required Page Structure

Docs live under `docs/power-platform-skills/<product-area>/`, one subfolder per product area, matching the folder names under `samples/` and the plugin names in the power-platform-skills marketplace: `canvas-apps`, `code-apps`, `mcp-apps`, `mobile-apps`, `model-apps`, `power-apps-mobile-extension`, `power-automate`, `power-pages`. Use PascalCase-hyphenated filenames that match the page's topic, e.g. `Canvas-Apps-Setup-Guide.md`, `Power-Pages-WebAPI-Integration.md`. A sample under `samples/<product-area>/` gets its own `README.md` inside the sample's folder rather than a page under `docs/`.

Every page should follow this structure, in order:

1. Page title (H1) — matches the filename topic. No YAML frontmatter.
1. Intro — 1-2 sentences (or a `## Overview` section) stating what the page covers. For guides written for a specific reader, add a bold **Audience:** line.
1. Table of contents — for long, multi-section guides only (anchor-linked list). Skip it on short reference pages.
1. Body sections (`##`, `###`) — numbered for sequential setup procedures (`## 1. Install the Plugin`), unnumbered for reference material.
1. Troubleshooting / Common Issues — one `### Issue: "<exact error text>"` entry per problem, using **Symptom:**, **Cause:**, **Solution:**/**Fix:** labels.
1. Additional Resources / Related links — Microsoft Learn links plus relative links to other docs, samples, or presentations in this repo.
1. Version footer — bolded `**Version:**` / `**Last Updated:**` / `**Maintained by:**` (or `**Author:**`) lines.

Not every page needs every section. A short reference page can be just a title, one sentence, and a table. A stub page can be only a title until content is written — don't invent sections it doesn't need yet.

## Headings

- Use `##` for major sections and `###`/`####` only for nested procedural sub-steps — don't skip levels.
- For sequential setup guides, number top-level headings (`## 1. ...`, `## 2. ...`) and carry the number into subheadings (`### 2.1 ...`, `#### 2.2.1 ...`) so steps stay referenceable (e.g. "Application user exists in the target environment (Section 3)").
- Separate major `##` sections with a horizontal rule (`---`) in long guides. Short pages (a handful of sections) can omit it.
- Prefer descriptive headings over generic ones (`## Repository Architecture`, not `## Details`).

## Intro and Audience

Open with 1-2 sentences stating what the page covers — either right after the H1, or under an explicit `## Overview` heading for longer guides. For guides with a specific reader, add a bold **Audience:** line naming who it's for.

**Example** (from `Canvas-Apps-Setup-Guide.md`):

> This guide explains how to install and configure the `canvas-apps` plugin so an AI coding agent can author and edit Canvas Apps live against a session open in Power Apps Studio.
>
> **Audience:** Power Apps makers and pro developers using Claude Code or GitHub Copilot CLI to build or edit Canvas Apps.

## UI Steps and Navigation

Write configuration steps as numbered lists. Use **Click** for UI interactions and bold the exact UI text (button, menu item, field label) — this repo's guides target a maker/developer audience configuring Power Apps Studio, the Power Platform Admin Center, and CLI tools like Claude Code or GitHub Copilot CLI.

Chain a fixed menu path with `→` instead of writing separate steps for each hop:

```markdown
1. Open the app in **Power Apps Studio** (make.powerapps.com)
2. Go to **Settings** → **Updates** → **Coauthoring**
3. Toggle **Coauthoring** on
```

Use nested bullets under a numbered step to list the fields or values being configured:

```markdown
3. Configure:
   - **Scope**: User (reusable across sessions)
   - **Studio URL**: Paste the full URL from your open browser tab
```

## Callouts

Use a bold inline label, not a blockquote:

```markdown
**Note**: Ensure each service connection successfully verifies before saving.
```

Reserve **Important**/**Warning**/**Note** for information that's easy to miss or that changes the outcome (a step needing manual follow-up, an async operation, a security tradeoff) — not routine content.

For scannable lists of benefits, best practices, or key principles, lead each bullet with an emoji instead of writing a paragraph:

- ✅ confirmed behavior, benefits, or a best-practice list read as prose (not an interactive checkbox)
- ⚠️ a caveat serious enough to stop and read (async operations, irreversible actions)
- 📖 a pointer to a more detailed doc

```markdown
✅ **Centralized secret management** - One source of truth
⚠️ **Asynchronous Operation:** Pipeline completes quickly, but the actual copy continues in background
📖 **Documentation:** [pipelines/provision-environment/README.md](../pipelines/provision-environment/README.md)
```

Reserve literal `- [ ]` checkboxes for validation/audit checklists the reader works through and checks off (see `Two-Repository-Validation-Guide.md`) — not for prose lists of facts.

## Tables

Use tables for reference data the reader scans or looks up, not narrative content:

- Environment/status registries (name, URL, status)
- Path/variable references (item, value per pipeline, notes)
- "What goes where" comparisons

Keep cell content terse — a phrase, a code-formatted value, or a single emoji status marker, not a paragraph.

## Diagrams

Prefer Mermaid diagrams in fenced ` ```mermaid ` blocks for architecture and data-flow (e.g., *Terminal (agent) → CLI plugin/skills → MCP server → `.pa.yaml` sync → Power Apps Studio*) — GitHub renders them natively, so no image export step is needed. Fall back to ASCII box-drawing diagrams only when the reader will view the page in a plain-text terminal. Label arrows with the action (`Sync`, `Compile`, `Deploy`).

## Code Blocks

Always fence with a language identifier (` ```powershell `, ` ```yaml `, ` ```json `, ` ```bash `, ` ```typescript `, ` ```markdown `) — use `yaml` for `.pa.yaml` snippets. Use inline `#`/`//` comments in scripts to explain non-obvious parameters, and prefer real example values (plugin names, skill names, sample app names) over generic placeholders like `<value>`.

## Troubleshooting Sections

Structure every troubleshooting entry the same way so readers can scan for their error:

````markdown
### Issue: "Bad Request – Request Too Long"

**Symptom:**
```
Authentication fails or loops when connecting to the MCP server.
```

**Cause:**
1. The browser profile has heavily cached identity cookies for the Studio session
2. ...

**Fix:**
1. Clear cookies or use a clean browser profile, then retry `/configure canvas MCP`
````

Quote the exact error text where possible — readers search for it verbatim.

## Links and Sourcing

- Link to primary sources (Microsoft Learn, the [power-platform-skills](https://github.com/microsoft/power-platform-skills) marketplace repo) for platform and plugin behavior rather than restating it from memory.
- Use relative paths for links to other pages in this repo (e.g., `Canvas-Apps-Setup-Guide.md`, `../../../samples/canvas-apps/README.md`), and absolute URLs for everything external.
- End the page with an `## Additional Resources` section listing cited sources. A long guide can follow it with a `## Support & Feedback` section pointing readers to troubleshooting and known limitations.
- Close with a bolded version footer using hard line breaks (two trailing spaces per line):

```markdown
**Version:** 1.0  
**Last Updated:** January 2026  
**Maintained by:** Power Platform Skills Samples contributors
```

## Writing Style

- Use active voice and imperative mood for steps ("Navigate to...", "Click...", "Verify...").
- Write for a Power Platform maker/developer audience: direct, technical, no marketing language.
- Keep sentences focused — break up long, multi-clause sentences rather than packing in qualifiers.
- Be precise about product-area and plugin names — `canvas-apps`, `code-apps`, `mcp-apps`, `mobile-apps`, `model-apps`, `power-apps-mobile-extension`, `power-automate`, `power-pages` map 1:1 to marketplace plugins and to folders under `docs/power-platform-skills/` and `samples/`; don't use them loosely or interchangeably.
- Flag preview/experimental features explicitly (e.g. "this is public preview") rather than implying GA stability.
- Mark unfinished content explicitly with italics (e.g. `*(add status)*`, `*Update this section once the sample is published.*`) rather than leaving a table cell blank or guessing a value.

## Additional Resources

- [Microsoft Writing Style Guide](https://learn.microsoft.com/style-guide/welcome/) — official Microsoft style and terminology guide.
