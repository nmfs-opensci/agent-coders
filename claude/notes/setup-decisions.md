# Setup chapter decisions

Eli said the original Setup page was "completely wrong": it told participants to
export `GATEWAY_URL`, `GATEWAY_KEY` and `ANTHROPIC_*` variables by hand. Clinic
participants never do that. They work on the NMFS Openscapes JupyterHub and run
a shared script that gets a key, installs Claude Code and sets everything.

The steps Eli specified, in order:

1. Log in to GitHub.
2. Go to https://nmfs-openscapes.2i2c.cloud/.
3. Start a server with 4 GB.
4. Open a terminal.
5. Run `~/shared/workshop/claude-agent-coders` and enter the workshop code.
   **Say no to trusting the folder**, then close Claude Code. The first run starts
   in the home directory, and its only job is to install Claude Code and save
   the key. This looks odd but is deliberate.
6. `gh auth login`.
7. Create a repo in the participant's own account from the template
   `nmfs-opensci/NOAA-quarto-book` (`gh repo create ... --template ... --clone`).
8. In a new terminal, `cd` into that repo and run `claude-agent-coders`. Trust it
   this time.

Details:

- The script path is `~/shared/workshop/claude-agent-coders`. An older
  `~/shared/agent-coders/claude-tester` also exists on the hub; it is not the one
  to document.
- The short name `claude-agent-coders` only works in terminals opened after the
  first run (the script adds `~/.local/bin` to PATH in login files).
- Budget checks use `claude-agent-coders --budget`; a new workshop uses `--reset`.
  The `curl .../key/info` recipe was removed from the book because hub users
  have no `GATEWAY_*` variables.
- The model table follows `~/agent-coders-issuer/models.yaml`; `deepseek-v3.2`
  was dropped from the gateway.
- Source of truth for these steps: `~/agent-coders-issuer/docs/hub-quickstart.md`.

## Antigravity (agy) setup decisions (Issue #12)

Antigravity instructions are integrated alongside Claude Code in `setup.qmd` using panel tabsets for tool-specific steps (step 5 and step 8).

Why and what's deliberate:

- **Launcher over plain `agy`:** Out of the box, `agy` prompts before running *every* shell command, making normal coding impossible without constant interruptions. The team launcher at `~/shared/agent-coders/agy-agent-coders` (source: `agent-coders-clinics` repo `hub/agy-agent-coders`) pre-approves safe everyday tools (`ls`, `cat`, `grep`, `mkdir`, `touch`, `R`, `Rscript`, `quarto`, `python`, `python3`, `git`, read-only `gh`, `curl`) and sets `defaultMode: accept-edits`.
- **Gzip install fix:** Upstream install script (`curl -fsSL https://antigravity.google/cli/install.sh | bash`) fails on the hub because the server returns gzipped content uncompressed; the launcher handles installation using `curl --compressed`.
- **First start sign-in:** First start installs `agy` and prompts for Google OAuth ("click to authenticate"), followed by pasting the Google Cloud project code from Jira, and accepting 'Agent Platform'.
- **Guards:** The launcher installs an agy hook (`agy-agent-coders --hook`) blocking `git reset --hard`, force pushes without lease, `git clean -f`, and recursive deletions (`rm -r`) outside the project root. Blocked commands report `agent-coders guard:`.
- **Project folder boundary:** Users must `cd` into their project repository before starting `agy-agent-coders` because `agy` takes the launch directory as the project root and `rm -r` boundary.
- **Why approvals still appear:** Unlisted commands (copying, moving, deleting files with `rm`, `pip`, or chained commands with an unlisted segment) intentionally prompt for confirmation.
- **Hub constraints:** `agy --sandbox` fails because hub containers do not allow nested unprivileged user namespaces. Non-interactive `agy -p` stops at the first command needing confirmation.

## Setup and Activity 1 reorganization (Issue #13)

