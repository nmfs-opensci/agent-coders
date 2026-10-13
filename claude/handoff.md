# Handoff — 2026-10-13

## Latest (2026-10-13): Pages aligned with Eli's streamlined Setup (PR #22, merged)

- Eli cut Setup down herself (commits `3880dac`, `601940b`): the Claude key now comes from `~/shared/workshop/agentcoders-keys.md`, and **GitHub Access moved into Setup** with three choices (No access / Token for one repository / Full access). Activity 1 now links there instead of repeating it.
- **One clear phrase and one handoff file across the guide:** "I am about to clear" (Skills 100 also accepts "Get ready to clear") and a single rolling `notes/handoff.md` plus topic notes. Skills 100, 101, How-to and Activity 1 all follow this; keep new pages consistent.
- **Skills 102 teaches real `SKILL.md` skills** (Eli chose this over a plain `SKILLS.md` file, which neither tool reads by itself), made with a copy-paste prompt that names no paths.
- README has a CC0 no-attribution `## Reuse and citation` section. The rest of README is still the NOAA-quarto-book template text.
- Details and what is untested: `claude/notes/setup-decisions.md`, section "Consistency pass after Setup cleanup".

## Earlier (2026-10-03): Guardrails section & Skills 100 for both tools (PR #18, merged)

