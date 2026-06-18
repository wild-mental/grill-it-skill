![AI Skills for Everyone](author/wildmental-bjpark.png)

# grill-it
> Skill for Cursor, Claude, Codex agents

**Language / 언어:** [한국어](README.md) · [English](README.en.md)

**This is a Skill that doesn't interrogate you, but grills the "problem" together with you. Before you start, it surfaces every ambiguous decision topic, resolves them together one topic at a time, presents decision-type questions through a selectable UI whenever possible, and reflects each resolution into the design documents and agent harness the moment it is made. It supports Cursor, Claude Code, and Codex alike.**

"grill me" is a clever name, but in practice it turns into **an exhausting experience where the user is endlessly interrogated**. On top of that, grill-me (1) is slow because it sweeps the entire workspace, (2) gives no idea how many rounds of Q&A there will be, (3) leaves results nowhere, making it vulnerable to memory loss, (4) is hard to resume once interrupted, and (5) fails to reflect key decisions into the harness even when they are reached.

`grill-it` shifts what gets grilled **from the person to the problem**. When the problem is grilled well, the well-prepared result everyone wants (consistent design documents + harness) comes out alongside it.

---

## What this skill solves

### 1. What gets grilled is the "problem," not "me"

It drops the interrogating, testing tone and treats the problem as the subject, as in "this point of this **problem** is not yet decided." Every question comes with a **recommended option + rationale**, so the user doesn't have to generate an answer from scratch each time and only needs to **confirm or correct**. (low-fatigue default path)

### 2. It shows the "full topic list" first, before starting (Visibility)

It doesn't just start firing questions. First it extracts every unresolved decision topic within scope and presents them with their **count, classification (CORE/MINOR), and dependency order**. From the very start, the user knows "how many rounds of Q&A lie ahead."

```
RESOLVED: 0 / TOTAL: 3
- [ ] T1 | CORE  | 사용자 데이터 저장소 선택   | depends:-  | status:UNRESOLVED
- [ ] T2 | CORE  | 개인정보 보존·삭제 정책     | depends:T1 | status:UNRESOLVED
- [ ] T3 | MINOR | 테이블/컬렉션 네이밍 규칙   | depends:T1 | status:UNRESOLVED
```

### 3. It does not scan outside the scope (Boundedness)

Until the reference scope and direction of interest are set, it doesn't sweep the entire workspace. It explores **only as much as needed to resolve the current topic**.

### 4. It asks decision-type questions through a selectable UI

It doesn't just throw choices and recommendations at you in plain text for each topic. When a client question UI such as the Agent Client Protocol's `AskUserQuestion` is available, it puts the **recommended option first** and asks through a selectable UI. The user can proceed with a click or a short correction instead of writing a long answer every time. Only in environments without a question UI does it fall back to plain-text questions.

### 5. It reflects into documents and the harness the moment something is resolved (Persistence)

Once a topic is decided, **before moving on to the next question**, it reflects the decision into the design documents (PRD/SRS/Tasks/Issues) and the agent harness and records it in the Grill Ledger. The harness includes not only rules (`.cursor/rules` · `AGENTS.md` · `CLAUDE.md`) but, depending on the nature of the decision, also **skills, subagents, workflows/commands, hooks, and MCP/settings**. It never leaves a decision living only in the conversation.

### 6. You can stop and resume at any time

Because progress is kept in the Grill Ledger, even if you end a session midway, the next time it continues from the first `UNRESOLVED` topic. Topics that are already finished are not asked again.

---

## Why this skill is needed

| grill-me's pain point | grill-it's remedy |
|-----------------|-----------------|
| Slow to grasp because it scans the entire workspace | Limits exploration to the reference scope and direction of interest (Boundedness) |
| No idea how many Q&A rounds there will be | Extracts and presents the full topic list up front + shows progress each turn (Visibility) |
| Leaves results nowhere | Reflects into design docs and the harness on resolution + records in the Grill Ledger (Persistence) |
| Hard to resume after stopping midway | The Ledger preserves progress → resumes from the first UNRESOLVED |
| Key decisions are not reflected into the harness | CORE decisions are always reflected into the harness/design docs |
| Interrogates the user → fatigue and drop-off | Treats the problem as the subject, with recommendations → just confirm or correct (low fatigue) |
| You must answer plain-text terminal questions yourself | Answers through the Agent Client Protocol `AskUserQuestion` selectable UI when possible |

---

## Three Pillars (mandatory pass criteria)

