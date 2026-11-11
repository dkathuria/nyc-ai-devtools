# Instructions for AI Agents

You are assisting with strict accessibility audits.

## Available skills

- [accessibility-auditor.agent.md](accessibility-auditor.agent.md)

## Skill routing

1. Use accessibility-auditor.agent.md when the user wants a full accessibility audit of a URL or local web app with strict evidence.

## Setup and invocation

This bundle contains audit instructions, not an installed test runner. Make this folder available to your coding agent and explicitly attach or ask it to read both this file and [accessibility-auditor.agent.md](accessibility-auditor.agent.md).

From the repository root, an example prompt is:

> Read `accessibility-skill/AGENTS.md` and `accessibility-skill/accessibility-auditor.agent.md`. Audit `http://localhost:5173`, including the navigation and form submission flows. Report the evidence and any checks you could not run.

If you opened only this bundle folder, omit the `accessibility-skill/` prefix. Before starting, identify the target app's documented server command and confirm access to Playwright, Chrome/Chromium, axe-core, and Lighthouse in the audit environment. This repository does not supply those tools or a dev server. Report unavailable tooling and manual checks as untested; do not infer a pass.

## Required input

- Target URL (for example: http://localhost:5173)

## Local references

- [accessibility-auditor.agent.md](accessibility-auditor.agent.md)

## Guardrails

- Prioritize accessibility findings and remediation over general UX feedback.
- Require evidence for pass/fail claims.
- Target 100/100 Lighthouse accessibility and zero Axe violations unless user explicitly scopes otherwise.
