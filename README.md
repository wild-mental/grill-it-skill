![AI Skills for Everyone](author/wildmental-bjpark.png)

# grill-it
> Skill for Cursor, Claude, Codex agents

**나를 추궁하는 게 아니라, 나와 함께 "문제"를 굽는 Skill입니다. 착수 전에 모호한 결정 토픽을 전부 끄집어내 한 토픽씩 함께 해소하고, 해소되는 즉시 설계문서와 에이전트 하네스에 반영합니다. Cursor, Claude Code, Codex 모두 지원합니다.**

"grill me(나를 구워줘)"는 재치 있는 이름이지만, 실제로 해보면 **사용자가 끝없이 추궁당하는 피로한 경험**이 됩니다. 게다가 grill-me는 (1) 전체 워크스페이스를 훑어 느리고, (2) 몇 번 문답할지 알 수 없고, (3) 결과를 어디에도 남기지 않아 메모리 손실에 취약하며, (4) 중간에 끊기면 재개가 어렵고, (5) 핵심 결정이 나와도 하네스에 반영되지 않습니다.

`grill-it`은 굽는 대상을 **사람에서 문제로** 옮깁니다. 문제가 잘 구워지면, 모두가 원하는 잘 차려진 결과물(정합된 설계문서 + 하네스)이 함께 나옵니다.

---

## 이 스킬이 해결하는 것

### 1. 굽는 대상은 "나"가 아니라 "문제"다

추궁·시험 톤을 버리고, "이 **문제**의 이 지점이 아직 안 정해졌다"처럼 문제를 주어로 다룹니다. 모든 질문에는 **권장안 + 근거**가 함께 붙어, 사용자는 매번 답을 새로 생성하지 않고 **확인·교정**만 하면 됩니다. (저피로 기본 경로)

### 2. 착수 전 "토픽 전체"를 먼저 보여준다 (Visibility)

무작정 질문을 시작하지 않습니다. 먼저 범위 안의 미해소 결정 토픽을 전부 추출해 **개수·분류(CORE/MINOR)·의존 순서**와 함께 제시합니다. 사용자는 처음부터 "앞으로 몇 번 문답하는지"를 압니다.

```
RESOLVED: 0 / TOTAL: 3
- [ ] T1 | CORE  | 사용자 데이터 저장소 선택   | depends:-  | status:UNRESOLVED
- [ ] T2 | CORE  | 개인정보 보존·삭제 정책     | depends:T1 | status:UNRESOLVED
- [ ] T3 | MINOR | 테이블/컬렉션 네이밍 규칙   | depends:T1 | status:UNRESOLVED
```

### 3. 범위 밖을 스캔하지 않는다 (Boundedness)

참조 범위와 관심 방향이 정해지기 전에는 전체 워크스페이스를 훑지 않습니다. 탐색은 **현재 토픽을 해소하는 데 필요한 만큼만** 합니다.

### 4. 해소되는 즉시 문서·하네스에 반영한다 (Persistence)

한 토픽이 결정되면 **다음 질문으로 넘어가기 전에** 설계문서(PRD/SRS/Tasks/Issues)와 에이전트 하네스에 반영하고 Grill Ledger에 기록합니다. 하네스는 규칙(`.cursor/rules` · `AGENTS.md` · `CLAUDE.md`)뿐 아니라 결정 성격에 따라 **skill · subagent · workflow/command · hook · MCP/설정**까지 포함합니다. 결정을 대화 속에만 두지 않습니다.

### 5. 언제든 멈추고 재개할 수 있다

진척이 Grill Ledger에 남으므로, 세션을 중간에 끝내도 다음에 첫 `UNRESOLVED` 토픽부터 이어갑니다. 이미 끝난 토픽은 다시 묻지 않습니다.

---

## 왜 skill 이 필요한가

| grill-me의 불편 | grill-it의 처방 |
|-----------------|-----------------|
| 전체 워크스페이스 스캔으로 파악이 느림 | 참조 범위·관심 방향으로 탐색을 한정 (Boundedness) |
| 몇 번 문답할지 알 수 없음 | 착수 전 토픽 전체를 추출·제시 + 매 턴 진척 노출 (Visibility) |
| 결과를 어디에도 남기지 않음 | 해소 즉시 설계문서·하네스 반영 + Grill Ledger 기록 (Persistence) |
| 중간 종료 시 재개 어려움 | Ledger가 진척을 보존 → 첫 UNRESOLVED부터 재개 |
| 핵심 결정이 하네스에 반영 안 됨 | CORE 결정은 하네스/설계문서에 반드시 반영 |
| 사용자를 추궁 → 피로·이탈 | 문제를 주어로, 권장안 동반 → 확인·교정만 (저피로) |