- Setup has a **Guardrails** section (Claude Code / Antigravity tabs); Activity, Troubleshooting and Tool Comparison link to it. It describes the guard added to `agy-agent-coders` in nmfs-opensci/agent-coders-clinics#15 (blocks pushes to `main` and repo deletion; asks before merge, branch delete, issue close, PRs into others' repos). `claude-agent-coders` has no guard: Claude Code's auto mode (Sonnet/Opus only) or manual mode is the protection.
- Skills 100's prompt now works in Claude Code and Antigravity (it tells the agent which global file to use), asks before merging and deleting branches, and never commits on `main`. "Where does it live?" was cut to a table on purpose: Eli does not want learners taught the paths.
- Facts found by running, and why: `claude/notes/setup-decisions.md`, section "Guardrails and Skills 100 in both tools".
- **Shipped 2026-10-03:** `claude-agent-coders` now defaults to Sonnet 4.6 and starts in auto mode (nmfs-opensci/agent-coders-gateway#8, installed in `~/shared/workshop/`). Setup's Guardrails tab and the Models & Budget page say so; costs there are relative to Sonnet. The launcher is rendered in `~/agent-coders-gateway` (`assets/hub-signup.sh` via `scripts/render.py`); the `litellm-bedrock-gateway` skill in agent-skills was deliberately left unchanged.

## Earlier (2026-10-03): GitHub access choice in Activity 1 (Issue #10, PR #16, merged)

- Activity 1's GitHub step is now a tabset: **Token for one repository** (default; fine-grained PAT pasted into `gh auth login`, repo created on the website first) or **Full access** (browser `gh auth login`, with a warning). The issue's `GH_TOKEN`-per-terminal flow was dropped as too much hassle; see notes. Troubleshooting has matching tabs. *(Superseded 2026-10-13: GitHub access now lives in Setup, with a third "No access" option.)*
- Merged without the real-token tests. Issue #10 is still open for them (the PR said "Addresses", not "Closes").
- Still needs a real token: pasting a fine-grained token into `gh auth login` (does gh complain about scopes?), clone/push, PR/issue creation, confirming a private org repo is refused. Issue #10's last validation line is cut off ("Ensure the guide states…").
- Why (including why `gh-scoped-creds` was ruled out): `claude/notes/setup-decisions.md`, section "GitHub access via fine-grained token".

## Earlier (2026-10-03): Antigravity added & Activity 1 updated (PR #14 & #15, merged)

- **Issue #12 (PR #14):** Added instructions for Google Antigravity (`agy`) on JupyterHub via `~/shared/agent-coders/agy-agent-coders`. Setup, Tool Comparison, and Troubleshooting updated with Antigravity details, commands, and safeguards. Decisions in `claude/notes/setup-decisions.md`.
- **Issue #13 (PR #15):** Streamlined Setup page and converted "Good to know" and "If something goes wrong" to Claude Code vs Antigravity panel tabsets. Updated Activity 1 (`content/activity-repo-analysis.qmd`) to use `gh repo create` from template and tabbed agent startup (`claude-agent-coders` vs `agy-agent-coders`). Applied `number-sections: false` in `_quarto.yml`.

## Previous (2026-09-29): Setup rewritten for the hub (PR #9, merged)

The old Setup page described manual gateway env vars; participants actually use the shared hub script. Setup, Troubleshooting and Models & Budget now follow the hub flow. Why and what's deliberate: `claude/notes/setup-decisions.md`.

## What was done (first draft, 2026-09-28)

First draft of the Agent Coders Clinic participant guide — a Quarto book with 12 chapters across 4 parts. All content written, all PRs merged to main, book renders cleanly.

### Book structure (12 chapters)

1. **Welcome & Overview** (`index.qmd`)
2. **Setup** (`content/setup.qmd`) — hub workflow; see `claude/notes/setup-decisions.md`
3. **Models & Budget** (`content/models-and-budget.qmd`)
4. **What Is Agentic Coding?** (`content/what-is-agentic-coding.qmd`) — concepts only
5. **How to Work with an Agent** (`content/how-to-work-with-an-agent.qmd`) — workflow discipline with Mermaid flowchart
6. **Activity: Edit a Quarto Book** (`content/activity-repo-analysis.qmd`) — 3-phase hands-on exercise
7. **Skills 100: Telling the Agent How You Work** (`content/skills-100.qmd`) — global CLAUDE.md with copy-paste prompt
8. **Skills 101: Clear–Handoff–Notes** (`content/skills-101.qmd`)
9. **Skills 102: SKILLS.md** (`content/skills-102.qmd`)
10. **Skills 103: Checking the Agent's Work** (`content/skills-103.qmd`) — verification via domain knowledge, real data, breaking things
11. **Tool Comparison** (`content/tool-comparison.qmd`)
12. **Troubleshooting** (`content/troubleshooting.qmd`)

### Key decisions made

- HTML output only for now (dropped PDF/docx until content stabilizes)
- `/resume` acknowledged but discouraged — handoff notes preferred, especially for gaps and team handoffs
- Verification (Skills 103) framed honestly: you're the domain expert, not the code reviewer. Give the agent real data and constraints, let it break its own work.
- Skills 100 uses a copy-paste prompt so participants don't have to write CLAUDE.md by hand
- Activity is the template itself (NOAA-quarto-book) — participants fork, clone, and practice the full clear/handoff/notes cycle

## What's left

- **Review and polish** — first drafts throughout, could tighten prose and check consistency
- **Eli's intro content** — chapter 4 has a placeholder mention of Eli's ~20 min intro; may need updating once that material is ready
- **Activity repos** — chapter 6 says "pick a public repo" but doesn't suggest specific ones
- **Skills 102** — rewritten for real `SKILL.md` skills (PR #22); the prompt is untested in Antigravity
- **Skills 103** — marked as a bit of a placeholder, could expand with concrete worked examples
- **Test the full activity flow** — walk through the 3-phase exercise end-to-end before the clinic
- **`claude/notes/plan.md`** is now outdated — reflects the original plan, not the current state
- **Walk the Setup steps on a fresh hub account** — not done yet; `/model` with an open model through Claude Code also untested
- **Tool Comparison** updated to cover both hub launchers (`claude-agent-coders` and `agy-agent-coders`)


## Source material

- Hub participant steps (source of truth for Setup): `~/agent-coders-issuer/docs/hub-quickstart.md`
- Manual gateway setup (other tools, own machine): `~/agent-coders-issuer/docs/participant-quickstart.md`
- Current model list: `~/agent-coders-issuer/models.yaml`
- Template repo participants will fork: https://github.com/nmfs-opensci/NOAA-quarto-book
