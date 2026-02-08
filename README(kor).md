# Skillful Agent System Builder

> **"코드 리뷰 시스템 만들어줘"** 라고 말하면, AI가 대화를 통해 맞춤형 에이전트 팀을 설계해줍니다.

[🇺🇸 English Version](./README.md)

## 이런 분들을 위한 프로젝트입니다

- ✅ Claude Code나 Antigravity에서 **멀티 에이전트 시스템**을 만들고 싶은데, 프롬프트 작성이 막막한 분
- ✅ 반복되는 업무를 자동화할 **커스텀 AI 도우미**가 필요한 분
- ✅ 에이전트/스킬 설계의 **베스트 프랙티스**를 빠르게 적용하고 싶은 분

## 30초 만에 이해하기

**입력** (당신이 하는 것):
```
"교육자들이 특정 주제에 대해 교육자료를 만드는 것을 도와주는 에이전트 시스템이 필요해"
```

**출력** (시스템이 만드는 것):
```
./outputs/lesson-material-generator-start-prompt.md
./outputs/lesson-material-generator/
    ├── .agents/
    │   ├── lesson-planner.md
    │   ├── content-writer.md
    │   └── skills/...
    └── AGENTS.md
```

**결과**: 바로 사용 가능한 에이전트 팀 설계서 + 프로젝트 파일

---

## 빠른 시작

### 필수 환경
- **Claude Code** 또는 **Antigravity IDE** (AI 에이전트 실행 환경)

### 실행 방법
1. 이 저장소를 클론하거나 다운로드
2. Claude Code 또는 Antigravity에서 열기
3. 다음과 같이 말하기:
   ```
   "... 을 위한 에이전트 시스템을 만들어줘"
   ```
4. AI의 질문에 답하면 끝!

결과물은 `./outputs/` 폴더에 생성됩니다.

---

## 작동 방식

4명의 전문 AI 에이전트가 순서대로 협업합니다:

| 순서 | 에이전트 | 역할 |
|:---:|---------|------|
| 1️⃣ | **Sam** | 요구사항을 파악하고 질문합니다 |
| 2️⃣ | **Jenny** | 에이전트와 스킬을 상세 설계합니다 |
| 3️⃣ | **Will** | 품질을 검증하고 문제를 수정합니다 |
| 4️⃣ | **Tom** | 최종 문서와 프로젝트 파일을 생성합니다 |

```
당신의 요청 → Sam → Jenny → Will → Tom → 완성된 에이전트 시스템
```

### 두 가지 모드

| 모드 | 사용법 | 설명 |
|------|--------|------|
| **일반 모드** | `"... 만들어줘"` | 에이전트가 질문하며 신중하게 설계 |
| **빠른 모드** | `"빠르게. ... 만들어줘"` | 질문 없이 즉시 결과물 생성 |

---

## 사용 예시

### 예시 1: 고객 지원 시스템 (일반 모드)
```
입력: "고객 문의를 분류하고 FAQ로 자동 답변하되, 복잡한 건은 담당자에게 전달하는 시스템이 필요해"

출력:
- 3개 에이전트: inquiry-classifier, faq-responder, escalation-handler
- 2개 스킬: faq-knowledge-base, ticket-routing-rules
```

### 예시 2: 코드 리뷰 시스템 (빠른 모드)
```
입력: "PR 코드를 분석해서 버그 가능성과 보안 취약점을 찾아주는 시스템이 필요해. 빠르게 만들어줘."

출력:
- 3개 에이전트: code-analyzer, bug-detector, security-checker
- 2개 스킬: code-quality-rules, security-best-practices
```

---

## 결과물

시스템이 생성하는 **Start Prompt** 파일에는 다음이 포함됩니다:

1. 시스템 개요와 목적
2. 파일 구조 (`.agents/` 기반)
3. 모든 에이전트 정의 (바로 복사해서 사용 가능)
4. 모든 스킬 정의
5. 워크플로우 설명
6. 사용 예시

**사용법**: 생성된 Start Prompt를 새 프로젝트 폴더에 복사하고, Claude Code나 Antigravity에 입력하면 에이전트 시스템이 자동으로 생성됩니다.

---

## 프로젝트 구조

```
skillful-agent-system-builder/
├── .agents/                  # 이 시스템의 에이전트들
│   ├── sam-analyst.md        # 요구사항 분석
│   ├── jenny-engineer.md     # 설계
│   ├── will-reviewer.md      # 품질 검토
│   └── tom-builder.md        # 출력 생성
├── context/                  # 참조 문서 (Anthropic 가이드 등)
├── outputs/                  # 결과물 저장 위치
└── temp/                     # 작업 중 임시 파일
```

---

## 용어 정리

| 용어 | 설명 |
|------|------|
| **Agent (에이전트)** | 특정 역할을 수행하는 AI. 예: 코드 분석가, 문서 작성자 |
| **Skill (스킬)** | 에이전트가 사용하는 지식/도구 모음. 예: 코딩 규칙, 문서 템플릿 |
| **Start Prompt** | 에이전트 팀을 만들기 위한 완전한 설계서 |
| **Orchestrator** | 여러 에이전트를 조율하는 중앙 관리자 |

---

## 커스터마이징

### 새 에이전트 추가
`.agents/` 폴더에 새 `.md` 파일 생성

### 새 스킬 추가
`.agents/skills/{skill-name}/SKILL.md` 파일 생성

### 워크플로우 수정
[skillful-orchestrator.md](.agents/skillful-orchestrator.md) 편집

---

## 문제 해결

| 문제 | 해결 방법 |
|------|----------|
| 이전 세션 데이터가 방해됨 | 자동으로 `./temp/` 폴더가 초기화됩니다 |
| 컨텍스트 파일 누락 | `./context/` 폴더에 필수 파일이 있는지 확인 |
| 파일 쓰기 권한 오류 | 에이전트 frontmatter에 `permissionMode: acceptEdits` 확인 |

---

## 참고 자료

- [Anthropic Skills Guide](./context/anthropic-skills-guide.md)
- [Anthropic Subagents Guide](./context/anthropic-subagents-guide.md)

---

**Version**: 1.1 | **Created**: 2026-01-28 | **Status**: ✅ Production Ready

**Author**: [Yz](mailto:houarnu166@gmail.com)

## 업데이트 로그

### v1.1 (2026-01-28)
- **신규 에이전트**: `tom-builder` 추가 (출력 전문가)
- **역할 조정**: Will은 품질 검증 전담, Tom이 Start Prompt 생성 및 프로젝트 구축 담당
- **신규 스킬**: `verification-before-completion`, `concise-planning`, `context-window-management` 등 추가
- **Fast Mode 개선**: 사용자 질문 없이 자동으로 프로젝트 구축까지 진행

### v1.0 (2026-01-17)
- 초기 릴리즈

## 출처 및 저작권 정보

| 출처 | 설명 |
|------|------|
| [필로소피 AI](https://www.youtube.com/@%ED%95%84%EB%A1%9C%EC%86%8C%ED%94%BC) | 유튜브 영상과 함께 제공된 프롬프트, 시스템 기초 디자인 |
| [anthropics/skills](https://github.com/anthropics/skills) | Anthropic 공식 스킬 저장소 |
| [antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills) | 에이전트가 사용하는 스킬 모음 |