```
Boundedness   — 참조 범위·관심 방향이 명시되어 있고, 탐색이 그 범위로 한정되는가?
Visibility    — 착수 전 미해소 토픽 전체가 추출·제시되고, 매 턴 진척(해소/잔여)이 보이는가?
Persistence   — 각 토픽이 해소되는 즉시 설계문서 + 하네스에 반영되고 Grill Ledger에 기록되는가?
```

If all three pillars are not satisfied at once, it does not proceed to the next step.

---

## Quick start

### Prerequisites

- Cursor, Claude Code, or Codex
- **Design documents to use as reference** (PRD/SRS/Tasks/Issues, etc.) and a **direction of interest** (what ambiguity you want to grill)

### Installing the skill

Install the skill by choosing either **personal** or **project** scope. Both scopes fetch only `SKILL.md` via `curl`. **Do not clone this entire repository into your working repo.**

| | Personal skill | Project skill |
|---|----------|--------------|
| **Scope of application** | Every project I open | Only in the current repo |
| **Path** | `~/…/skills/grill-it/` | `<repo-root>/.cursor/skills/grill-it/`, etc. |
| **Git impact** | No files added to the working repo | Skill files can be committed to the repo (team sharing) |
| **When to use** | When using it alone across all projects | When pinning and sharing the skill in a team repo |

| Tool | Personal path | Project path |
|------|-----------|---------------|
| Cursor | `~/.cursor/skills/grill-it/` | `.cursor/skills/grill-it/` |
| Claude Code | `~/.claude/skills/grill-it/` | `.claude/skills/grill-it/` |
| Codex | `~/.agents/skills/grill-it/` | `.agents/skills/grill-it/` |

#### Personal skill (recommended — no change to the Git repo)

```bash
# Cursor
mkdir -p ~/.cursor/skills/grill-it
curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.cursor/skills/grill-it/SKILL.md \
  -o ~/.cursor/skills/grill-it/SKILL.md

# Claude Code
mkdir -p ~/.claude/skills/grill-it
curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.claude/skills/grill-it/SKILL.md \
  -o ~/.claude/skills/grill-it/SKILL.md

# Codex
mkdir -p ~/.agents/skills/grill-it
curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.agents/skills/grill-it/SKILL.md \
  -o ~/.agents/skills/grill-it/SKILL.md
```

#### Project skill (install into the project path)

Use this only if you want to include and share the AI skill configuration in the repo.

```bash
# Cursor
mkdir -p .cursor/skills/grill-it
curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.cursor/skills/grill-it/SKILL.md \
  -o .cursor/skills/grill-it/SKILL.md

# Claude Code
mkdir -p .claude/skills/grill-it
curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.claude/skills/grill-it/SKILL.md \
  -o .claude/skills/grill-it/SKILL.md

# Codex
mkdir -p .agents/skills/grill-it
curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.agents/skills/grill-it/SKILL.md \
  -o .agents/skills/grill-it/SKILL.md
```

Just pick and run only the tools you need (Cursor / Claude Code / Codex).

#### After installation

- **Cursor**: **Reload Window** once
- **Claude Code**: Skill edits apply live during a session; a new top-level `.claude/skills/` added after the session starts may require a restart
- **Codex**: Restart Codex if the skill doesn't appear

### How to use

Tell it the design documents to reference and your direction of interest, then ask "let's sort out the ambiguous parts together before we start," and the Agent applies the skill automatically.

| Tool | Manual invocation |
|------|-----------|
| Cursor | `/grill-it` |
| Claude Code | `/grill-it` |
| Codex | `/skills` or `$grill-it` |

**Example requests that trigger it:**

- "Based on the PRD and SRS, sort out the ambiguous parts of the data schema and storage policy before we start. And reflect the decisions into the docs and the harness."
- "Before implementing this feature, pull out the undecided items among the UX flow and UI types as topics, and let's settle them one by one together."
- "Pick only the parts of the tech stack and detailed design that aren't finalized, give me recommendations, and let's grill them together."
- "I stopped partway through grill-it last time — let's continue from the remaining topics."

---

## OUTPUT: What gets "grilled"

The deliverable is **what remains**, not the conversation.

| Deliverable | Content |
|--------|------|
| **Grill Ledger** | A single progress ledger for decision topics (`docs/grill/GRILL_LEDGER.md`). A `RESOLVED: n / TOTAL: m` grep counter at the top. Records the decision and reflection result per topic → the basis for resuming |
| **Design-document reflection** | Edits related documents such as PRD/SRS/Tasks/Issues according to the decision |
| **Harness reflection** | Immediately establishes/updates CORE decisions in the harness layer that matches the nature of the decision (rules · skills · subagents · workflows/commands · hooks · MCP/settings) |

