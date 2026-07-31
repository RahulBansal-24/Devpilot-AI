# AI Software Engineering Assistant
### Project Documentation — OpenAI Agents SDK Capstone (Summer School '26)

**Project #13 — Domain: Software Development**

---

## 1. Problem Analysis

### 1.1 Business Context

Modern software teams spend a large share of engineering time on activities that
surround writing code rather than writing code itself: triaging incoming issues,
scoping requirements, reviewing pull requests, writing tests, documenting changes,
and root-causing bugs. These tasks are repetitive, well-specified, and highly
suited to automation, but they still require judgment, context-awareness, and
handoffs between different "roles" (analyst, developer, reviewer, tester, writer,
debugger) — which is exactly the structure a multi-agent system is designed for.

Small teams and individual developers in particular lack the bandwidth to have a
dedicated requirements analyst, a dedicated reviewer, and a dedicated technical
writer on every task. An AI Software Engineering Assistant that plays all of these
roles — while still keeping a human in control of anything that touches the
filesystem or executes code — directly addresses this gap.

### 1.2 Stakeholders

| Stakeholder | Interest in the system |
|---|---|
| **Individual developers / small teams** | Faster turnaround on routine SDLC tasks (spec-writing, tests, docs, bug triage) without hiring specialists for each role |
| **Engineering managers** | Consistent, structured artifacts (specs, reviews, test reports) instead of ad-hoc notes; auditable decision trail |
| **Open-source maintainers** | Faster first-pass triage of incoming issues, cross-referencing against existing GitHub issues before work starts |
| **QA / reviewers** | A first line of automated code review and test generation, freeing human reviewers to focus on harder judgment calls |
| **End users of the software being built** | Indirectly benefit from more thoroughly reviewed, tested, and documented code |

### 1.3 Problem Statement

Development teams need an assistant that can analyse a GitHub issue or feature
request end-to-end — understanding the requirement, implementing it, reviewing the
implementation, testing it, documenting it, and investigating any resulting bugs —
while producing structured, reusable artifacts at every step, and without taking
unsupervised action on the codebase (every write or code-execution action must be
explicitly approved by a human).

### 1.4 Objectives

1. Turn a raw issue/feature request into a structured, actionable specification.
2. Generate working code for that specification, with an automated review/reflection
   cycle before the code is considered "done."
3. Generate and execute a test suite for the code, producing a structured pass/fail
   report.
4. Generate human-readable documentation for the resulting change.
5. When tests fail or a bug is reported, investigate the root cause and propose a
   fix, cross-referencing similar issues on GitHub.
6. Keep a human in the loop for every action that writes to disk or executes code.
7. Persist both short-term (per-run) and long-term (cross-run) memory so the
   assistant has context across a project's lifetime.

---

## 2. Multi-Agent Design

### 2.1 Agent Architecture

The system uses **two complementary orchestration patterns**, both built on the
OpenAI Agents SDK:

1. **Triage / handoff pattern** — a lightweight `Triage Agent` uses the SDK's
   native `handoffs=[...]` mechanism to route a single natural-language request to
   exactly one specialist agent. This is the conversational entry point.
2. **Orchestrated pipeline pattern** — a hand-written async function
   (`run_full_sdlc_pipeline`) chains multiple specialists together to run an entire
   feature end-to-end, including a **reflection loop** (Coding ⇄ Review) and
   **parallel execution** (Testing ‖ Documentation).

```
                         ┌─────────────────┐
             user ─────► │  Triage Agent   │  (handoff-based routing)
                         └───────┬─────────┘
              ┌──────────────────┼──────────────────────────────┐
              ▼                  ▼                               ▼
   Requirements Analysis   Coding Assistant  ...  Code Reviewer / Testing /
        Agent                  Agent               Documentation / Bug agents


                    Orchestrated full-lifecycle pipeline:

  Requirements  ──►  Coding ⇄ Review (reflection loop, up to N rounds)
                                  │
                                  ▼
                    ┌─────────────────────────────┐
                    │   Testing   ‖  Documentation │   (parallel, asyncio.gather)
                    └──────────────┬──────────────┘
                                   │  tests failed?
                                   ▼
                         Bug Investigation Agent
```

### 2.2 Roles of Each Agent

| Agent | Role | Output type (structured) |
|---|---|---|
| **Triage Agent** | Reads a request and hands off to the correct specialist; does not solve anything itself | — (delegates) |
| **Requirements Analysis Agent** | Converts a raw issue into a problem summary, functional/non-functional requirements, acceptance criteria, and a step-by-step implementation plan; checks GitHub for related issues | `RequirementsSpec` |
| **Coding Assistant Agent** | Implements the requirement in Python; lints its own output before returning | `CodeSolution` |
| **Code Reviewer Agent** | Reviews code for correctness, security, and style; issues an approve/reject verdict with severity and concrete suggestions | `CodeReviewResult` |
| **Testing Agent** | Writes a pytest suite and executes it in a sandboxed subprocess; reports pass/fail per test | `TestReport` |
| **Documentation Writer Agent** | Produces Markdown documentation (usage, install, caveats) for a finished code change | `DocumentationOutput` |
| **Bug Investigation Agent** | Root-causes a bug report or failing test report, proposes a fix, and cross-references related GitHub issues | `BugInvestigationReport` |

