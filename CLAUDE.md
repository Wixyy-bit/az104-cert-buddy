# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **content-only repository** for an AZ-104 certification study buddy powered by GitHub Copilot agents. There is no application code, no build system, and no tests. The primary artifacts are agent definitions, skill specs, prompt templates, and MCP server configurations.

## Architecture

```text
.github/
  agents/az104-cert-buddy-agent.agent.md   # Main Copilot agent definition
  skills/
    az104-item-creator/SKILL.md            # Exam question generation skill
    az104-lab-creator/SKILL.md             # Practice lab generation skill
    az104-study-planner/SKILL.md           # Personalized study plan skill
  prompts/
    az104-practice-questions.prompt.md     # Prompt template for practice questions
    az104-practice-lab.prompt.md           # Prompt template for practice labs
  copilot-instructions.md                  # Copilot workspace instructions + rename table
  workflows/
    validate.yml                           # CI validation pipeline (non-blocking)
    mlc-config.json                        # Markdown link checker config
.vscode/
  mcp.json                                # MCP server definitions (workspace-scoped)
  extensions.json                          # Recommended VS Code extensions
.editorconfig                              # Editor formatting consistency
references/
  az104-objectives.md                      # AZ-104 skills-measured reference (April 2025)
  fictional-companies.md                   # Microsoft fictional company names for scenarios
  style-guide.md                           # Microsoft Writing Style Guide key principles
  style-guide.pdf                          # Microsoft Writing Style Guide (full source PDF)
CONTRIBUTING.md                            # Contribution guidelines and PR process
SECURITY.md                                # Security policy and vulnerability reporting
```

### How It Works

The **az104-cert-buddy-agent** orchestrates three skills:

- **az104-item-creator**: Generates exam-realistic AZ-104 practice questions (multiple-choice, scenario-first stems, 4 options, rationale for each). Supports "hint" (eliminate a distractor) and "skip" (reveal the answer) during interactive delivery.
- **az104-lab-creator**: Generates 10-20 minute self-validating practice labs with prerequisites, tasks, validation gates, troubleshooting, and cleanup.
- **az104-study-planner**: Generates personalized study plans based on user confidence ratings across the five AZ-104 skill areas.

All three skills enforce a strict grounding chain: **Microsoft Learn first** (accessed via Context7 and Copilot web search) -> **Context7 for CLI/PowerShell syntax** -> **Azure MCP for reality checks**.

Questions use a **two-phase interactive delivery**: Phase 1 presents only the stem and choices (no answer), then the agent waits for the user to reply. Phase 2 reveals the correct answer, 2-sentence-per-choice rationale, and references. Users can type "hint" to eliminate a distractor, "skip" to reveal the answer, or an unrecognized input triggers a prompt for A/B/C/D.

### MCP Servers

Defined in `.vscode/mcp.json` with IDs:

- `az104buddy-azure` — Azure MCP via `npx @azure/mcp@latest` (validate commands, resource types, properties)
- `az104buddy-context7` — Context7 (version-specific docs/snippets)
- `az104buddy-markitdown` — MarkItDown (convert PDFs/Office docs to markdown)

### Cross-Reference Dependencies

- **Prompt files** reference the agent via `agent: az104-cert-buddy-agent` in YAML frontmatter. If the agent `name` field changes, update all `.github/prompts/*.prompt.md` files.
- **Agent file** references skills by their YAML frontmatter `name` (not folder name). If a skill is renamed, update the agent's `skills` list.
- **Tool IDs** in agent and prompt files must match server IDs in `.vscode/mcp.json`.

## Authoring Conventions

- **Skill files**: YAML frontmatter (`name`, `description`) followed by Markdown body. The `name` field in frontmatter is the canonical skill identifier (not the folder name).
- **Prompt files**: YAML frontmatter with `name`, `description`, `agent`, and `tools` fields followed by Markdown body.
- **Agent files**: YAML frontmatter with `tools` and `skills` lists. Tool IDs must match MCP server IDs from `.vscode/mcp.json`.
- **Plain ASCII only** — no curly quotes, no en dashes, no em dashes. Use straight quotes and `--`.
- **No contractions** in any generated content.
- **Microsoft style** — use official UI labels, sentence-style capitalization, and Microsoft instruction formatting.
- **Current terminology only** — never use retired Azure product names (e.g., "Azure AD" -> "Microsoft Entra ID"). A full rename table lives in `copilot-instructions.md`.
- Negatives only when required; if used, **CAP** + **bold** the negative word.
- Distractors in questions must reference real Azure services/flags (never invent fake ones).
- Labs must always include cleanup steps that remove all created resources.
- **Rationale depth** — every choice explanation must be exactly 2 sentences (why correct/incorrect + context).
- **Fictional companies** — use names from `references/fictional-companies.md` (Contoso, Fabrikam, Tailwind Traders, etc.) for scenario context in questions and labs.

## Default Behaviors

When editing agent or skill content, preserve these defaults:

- If the user does not specify Portal vs CLI vs PowerShell, labs default to **Azure CLI**.
- If the user does not specify a skill area, the agent picks one from the AZ-104 study guide.
- Labs prefer the lowest-cost Azure resources and include a cost warning when that is not possible.