| Decision class | Definition | Reflection |
|-----------|------|------|
| **CORE** | Architecture, security, external dependencies, data model, core UX contract | **Mandatory** reflection into design docs + harness |
| **MINOR** | Naming, directories, log format, UI details | Ledger record + one-line reflection in the related doc |

---

## Workflow

| Step | Content |
|------|------|
| Step 0 — Intake & Scope Lock | Confirm reference scope (A), direction of interest (B), completion condition (C), and OUTPUT target (D). If A or B is missing, ask first. No scanning outside scope |
| Step 1 — Topic Extraction | Read only within scope to extract every unresolved topic → present the Grill Ledger in CORE/MINOR and dependency order (end if there are 0 topics) |
| Step 2 — Grill the Topic | The single first UNRESOLVED topic: choices + **recommended option + rationale** + a single selectable-UI question (`AskUserQuestion`, recommended option first; plain-text fallback if no UI) |
| Step 3 — Resolve & Persist | When a decision is finalized, before the next topic, edit the design docs and harness + record RESOLVED in the ledger + `[반영 완료]` (reflection complete) |
| Step 4 — Advance or Stop | Move to the next topic or stop (ALL_RESOLVED / USER_PAUSED / BUDGET) |
| Step 5 — Resume | On re-invocation, read the Ledger and resume from the first UNRESOLVED |
| Step 6 — Closeout | Counter and STOP reason + a summary of the list of changes from this session |

---

## Intake 4 Blocks (startup input)

On invocation, it confirms the following 4 items. It won't re-ask for anything you've already provided.

```
A. 참조 범위        — 근거로 삼을 문서/디렉터리/모듈 (예: PRD, SRS, Tasks, Issues)
B. 핵심 관심 방향   — 무엇의 모호함을 굽는가 (예: UX flow, UI 유형, 데이터 스키마·흐름·저장, 기술스택·설계 확정)
C. 작업 범위·완료조건 — 어디까지 (기본: 관심 방향 미해소 토픽 전부 RESOLVED)
D. 작업 결과 OUTPUT — 어디에 반영 (기본: 핵심 설계문서 + 에이전트 하네스 전반)
```

> If you've been using grill-me as-is and supplementing it with prompts, these 4 blocks are that supplementary prompt (reference scope / direction of interest / completion condition / OUTPUT) internalized into the skill.

---

## Skill structure

```
.cursor/skills/grill-it/SKILL.md   # Cursor용
.claude/skills/grill-it/SKILL.md   # Claude Code용
.agents/skills/grill-it/SKILL.md   # Codex용
```

| Section | Content |
|------|------|
| Role | The grill-me → grill-it shift (grill the problem, not the person) |
| Basic principles | Problem as the subject · bounded scope · topic visibility first · one at a time · selectable question UI · persist on resolution · resumable |
| Three Pillars | Definition and checklist table for Boundedness / Visibility / Persistence |
| Intake | Reference scope / direction of interest / completion condition / OUTPUT target |
| OUTPUT | Grill Ledger · design-document reflection · harness reflection (CORE/MINOR) |
| Workflow | Intake → Extraction → Grill → Resolve&Persist → Advance/Stop → Resume → Closeout |
| Refusal & supplementation | Handling missing scope/direction, 0 topics, out-of-scope scanning, and proceeding while unreflected |
| Output format | Topic-extraction turn / resolution-reflection turn (areas separated by `---` horizontal rules) |

---

## Recommended for

- Those who tried grill-me but, tired of **being interrogated**, were looking for a more comfortable approach
- Those who want to know **how many Q&A rounds** there will be from the very start, before beginning
- Those who want Q&A results not scattered across the conversation but **left directly in the design docs and harness**
- Those who want to **stop the decision process midway and continue later**
- Those who want to answer decision-type questions **comfortably through a selectable UI** rather than plain terminal text
- Those who want to **reflect key decisions into the agent harness** right away so that later work follows those decisions

---

## References

