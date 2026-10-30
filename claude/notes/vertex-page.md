# Vertex page (`content/vertex.qmd`, PRs #24 and #25, 2026-10-30)

A Reference chapter for using Claude Code, OpenCode, Antigravity or Copilot CLI on
your **own** Linux, Mac or Windows computer, billed to a Google Cloud (Vertex)
project. At NOAA that is the USAI program. Eli asked for it to be written for a generic
computer: no hub, no `claude-agent-coders` / `agy-agent-coders` launchers. Eli's
own setup is the `claude-vertex` launcher in `~/claude-config` (project ID in
`~/.config/vertex/project_id`).

## Found by running (hub, Eli's Vertex project, 2026-10-30)

- **Claude Code:** `CLAUDE_CODE_USE_VERTEX=1`, `CLOUD_ML_REGION=global`,
  `ANTHROPIC_VERTEX_PROJECT_ID` plus `gcloud auth application-default login` is the
  whole setup. It defaulted to Opus 5.5.
- **OpenCode (1.18.32):** `GOOGLE_CLOUD_PROJECT` + `VERTEX_LOCATION` work, but its
  built-in catalog sends newer Claude IDs as `claude-…@default`, which Vertex rejects
  as "not found". Declaring the models under
  `provider.google-vertex-anthropic.models` in `opencode.json` fixes it. That step is
  deliberate, not clutter. OpenCode's installer mentions an OpenCode 2 under a
  different package name, so the config may change there. That's why the page avoids npm.
- **Antigravity ignores the Vertex env vars** (`GOOGLE_GENAI_USE_VERTEXAI`,
  `GOOGLE_CLOUD_PROJECT`). Its log showed it billing the GCP project chosen at the
  "Continue with Google Cloud" sign-in, which is saved in its settings. So its tab is
  sign-in steps, not env vars. `/logout` switches projects (the command exists; the
  switch itself is untested).
- **Copilot CLI (1.0.88):** works only for Gemini and gpt-oss. Settings:
  `COPILOT_PROVIDER_TYPE=openai`, base URL
  `…/projects/<P>/locations/global/endpoints/openapi`, and
  `COPILOT_PROVIDER_API_KEY_COMMAND="gcloud auth application-default print-access-token"`.
  The key command is what makes it workable, because Google tokens last an hour. Vertex's
  OpenAI endpoint says Claude is "not servable", and Copilot's `anthropic` type
  doesn't match Vertex's Claude request format. Claude in Copilot would need a
  LiteLLM-style gateway, which is out of scope. Grok returned 404 (approval or naming?).
- **Models depend on the project.** Each Claude model needs its own approval. Eli
  approved only Haiku, Sonnet and Opus 5.5, so other Claude IDs 404 on her project.
  That's expected, not a bug in the page.

## Untested

Anything on Mac or Windows (`setx`, OpenCode's settings path on Windows, whether
Claude Code still needs Git for Windows, the `winget install GitHub.Copilot` name);
running the installers on a fresh machine (URLs only checked to respond); the `us`
region; long multi-tool Copilot sessions on Gemini.
