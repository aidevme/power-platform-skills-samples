# Speaker Notes — AI-Powered Canvas App Development
### (Microsoft Power Platform Skills — Canvas Apps Plugin)

---

## 1. Introduction

**Talking points:**
- Open with the pain point everyone in the room recognizes: building a Canvas App in Power Apps Studio means manually dragging controls, hand-tuning X/Y/Width/Height on every element, and clicking through dozens of property panes just to get a screen that looks presentable.
- Frame the talk: today isn't about a new low-code feature — it's about a new *authoring model*. Canvas Apps can now be built and edited by an AI coding agent (Claude Code or GitHub Copilot CLI) working live against the app open in Studio.
- Set expectations for the session: what it is, why it matters, how the architecture works under the hood, how to install and configure it, a live/first-build walkthrough, prompting best practices, and — importantly — where the rough edges are today (it's a preview technology).
- One-line hook: "We're not replacing the maker experience — we're giving Canvas Apps a command line."

**Suggested time:** 2–3 minutes.

---

## 2. What Is AI-Powered Canvas App Development?

**Talking points:**
- Define it plainly: it's the ability to author and edit a live Canvas App using natural-language prompts sent to an AI coding agent, instead of (or alongside) point-and-click Studio editing.
- The enabling technology is Microsoft's **Canvas Authoring MCP Server** (`Microsoft.PowerApps.CanvasAuthoring.McpServer`) — a public preview MCP (Model Context Protocol) server that exposes the canvas app's structure to external AI tools.
- This is delivered through the **`canvas-apps` plugin** inside Microsoft's official **`power-platform-skills`** marketplace (github.com/microsoft/power-platform-skills), which packages it as a Claude Code / GitHub Copilot CLI plugin.
- Key distinction to make clear: this is **not** the same as "Power Apps Copilot" chat-based generation inside Studio. This is an external agent (running in your terminal) that connects to the *same session* you have open in the browser and edits the real, live app via coauthoring.
- Mention the sibling plugins in the same marketplace for contrast: Power Pages, Model Apps, MCP Apps, Code Apps, Mobile Apps, Power Automate — Canvas Apps is one of seven plugins, each targeting a different Power Platform surface.

**Suggested time:** 4–5 minutes.

---

## 3. Why This Changes Everything for Power Apps Development

**Talking points:**
- **Source-controlled canvas apps, finally.** The app is represented as `.pa.yaml` — plain-text, diffable, reviewable in a pull request like any other code. This has been Canvas Apps' biggest gap versus "real" development for years.
- **Speed at scale.** Instead of manually positioning every control, describe the screen ("a dashboard with a KPI header, a filterable gallery, and a detail panel") and let the agent generate and lay out the whole thing, then iterate with follow-up prompts.
- **Parallelized screen building.** The underlying skill can plan an entire multi-screen app and then build several screens *in parallel* via specialist sub-agents — a workflow no human maker can replicate manually.
- **Consistency and best practice by default.** The agent applies layout strategy rules automatically (see Section 4) rather than relying on a maker's individual habits.
- **Bridges "citizen dev" and "pro dev."** Pro developers get a CLI and version control; citizen developers still get the familiar Studio canvas — both are editing the *same* app, live, at the same time.
- Caveat to plant early (you'll return to it in Section 8): this is a **preview** technology — powerful, but not yet something to bet mission-critical production timelines on without guardrails.

**Suggested time:** 5–6 minutes.

---

## 4. How It Works: The Complete Technical Architecture

**Talking points — walk through the pipeline end to end:**

1. **You open the app in Power Apps Studio** and enable **Coauthoring** (Settings → Updates → Coauthoring). This is the feature that allows multiple "authors" — human or AI — to edit the same app session concurrently.
2. **The Canvas Authoring MCP Server** launches on your machine via `dnx` (part of the .NET 10 SDK), pulled directly from NuGet. It acts as the bridge/API layer between your AI agent and the live Studio session.
3. **The plugin's `configure-canvas-mcp` skill** parses your Studio URL to extract the four values the server needs to connect:
   - `ENV_ID` — the environment ID segment of the URL
   - `APP_ID` — URL-decoded app ID from the `app-id` query parameter
   - `MAKER_HOSTNAME` — e.g. `make.powerapps.com`
   - `CLUSTER_CATEGORY` — derived from hostname (`prod` for `make.powerapps.com` / `make.preview.powerapps.com`, `test` otherwise)
4. **The agent syncs the app's source**, represented as **`.pa.yaml`** — Microsoft's canonical, human-readable YAML source format for canvas apps (controls, properties, Power Fx expressions, screens).
5. **Two specialist agents split the work:**
   - `canvas-app-planner` — discovers available data sources/resources and writes the build plan
   - `canvas-screen-builder` — executes individual screen creation or modification, run in parallel across screens
6. **Layout strategy is chosen automatically:** AutoLayout for responsive, multi-device screens; ManualLayout for fixed desktop-style dashboards.
7. **Every change is validated through compile/error-fix loops** before being applied — the agent doesn't just emit YAML, it compiles it, checks for Power Fx errors, and iterates until it's clean.
8. **Changes are compiled and pushed back into the live coauthoring session**, so they appear in Studio in near real time — the human maker watching the canvas sees it update.
- Emphasize: nothing here bypasses Power Platform's normal security model — auth happens through your signed-in Studio session/tenant identity, and no tenant ID, environment ID, app ID, token, or coauthoring link ships bundled with the plugin.

**Suggested visual:** a simple left-to-right diagram — *Terminal (agent) → CLI plugin/skills → Canvas Authoring MCP Server (dnx) → .pa.yaml sync → Power Apps Studio (coauthoring session, live canvas)*.

**Suggested time:** 8–10 minutes (this is your technical core — slow down here).

---

## 5. Getting Started – Installation, Configuration & Setup

**Talking points:**

**Prerequisites (state these clearly — they trip people up):**
- Claude Code or GitHub Copilot CLI installed
- **.NET SDK 10 or newer** (verify with `dotnet --list-sdks`)
- `dnx` (ships with supported .NET 10 SDK installs)
- Edit access to a Canvas App in Power Apps Studio
- **Coauthoring enabled** in that app's settings

**Step 1 — Install the marketplace and plugin:**
```
/plugin marketplace add microsoft/power-platform-skills
/plugin install canvas-apps@power-platform-skills
```
(Or run the one-line installer script from the repo, which also installs the `pac` CLI and enables auto-update.)

**Step 2 — Configure the MCP server connection** using the `configure-canvas-mcp` skill:
```
/configure canvas MCP
```
- You'll be asked for a **scope** (typically user-level, so the server is reusable across sessions) and the **Studio URL** — just copy/paste the full URL from your open browser tab.
- Approve the accompanying permission prompts.

**Step 3 — Restart the session** so the MCP tools register:
```
/exit
claude --continue
```
Then verify the connection:
```
claude mcp list
```

**Known setup gotcha to mention:** authentication can loop or throw a "Bad Request – Request Too Long" error if your browser profile has heavily cached identity cookies — clearing cookies or using a clean profile resolves it. Also flag that Windows tends to be the smoothest auth environment (it can reuse the signed-in Windows account); Linux/SSH hosts may need device-code or browser-based auth, subject to your tenant's Conditional Access policy.

**Suggested time:** 5–6 minutes (consider a live demo here instead of slides).

---

## 6. Building Your First AI-Generated Canvas App

**Talking points:**
- Introduce the core skill: `/generate canvas app` (or `canvas-app` / `generate-canvas-app`), invoked with a detailed natural-language prompt describing the app.
- Walk through the **two-mode workflow**:
  - **CREATE mode** — for a brand-new app: the agent runs a *preference wizard* (auto-detecting gaps and asking only necessary questions — target users, aesthetic, key features), then plans and builds in parallel.
  - **EDIT mode** — for an existing app: the agent assesses complexity and either makes a simple inline change or executes a full planned multi-screen redesign.
- Suggested live example: prompt something concrete, e.g. *"Build a field-inspection app with a home screen listing open inspections, a detail screen with photo capture and notes, and a submit flow that writes back to a SharePoint list."*
- Show what happens step by step: preference questions → plan output (screens, data sources, layout strategy) → parallel screen build → compile/validate loop → live update appears in Studio.
- Also cover **`add-data-source`** — the skill for connecting the app to a data source (Dataverse, SharePoint, SQL, etc.) as part of the build.
- Close the loop back to Studio: the human maker can now open the same app, see the generated screens, and keep editing by hand — it's not agent-only from this point forward.

**Suggested time:** 8–10 minutes (best section for a live demo or recorded clip).

---

## 7. Advanced Techniques – Prompting, Patterns & Best Practices

**Talking points:**
- **Be specific about data first.** Naming real data sources/tables up front (rather than "some data") produces far more accurate plans, since `canvas-app-planner` grounds itself in discovered resources.
- **Describe layout intent, not pixel values.** Say "responsive dashboard for phone and tablet" (triggers AutoLayout) vs. "fixed desktop layout with a left nav rail" (triggers ManualLayout) — let the agent choose the underlying technique rather than specifying coordinates.
- **Iterate in EDIT mode rather than regenerating.** Small, targeted follow-up prompts ("add a search box to the gallery header") are cheaper and safer than re-running full generation.
- **Use the planning step as a checkpoint.** Since CREATE mode plans before building, review the plan output and correct scope *before* parallel screen builds kick off — much cheaper than fixing four screens after the fact.
- **Trust, but verify, Power Fx.** The compile/error-fix loop catches syntax errors, but always review generated formulas for *business logic* correctness — the agent validates that code compiles, not that it's doing what you actually meant.
- **Treat `.pa.yaml` as source code.** Once apps are in this format, apply normal engineering hygiene: commit to source control, use pull requests/diffs to review agent-generated changes, and consider this the natural on-ramp to CI/CD for canvas apps.
- **Back up before large edits.** Since this is preview technology operating on a live app, export/back up important apps before large multi-screen EDIT-mode operations.

**Suggested time:** 6–7 minutes.

---

## 8. Production Readiness – Troubleshooting & Limitations

**Talking points — be candid here, this builds credibility:**

**Status:** The Canvas Authoring MCP Server and the `canvas-apps` plugin are explicitly **public preview**. Treat generated changes as reviewable drafts, not blind-trust production commits.

**Common issues and fixes:**
- *"MCP not working" / not configured* → re-run `/configure canvas MCP`; this is exactly what the `configure-canvas-mcp` skill exists to resolve.
- *Auth loop / "Bad Request – Request Too Long"* → usually caused by a stale/heavily cached browser identity session; use a clean profile or clear cookies.
- *Missing prerequisite errors* → almost always a missing or outdated .NET SDK; confirm with `dotnet --list-sdks` before troubleshooting anything else.
- *Environment/tenant access errors* → check Conditional Access policies, especially on Linux/SSH hosts where device-code or browser auth is required instead of seamless Windows SSO.

**Permission-prompt fatigue:** Because the plugin invokes file edits, shell commands, and MCP calls, you'll hit frequent approval prompts by default. For smoother sessions (in trusted/sandboxed environments only):
- Claude Code: set `"defaultMode": "acceptEdits"` in `settings.json` with an explicit allow-list (`Bash(npm run *)`, `Bash(git *)`, `Bash(pac *)`), or use `claude --dangerously-skip-permissions` for full auto-accept.
- Copilot CLI: `--allow-tool` for specific tools, or `--allow-all-tools` (optionally paired with `--deny-tool` for destructive commands like `rm` or `git push`).
- **Explicit warning to give the audience:** auto-approval modes grant the agent the same access you have on your machine — only use them in trusted or sandboxed environments.

**Known limitations to set expectations on:**
- Preview server, subject to breaking changes
- Coauthoring must be explicitly enabled per app
- Auth/session behavior varies meaningfully by OS and tenant policy
- Validation covers compilation, not business-logic correctness
- Telemetry is on by default for plugins that ship it; can be opted out per plugin (`/<plugin>:telemetry off`) or via environment variable for CI/automation

**Suggested time:** 6–8 minutes.

---

## 9. References

Use this as your closing slide — read a few aloud, leave the rest for follow-up:

- **Marketplace repo:** github.com/microsoft/power-platform-skills (short link: **aka.ms/ppskills**)
- **Canvas Apps plugin docs:** `plugins/canvas-apps/AGENTS.md` in the repo
- **Configure-canvas-MCP skill:** `plugins/canvas-apps/skills/configure-canvas-mcp/SKILL.md`
- **Canvas App generation skill:** `plugins/canvas-apps/skills/canvas-app` (a.k.a. `generate-canvas-app`)
- Microsoft Learn — Power Apps Code Apps: learn.microsoft.com/power-apps/developer/code-apps
- Microsoft Learn — PAC CLI Reference: learn.microsoft.com/en-us/power-platform/developer/cli/reference
- Claude Code permissions docs: code.claude.com/docs/en/permissions
- GitHub Copilot CLI docs: docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli

---

## 10. Question / Closing Slide

**Talking points:**
- Invite questions — good prompts to seed discussion if the room is quiet: *"Who here is already storing canvas apps outside of Studio?"* or *"What's the one Studio pain point you'd most want an agent to take off your plate?"*
- Reiterate the two big takeaways to leave people with:
  1. Canvas Apps now has a real source format and a real CLI-based authoring path.
  2. It's preview technology — adopt it for experimentation and acceleration today, and layer in review/backup discipline before treating it as a production pipeline.