### 2.3 Agent Interaction & Handoff Flow

- **SDK-native handoffs**: the Triage Agent's `handoffs` list contains all six
  specialists, each wrapped with `handoff(agent, on_handoff=...)` so every handoff
  is logged (which agent received control, and when) into both the run log and the
  console.
- **Reflection loop**: `code_with_reflection()` alternates between the Coding
  Assistant and the Code Reviewer for up to `MAX_REFLECTION_ROUNDS` iterations. If
  the reviewer rejects the code, its `issues_found` and `suggestions` are appended
  to the next prompt sent to the Coding Assistant, closing the feedback loop
  automatically — a self-review / reflection pattern.
- **Parallel execution**: once code is approved, the Testing Agent and
  Documentation Writer Agent are launched concurrently with `asyncio.gather`, since
  neither depends on the other's output.
- **Conditional branching**: the Bug Investigation Agent is only invoked if the
  Testing Agent's structured report shows `all_passed=False` — the pipeline
  branches based on a *typed* field of a prior agent's structured output, not on
  free-text parsing.

### 2.4 Tool Integration Overview

| Tool | Used by | Approval required? | Notes |
|---|---|---|---|
| `read_code_file` | Coder, Reviewer, Testing, Docs, Bug agent | No (read-only) | Reads from the shared `workspace/` folder |
| `write_code_file` | Coding Assistant | **Yes** | Writes/overwrites a file in `workspace/` |
| `lint_python_code` | Coder, Reviewer, Bug agent | No (static, read-only) | `compile()`-based syntax check |
| `run_python_tests` | Testing Agent | **Yes** | Executes pytest in an isolated subprocess with a 30s timeout |
| `search_github_issues` | Requirements Agent, Bug agent | No (read-only, external API) | Live call to the public GitHub REST Issues Search API |
| `save_documentation` | Documentation Agent | **Yes** | Writes Markdown to `docs/` |
| `save_structured_report` | Pipeline/reporting | No | Persists a JSON report to `outputs/` |

Approval-gated tools call `request_human_approval()`, which blocks execution and
prints the proposed action and a content preview, requiring an explicit `y`
response before proceeding (with an `AUTO_APPROVE` override for unattended demo
runs).

---

## 3. Implementation Summary

- **Minimum 5 specialised AI agents** → 6 implemented (Requirements, Coding,
  Review, Testing, Documentation, Bug Investigation), plus a 7th Triage/Orchestrator
  agent for routing.
- **Minimum 5 tools/APIs** → 7 implemented (file I/O ×2, linter, sandboxed test
  runner, live GitHub Issues API, doc saver, report saver).
- **Agent handoffs** → SDK-native `handoffs=[...]` on the Triage Agent, each
  instrumented with an `on_handoff` logging callback.
- **Memory/context management** → three layers: `EngineeringContext` (per-run
  working memory passed to every `Runner.run(context=...)` call),
  `ProjectMemoryStore` (JSON-file long-term memory keyed by project name), and
  `SQLiteSession` (SDK-native conversation history persisted to disk).
- **Structured outputs** → every specialist agent declares a Pydantic `output_type`
  (`RequirementsSpec`, `CodeSolution`, `CodeReviewResult`, `TestReport`,
  `DocumentationOutput`, `BugInvestigationReport`), so downstream agents and code
  consume typed data, not free text.
- **Human approval** → `request_human_approval()` gate in front of `write_code_file`,
  `run_python_tests`, and `save_documentation`.

## 4. Advanced Features Implemented

| Feature | Implementation |
|---|---|
| Planning & reasoning | Requirements Analysis Agent produces a typed, step-by-step `implementation_plan` with an owning agent per step |
| Reflection / self-review | Coding Assistant ⇄ Code Reviewer loop, up to `MAX_REFLECTION_ROUNDS`, feeding rejection feedback back into the next attempt |
| Parallel agent execution | Testing Agent and Documentation Writer Agent run concurrently via `asyncio.gather` |
| Error handling and logging | Centralised `logging` logger (file + console handlers); every pipeline stage wrapped in `try/except` with a `status`/`error` field in the final report |
| Session persistence | `SQLiteSession` persists the Triage Agent's conversation across notebook runs |
| Long-term memory | `ProjectMemoryStore` appends a JSON history record per project on every run, independent of any single chat session |