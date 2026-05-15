# Agent Loop Streaming Skill

This repository contains a Codex skill for designing and implementing frontend experiences that show an agent loop in progress.

The skill helps product designers, developers, and coding agents turn vague "show the agent working" requirements into concrete UX decisions, event contracts, UI states, artifact rendering rules, and acceptance checks.

## Contents

- `SKILL.md` - skill trigger, mental model, workflow, and output format.
- `references/frontend-work-panel.md` - UX patterns for chat transcript, work panel, artifact area, and actions.
- `references/event-contract.md` - application-side event model and reducer-oriented UI state guidance.
- `references/debugging-and-tests.md` - implementation brief template, debugging guidance, and acceptance checks.
- `evals/evals.json` - qualitative evaluation prompts for common agent-loop-streaming scenarios.

## Usage

Install or copy this folder into a Codex skills directory, then invoke it when designing, implementing, or debugging agent loop streaming UI.

Typical requests include:

- Designing a frontend that shows retrieval, tool execution, artifact generation, and confirmation states.
- Converting a product idea into a coding-agent implementation brief.
- Debugging issues such as tool JSON appearing in chat, duplicate work rows, provisional text treated as final output, or artifacts being buried below chat text.

## Scope

This skill is intentionally framework-agnostic. It focuses on stable product and implementation contracts rather than provider-specific streaming event names.
