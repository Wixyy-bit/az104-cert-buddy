---
name: az104-practice-lab
description: "Generate one short AZ-104 practice lab validated with Azure MCP, defaulting to Azure portal steps."
argument-hint: "skillArea='Implement and manage storage' objective='Configure Azure Blob Storage' toolPreference='Portal' timebox='15'"
agent: az104-cert-buddy-agent
tools:
  - az104buddy-azure/*
  - az104buddy-context7/*
  - az104buddy-markitdown/*
---

Generate **ONE** short, self-validating **AZ-104** practice lab.

## Use this skill

You must follow the workspace skill **az104-lab-creator** for lab structure, guardrails, workflow, output format, and **delivery rules** (full lab in a single message).

## Inputs (from chat)

- Skill area: ${input:skillArea:Pick an AZ-104 skill area (or leave blank to choose)}
- Objective: ${input:objective:Specific objective to practice (optional)}
- Tool preference: ${input:toolPreference:Portal | Azure CLI | PowerShell (default Portal)}
- Timebox: ${input:timebox:Duration in minutes (default 15)}

## Grounding and validation rules

1. Ground the lab outcome in **Microsoft Learn** using **Context7** tools (which index Learn documentation) and Copilot web search.
2. Validate every lab step with **Azure MCP** tools: resource providers exist, properties and flags are real, validation queries detect success or failure, and cleanup commands are correct.
3. If the lab includes CLI or PowerShell syntax, confirm command names, flags, and module versions with **Context7** tools.
4. Provide **Microsoft Learn URLs** in the References section of the lab output.

## Terminology

Always use current Microsoft product names. Never use retired names (for example, "Azure AD" must be "Microsoft Entra ID"). See the full rename table in `.github/copilot-instructions.md`. If a lab touches identity or governance, double-check every product name against current terminology.

## Tool preference default

This prompt is designed for **Azure portal** practice labs. If the user does not specify a tool preference, default to **Portal** as the primary instruction path. When Portal is the path, steps must reference exact portal blade names, menu items, and button labels as they appear in the Azure portal UI.

## Output format

Use the YAML output format defined in the **az104-lab-creator** skill exactly. The output must include: title, objective, skill_area, estimated_time, prerequisites, starting_state, tasks (each with steps and validation), troubleshooting, cleanup (with validation), and references.

## Style rules

- No contractions.
- No ambiguous "click around until you find it" steps. Every portal step must name the exact blade, menu, or button label.
- Use Microsoft instruction formatting conventions for UI labels (bold for clickable elements, exact casing).
- Distractors in troubleshooting must describe real failure modes, not invented ones.
- All Azure product names must use current terminology (no retired names).
- Cleanup is mandatory. Every lab must end with steps that remove all created resources and a validation gate that confirms removal.
- Keep the lab within AZ-104 scope (administrator tasks, not deep development).
- Prefer lowest-cost resources; include a cost warning if the lab uses resources that incur charges beyond the free tier.