---

## Three Pillars (필수 통과 기준)

```
Boundedness   — 참조 범위·관심 방향이 명시되어 있고, 탐색이 그 범위로 한정되는가?
Visibility    — 착수 전 미해소 토픽 전체가 추출·제시되고, 매 턴 진척(해소/잔여)이 보이는가?
Persistence   — 각 토픽이 해소되는 즉시 설계문서 + 하네스에 반영되고 Grill Ledger에 기록되는가?
```

세 기둥을 동시에 만족하지 못하면 다음 단계로 진행하지 않습니다.

---

## 빠른 시작

### 사전 요구사항

- Cursor, Claude Code, 또는 Codex
- **참조로 삼을 설계문서**(PRD/SRS/Tasks/Issues 등)와 **관심 방향**(무엇의 모호함을 굽고 싶은지)

### 스킬 설치

스킬은 **개인** 또는 **프로젝트** 범위 중 하나를 골라 설치합니다. 두 범위 모두 `curl`로 `SKILL.md`만 받습니다. **이 저장소 전체를 작업 repo에 clone하지 마세요.**

| | 개인 스킬 | 프로젝트 스킬 |
|---|----------|--------------|
| **적용 범위** | 내가 여는 모든 프로젝트 | 현재 repo에서만 |
| **경로** | `~/…/skills/grill-it/` | `<repo-root>/.cursor/skills/grill-it/` 등 |
| **Git 영향** | 작업 repo에 파일 추가 없음 | repo에 스킬 파일 commit 가능 (팀 공유) |
| **언제 쓰나** | 혼자 모든 프로젝트에서 쓸 때 | 팀 repo에 스킬을 고정·공유할 때 |

| 도구 | 개인 경로 | 프로젝트 경로 |
|------|-----------|---------------|
| Cursor | `~/.cursor/skills/grill-it/` | `.cursor/skills/grill-it/` |
| Claude Code | `~/.claude/skills/grill-it/` | `.claude/skills/grill-it/` |
| Codex | `~/.agents/skills/grill-it/` | `.agents/skills/grill-it/` |

#### 개인 스킬 (권장 — Git repo 무변경)

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

#### 프로젝트 스킬 (프로젝트 경로에 설치)

AI 스킬 설정을 repo에 포함·공유하려는 경우에만 사용하세요.

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

필요한 도구(Cursor / Claude Code / Codex)만 골라 실행하면 됩니다.

#### 설치 후

- **Cursor**: **Reload Window** 한 번
- **Claude Code**: 스킬 수정은 세션 중 live 반영; 세션 시작 후 새 top-level `.claude/skills/`는 재시작 필요할 수 있음
- **Codex**: 스킬이 안 보이면 Codex 재시작

### 사용 방법

참조할 설계문서와 관심 방향을 알려주고 "착수 전에 모호한 부분 함께 정리하자"라고 요청하면 Agent가 스킬을 자동으로 적용합니다.

| 도구 | 수동 호출 |
|------|-----------|
| Cursor | `/grill-it` |
| Claude Code | `/grill-it` |
| Codex | `/skills` 또는 `$grill-it` |

**적용되는 요청 예시:**

- "PRD·SRS 기준으로 착수 전에 데이터 스키마·저장 방침 모호한 부분 정리해줘. 결정은 문서랑 하네스에 반영하고"
- "이 기능 구현 전에 UX flow·UI 유형 중 안 정해진 것들 토픽으로 뽑아서 하나씩 같이 정하자"
- "기술스택·세부 설계 확정 안 된 부분만 골라서 권장안 주면서 같이 굽자"
- "지난번 grill-it 하다 멈췄는데 남은 토픽부터 이어서 하자"

---

## OUTPUT: 무엇이 "구워지는가"

대화가 아니라 **남는 것**이 산출물입니다.

| 산출물 | 내용 |
|--------|------|
| **Grill Ledger** | 결정 토픽의 단일 진척 원장 (`docs/grill/GRILL_LEDGER.md`). 상단에 `RESOLVED: n / TOTAL: m` grep 카운터. 토픽별 결정·반영 결과 기록 → 재개의 근거 |
| **설계문서 반영** | PRD/SRS/Tasks/Issues 등 관련 문서를 결정대로 수정 |
| **하네스 반영** | 결정 성격에 맞는 하네스 계층(rules · skills · subagents · workflows/commands · hooks · MCP/settings)에 CORE 결정을 즉시 수립·갱신 |