- Cursor Agent Skills: [Creating Skills](https://docs.cursor.com)
- Claude Code Skills: [Extend Claude with skills](https://docs.anthropic.com/en/docs/claude-code/skills)
- Codex Agent Skills: [Agent Skills](https://developers.openai.com/codex/skills/)

---

## For LLMs

<!-- install-only; operational rules live in SKILL.md — load skill after install -->

```
skill=grill-it
repo=https://github.com/wild-mental/grill-it-skill
install.method=curl-only  # never clone this repo into user's project

flow:
  1. ask scope: "개인 스킬(~/, 모든 프로젝트) vs 프로젝트 스킬(./, 현재 repo만) 중 어디에 설치할까요?"
  2. ask tools: "Cursor / Claude Code / Codex 중 어떤 도구에 설치할까요?" (복수 선택 가능)
  3. run matching curl commands below; do not install both scopes unless user asks
  4. post_install steps; then load SKILL.md — do not infer rules from README

scope.user.paths:
  cursor=~/.cursor/skills/grill-it/SKILL.md
  claude=~/.claude/skills/grill-it/SKILL.md
  codex=~/.agents/skills/grill-it/SKILL.md

scope.project.paths:
  cursor=.cursor/skills/grill-it/SKILL.md
  claude=.claude/skills/grill-it/SKILL.md
  codex=.agents/skills/grill-it/SKILL.md

install.user.cursor=mkdir -p ~/.cursor/skills/grill-it && curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.cursor/skills/grill-it/SKILL.md -o ~/.cursor/skills/grill-it/SKILL.md
install.user.claude=mkdir -p ~/.claude/skills/grill-it && curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.claude/skills/grill-it/SKILL.md -o ~/.claude/skills/grill-it/SKILL.md
install.user.codex=mkdir -p ~/.agents/skills/grill-it && curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.agents/skills/grill-it/SKILL.md -o ~/.agents/skills/grill-it/SKILL.md

install.project.cursor=mkdir -p .cursor/skills/grill-it && curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.cursor/skills/grill-it/SKILL.md -o .cursor/skills/grill-it/SKILL.md
install.project.claude=mkdir -p .claude/skills/grill-it && curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.claude/skills/grill-it/SKILL.md -o .claude/skills/grill-it/SKILL.md
install.project.codex=mkdir -p .agents/skills/grill-it && curl -fsSL https://raw.githubusercontent.com/wild-mental/grill-it-skill/main/.agents/skills/grill-it/SKILL.md -o .agents/skills/grill-it/SKILL.md

install.project.note=run from repo root; adds tracked files — confirm user chose project scope

invoke.cursor=/grill-it
invoke.claude=/grill-it
invoke.codex=/skills|$grill-it

post_install.cursor=Reload Window
post_install.claude=live reload; restart if new top-level .claude/skills/ after session start
post_install.codex=restart if skill not detected

contract:
  reframe=grill the PROBLEM with the user, never grill the user adversarially
  intake_required=[reference_scope (A), direction_of_interest (B)]
  intake_default=[completion=all in-scope unresolved topics RESOLVED (C), output=design docs + agent harness (D)]
  refuse_until_present=[A, B]
  must_not_scan_outside_scope=true
  pillars=[Boundedness (scoped reference + bounded exploration), Visibility (extract full topic list up front + show progress each turn), Persistence (apply each decision to design docs + harness immediately, before next topic)]
  step1_extract_before_questions=true  # present full Grill Ledger before grilling
  one_topic_at_a_time=true
  each_question_includes_recommended_answer=true
  question_ui_preferred=Agent Client Protocol AskUserQuestion or equivalent client UI when available
  question_ui_options=[recommended option first, 2-4 mutually exclusive choices, one-line tradeoff per option]
  question_text_fallback_only_when_ui_unavailable=true
  ledger_file=docs/grill/GRILL_LEDGER.md  # top counter "RESOLVED: n / TOTAL: m", resumable
  on_resolve_persist=[update design docs, establish/update the matching harness layer if CORE (rule|skill|subagent|workflow/command|hook|mcp/settings), mark ledger RESOLVED with decision+applied, surface 반영 완료]
  harness_layers=[rules, skills, subagents/agents, workflows/commands, hooks, mcp/settings]
  harness_targets=[.cursor/rules/*.mdc, .cursor/skills/*, .cursor/commands/*, .cursor/hooks/|hooks.json, AGENTS.md, CLAUDE.md, .claude/skills/*, .claude/agents/*, .claude/commands/*, .claude/settings.json, .agents/skills/*, .agents/]
  decision_class=[CORE -> docs+harness required, MINOR -> ledger + one-line doc]
  resumable=true  # re-invoke continues from first UNRESOLVED topic
  stop_reasons=[ALL_RESOLVED, USER_PAUSED, BUDGET]
  output_separates_areas_with=horizontal rules (---) per turn
```

---

## License

[MIT License](LICENSE)