- **Setup page streamlined:** Repository creation and first agent launch were moved out of `setup.qmd` into Activity 1 (`content/activity-repo-analysis.qmd`) where participants actually work on their practice repository.
- **Tabset consistency:** In `setup.qmd`, the tool-specific setup step, "Good to know", and "If something goes wrong" all use panel tabsets separating Claude Code from Antigravity.
- **Activity 1 startup:** In `content/activity-repo-analysis.qmd`, participants create their repository from the template using `gh repo create my-quarto-book --template nmfs-opensci/NOAA-quarto-book --public --clone` and launch their chosen agent with tabbed instructions for `claude-agent-coders` vs `agy-agent-coders`. (Issue #10 moved `gh repo create` into the `gh auth login` tab; see below.)



## GitHub access via fine-grained token (Issue #10)

Eli chose (2026-10-03) to offer two options in a tabset in Activity 1, token
first (default tab): a fine-grained token scoped to the practice repo, or
`gh auth login` for people fine with the agent doing anything they can on GitHub.
Goal of the token: the agent works on the practice repo without the hub holding a
credential for private work-organization repos.

- **Considered and rejected: `gh-scoped-creds`.** It is installed on the hub and
  configured (`GH_SCOPED_CREDS_CLIENT_ID`, app
  `nmfs-openscapes-github-push-access`), but that GitHub App is owned by
  2i2c-org and has only `contents: write` + `metadata: read`, so `gh pr` /
  `gh issue` (Skills 100 workflow) would fail, and it only wires up git, not
  `gh`. A self-owned app with PR/Issues permissions would fix that; Eli chose the
  PAT instead.
- **The whole GitHub flow lives in Activity 1**, not Setup. In the token tab the
  repo is created on the website ("Use this template") *before* the token;
  `gh repo create` cannot be used there — a selected-repositories token cannot
  create repos. The `gh auth login` tab keeps `gh repo create --template --clone`.
- **Token is stored by `gh`, not passed via `GH_TOKEN` (deliberately departs from
  issue #10).** The issue's flow (`read -rsp` → `export GH_TOKEN`, re-done in every
  terminal, never saved) was dropped: Eli said it was excessive for a learning
  activity and that the hassle would push everyone to browser `gh auth login`.
  Instead the token tab runs interactive `gh auth login` → "Paste an
  authentication token" (masked input). `gh` saves it in `~/.config/gh/hosts.yml`
  (plain text on the hub, like a browser login), so every terminal and agent
  works, and it replaces any earlier browser login for that account, so no
  logout step is needed. `gh auth login --help` warns fine-grained tokens via
  login "may cause confusing behaviour" with other resources and favours
  `GH_TOKEN`; that confusion (e.g. `gh repo create` failing) is the intended
  restriction, and Troubleshooting says so.
- **Permissions:** Contents, Pull requests and Issues all read/write, because
  Skills 100's CLAUDE.md prompt has the agent create issues, open and merge PRs,
  and delete branches. Issue #10 said grant PR/Issues only if exercises use them;
  they do.
- **Not yet checked with a real token:** login via pasted fine-grained token
  (whether gh's scope check complains), clone/push, PR/issue creation, and that
  a private org repo is refused.

## Guardrails and Skills 100 in both tools (PR #18, 2026-10-03)

- **`claude-agent-coders` sets `CLAUDE_CONFIG_DIR`** (`~/.config/claude-agent-coders/claude`), so
  Claude Code reads its global `CLAUDE.md` from there and **ignores `~/.claude/CLAUDE.md`**
  (checked by running Claude Code with a scratch config dir and a canary). The Skills 100
  prompt therefore gives the agent a rule, not a path: Claude Code uses
  `$CLAUDE_CONFIG_DIR/CLAUDE.md` if set, else `~/.claude/CLAUDE.md`; Antigravity uses
  `~/.gemini/config/AGENTS.md`. Asked which path they would write, both tools picked
  the right one. One prompt for both tools was Eli's ask; participants are not taught
  the paths.
- **Auto mode needs Sonnet or Opus.** `claude --permission-mode auto` started in
  `default` with Haiku 4.5 (the launcher's default model) and in `auto` with Sonnet 4.6.
  Tested on a personal Anthropic account, not through the gateway. Per the Claude Code
  docs, auto mode allows pushing to `main`, so the page says to start tasks on a branch.
- **Since 2026-10-03 the launcher starts in auto mode on Sonnet** (agent-coders-gateway#8).
  Through the gateway with a test key: init showed `model=claude-sonnet-4-6
  permissionMode=auto`, and Bash ran unprompted. Whether switching to Haiku mid-session
  drops auto mode was not tested, so the pages say only that auto mode is not available
  with Haiku.
- **The agy guard's "ask" is a stop-and-ask the agent honours.** agy hooks can only deny;
  the agent reruns with `USER_CONFIRMED=1` after the person says yes. The participant
  page leaves that prefix out on purpose. For `gh pr merge` and `gh issue close`, which
  are not on the team allow list, agy's own approval prompt follows as well.
- **Skills 100 "Task is done"** asks before merging and before deleting the branch, to
  match the guard and the clinic's branch → PR → review → merge workflow.
- **Not done:** walking the Skills 100 prompt end to end as a participant, and seeing auto
  mode's classifier block a risky action through the gateway (a test was refused by the
  model itself first).

## Consistency pass after Setup cleanup (PR #22, 2026-10-13)

- **Why the pages changed:** Eli streamlined Setup and moved GitHub access into it. The other pages still described the old flow, used three different "clear" prompts, and disagreed on handoff files (dated `handoff-YYYY-MM-DD.md` in Skills 101 vs one file in Skills 100). Settled on one rolling `notes/handoff.md` because that is what Skills 100's prompt and Eli's own workflow use.
- **"Cannot push to main" is Antigravity-only.** Claude Code's auto mode allows pushes to `main`; Skills 100 and Activity 1 say so instead of implying both tools block it.
- **Skills 102 = real Agent Skills** (Eli's choice, option "b"). The old page claimed the agent finds `SKILLS.md` on its own; it does not. The prompt asks the agent to put the skill "where you load skills for this project" and report the path, so learners are not taught paths ([[keep-paths-out-of-learner-docs]] memory).
- **Not verified:** that Antigravity loads *project-level* skills, and that `/skill-name` works in Antigravity (`agy --help` mentions "skill expansion" for slash commands, untried). The page only promises `/render-check` for Claude Code.
- **Left alone on purpose / still open:** Setup's and Troubleshooting's workshop-code error messages (may be stale now that keys come from `agentcoders-keys.md`; Eli has not said); README body is still the template's.