| 결정 분류 | 정의 | 반영 |
|-----------|------|------|
| **CORE** | 아키텍처·보안·외부 의존·데이터 모델·핵심 UX 계약 | 설계문서 + 하네스 **필수** 반영 |
| **MINOR** | 네이밍·디렉터리·로그 포맷·UI 디테일 | 원장 기록 + 관련 문서 한 줄 반영 |

---

## Workflow

| 단계 | 내용 |
|------|------|
| Step 0 — Intake & Scope Lock | 참조 범위(A)·관심 방향(B)·완료 조건(C)·OUTPUT 대상(D) 확인. A·B 없으면 먼저 질문. 범위 밖 스캔 금지 |
| Step 1 — Topic Extraction | 범위 안만 읽어 미해소 토픽 전부 추출 → CORE/MINOR·의존 순서로 Grill Ledger 제시 (토픽 0개면 종료) |
| Step 2 — Grill the Topic | 첫 UNRESOLVED 토픽 1개: 선택지 + **권장안+근거** + 단일 질문 |
| Step 3 — Resolve & Persist | 결정 확정 시 다음 토픽 전에 설계문서·하네스 수정 + 원장 RESOLVED 기록 + `[반영 완료]` |
| Step 4 — Advance or Stop | 다음 토픽으로 이동 또는 종료(ALL_RESOLVED / USER_PAUSED / BUDGET) |
| Step 5 — Resume | 재호출 시 Ledger 읽어 첫 UNRESOLVED부터 재개 |
| Step 6 — Closeout | 카운터·STOP 사유 + 이번 세션 변경 목록 요약 |

---

## Intake 4블록 (착수 입력)

호출 시 아래 4가지를 확인합니다. 이미 준 것은 다시 묻지 않습니다.

```
A. 참조 범위        — 근거로 삼을 문서/디렉터리/모듈 (예: PRD, SRS, Tasks, Issues)
B. 핵심 관심 방향   — 무엇의 모호함을 굽는가 (예: UX flow, UI 유형, 데이터 스키마·흐름·저장, 기술스택·설계 확정)
C. 작업 범위·완료조건 — 어디까지 (기본: 관심 방향 미해소 토픽 전부 RESOLVED)
D. 작업 결과 OUTPUT — 어디에 반영 (기본: 핵심 설계문서 + 에이전트 하네스 전반)
```

> grill-me를 그대로 두고 프롬프트로 보완해서 쓰던 분이라면, 위 4블록이 그 보완 프롬프트(참조 범위 / 관심 방향 / 완료 조건 / OUTPUT)를 스킬 안으로 내재화한 것입니다.

---

## 스킬 구성

```
.cursor/skills/grill-it/SKILL.md   # Cursor용
.claude/skills/grill-it/SKILL.md   # Claude Code용
.agents/skills/grill-it/SKILL.md   # Codex용
```

| 섹션 | 내용 |
|------|------|
| 역할 | grill-me → grill-it 전환 (사람이 아니라 문제를 굽는다) |
| 기본 원칙 | 문제를 주어로 · 범위 한정 · 토픽 선(先)가시화 · 한 번에 하나 · 해소 즉시 영속화 · 재개 가능 |
| Three Pillars | Boundedness / Visibility / Persistence 정의·점검 표 |
| Intake | 참조 범위 / 관심 방향 / 완료 조건 / OUTPUT 대상 |
| OUTPUT | Grill Ledger · 설계문서 반영 · 하네스 반영 (CORE/MINOR) |
| Workflow | Intake → Extraction → Grill → Resolve&Persist → Advance/Stop → Resume → Closeout |
| Refusal & 보충 | 범위·방향 누락, 토픽 0개, 범위 밖 스캔, 미반영 상태 진행 처리 |
| 출력 형식 | 토픽 추출 턴 / 해소·반영 턴 (`---` 가로줄로 영역 분리) |

---

## 이런 분께 추천합니다

- grill-me를 써봤지만 **추궁당하는 피로감**에 더 편한 방법을 찾던 분
- 착수 전에 **몇 번 문답해야 하는지** 처음부터 알고 싶은 분
- 문답 결과가 대화에 흩어지지 않고 **설계문서·하네스에 바로 남기를** 원하는 분
- 결정 과정을 **중간에 멈췄다가 다음에 이어서** 하고 싶은 분
- 핵심 의사결정을 곧바로 **에이전트 하네스에 반영**해 이후 작업이 그 결정을 따르게 하려는 분

---

## 참고

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

## 라이선스

[MIT License](LICENSE)
