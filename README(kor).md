# Skillful Agent System Builder

> Sam(요구사항) → Jenny(설계) → Will(검토)의 3단계 협업을 통해 맞춤형 Agent+Skill 시스템을 설계하는 시스템

## 이 프로젝트는 무엇인가요?

Skillful Agent System Builder는 복잡한 프롬프트를 처음부터 작성하지 않고도 **맞춤형 AI 에이전트 팀**을 만들 수 있도록 도와줍니다.

**해결하려는 문제**: 멀티 에이전트 시스템을 구축하려면 상세한 프롬프트를 설계하고, 에이전트 역할을 정의하며, 품질을 보장해야 합니다. 이는 시간이 많이 걸리고 오류가 발생하기 쉽습니다.

**솔루션**: 전문 AI 에이전트들(Sam, Jenny, Will)이 대화를 통해 프로덕션 수준의 에이전트 시스템을 설계하도록 돕는 3단계 협업 시스템입니다.

## 시작하기 (5분 소요)

### 옵션 1: Antigravity
1. Antigravity IDE에서 이 저장소를 엽니다.
2. Antigravity 에이전트가 활성화되어 있는지 확인합니다.
3. 다음과 같이 말하세요: "[사용 사례]를 위한 에이전트 시스템을 만들어줘"
4. Sam의 질문에 답변하고, Jenny의 설계를 검토한 뒤, Will의 품질 확인을 받으세요.

### 옵션 2: Claude Code
1. 이 저장소를 클론(Clone)합니다.
2. Claude Code에서 엽니다.
3. 다음과 같이 말하세요: "[사용 사례]를 위한 에이전트 시스템을 만들어줘"
4. Sam의 질문에 답변하고, Jenny의 설계를 검토한 뒤, Will의 품질 확인을 받으세요.

완성된 맞춤형 에이전트 시스템은 `./outputs/` 디렉토리에서 확인할 수 있습니다.

## 용어 설명

- **Orchestrator (오케스트레이터)**: 사용자와 전문 에이전트 간의 워크플로우를 지휘하는 중앙 관리자입니다.
- **Subagent (서브 에이전트)**: 프로세스의 특정 부분(요구사항, 설계, 검토)을 담당하는 전문 AI 에이전트(Sam, Jenny, Will)입니다.
- **Skill (스킬)**: 에이전트가 작업을 수행하는 데 사용하는 특정 기능이나 지식 베이스입니다.
- **Start Prompt (시작 프롬프트)**: 맞춤형 에이전트 팀을 실행하기 위한 전체 지침이 포함된 최종 결과물 파일입니다.

## 빠른 시작

### 준비 사항

1. **컨텍스트 파일**: 다음 파일들이 `./context/` 디렉토리에 있는지 확인하세요 (이 저장소에 포함되어 있음):
   - `anthropic-skills-guide.md`
   - `anthropic-subagents-guide.md`
   - `example-skills-mcp-builder-SKILL.md`
   - `example-skills-skill-creator-SKILL.md`

2. **Claude Code**: Claude Code가 설치되어 있고 `.agents/` 디렉토리에 접근할 수 있는지 확인하세요.

### 파일 구조

```
skillful-agent-system-builder/
├── README.md                            # 메인 시스템 문서
├── AGENTS.md                            # 오케스트레이터 & 에이전트 가이드
├── .agents/                              # 에이전트 정의
│   ├── skillful-orchestrator.md         # 메인 오케스트레이터
│   ├── sam-analyst.md                   # 요구사항 분석가
│   ├── jenny-engineer.md                # 설계 엔지니어
│   ├── will-reviewer.md                 # 품질 검토자
│   └── skills/                          # 스킬 정의
│       ├── agent-design/
│       │   └── SKILL.md
│       ├── skill-design/
│       │   └── SKILL.md
│       ├── quality-checklist/
│       │   └── SKILL.md
│       └── anthropic-reference/
│           └── SKILL.md
├── context/                             # 상시 활용 가능한 프로젝트 공통 자료
├── inputs/                              # 해당 요청에만 한정적으로 활용할 현행 자료
├── temp/                                # 임시 세션 파일
│   └── skillful-session/
└── outputs/                             # 결과물 산출 경로
```

## 사용법

### 기본 명령어

1. **표준 요청**: 질문이 포함된 전체 3단계 프로세스
   ```
   "교육자를 위한 에이전트 시스템을 만들어줘"
   ```

2. **빠른 모드**: 질문을 건너뛰고 합리적인 가정을 사용
   ```
   "빨리 진행해줘. 마케터를 위한 콘텐츠 생성 시스템을 설계해줘"
   ```

3. **단계별 진입**:
   - `"Start from Sam"` - 요구사항 수집부터 시작
   - `"Go to Jenny stage"` - 상세 설계부터 시작 (sam-draft.md 필요)
   - `"Will review"` - 검토부터 시작 (jenny-draft.md 필요)

4. **수정 요청**:
   ```
   "Sam, 에이전트가 더 필요한 것 같아"
   "Jenny, 스킬 예시를 더 자세히 써줘"
   "Will, 다시 검토해줘"
   ```

### 워크플로우

```
사용자 요청
    ↓
[Orchestrator] 세션 초기화 → temp/ 정리 → session.json 생성
    ↓
[Orchestrator] 명령어 인식 → 진입점 결정
    ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1단계: 요구사항 (Sam)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ↓
[sam-analyst] 요구사항 수집
    ├─ 빠른 모드? 질문 건너뜀
    └─ 일반 모드? 2-3가지 질문
    ↓
출력: ./temp/skillful-session/sam-draft.md
    ↓
[Orchestrator] 사용자 확인 (빠른 모드가 아닌 경우)
    ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
2단계: 설계 (Jenny)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ↓
[jenny-engineer] 상세 설계 작성
    ├─ 에이전트 설계 (frontmatter + 시스템 프롬프트)
    ├─ 스킬 설계 (SKILL.md 구조)
    └─ 필요 시 ./context/ 참조
    ↓
출력: ./temp/skillful-session/jenny-draft.md
    ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
3단계: 검토 (Will)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ↓
[will-reviewer] 품질 검토
    ├─ 품질 체크리스트 적용
    ├─ ./context/ 문서와 대조 확인
    └─ 문제 자동 수정
    ↓
출력: ./outputs/{system-name}-start-prompt.md
    ↓
🎉 완료!
```

## 시스템 구성 요소

### 에이전트

| 에이전트 | 역할 | 주요 책임 |
|-------|------|---------------------|
| **skillful-orchestrator** | 조정자 | 세션 관리, 명령어 인식, 에이전트 위임 |
| **sam-analyst** | 요구사항 분석가 | 사용자 인터뷰, 요구사항 수집, 초안 작성 |
| **jenny-engineer** | 설계 엔지니어 | 에이전트/스킬 상세 설계, 아키텍처 결정 |
| **will-reviewer** | 품질 검토자 | 품질 보증, 검증, 최종 결과물 생성 |

### 스킬

| 스킬 | 목적 | 사용자 |
|-------|---------|---------|
| **agent-design** | 에이전트 frontmatter 및 시스템 프롬프트 가이드라인 | Jenny, Will |
| **skill-design** | 스킬 SKILL.md 및 번들 리소스 가이드라인 | Jenny, Will |
| **quality-checklist** | 종합 품질 검증 체크리스트 | Will |
| **anthropic-reference** | ./context/ 문서에 대한 빠른 참조 | Jenny, Will |

## 출력

시스템은 `./outputs/`에 다음을 포함하는 완전한 시작 프롬프트 파일을 생성합니다:

1. 시스템 개요
2. 파일 구조
3. 완성된 에이전트 정의 (.agents/로 복사 가능)
4. 완성된 스킬 정의 (.agents/skills/로 복사 가능)
5. 워크플로우 문서
6. 사용 지침
7. 예제 시나리오
8. 설계 결정 사항
9. 품질 검증 체크리스트

## 설계 원칙

1. **3단계 협업**: 관심사의 분리 (요구사항 → 설계 → 검토)
2. **점진적 공개**: 스킬 3단계 로딩 (메타데이터 → 지침 → 리소스)
3. **최소 권한**: 에이전트는 필요한 도구만 가짐
4. **세션 격리**: 매 요청마다 temp/ 정리
5. **오케스트레이터 패턴**: 모든 통신은 오케스트레이터를 통함
6. **품질 우선**: 포괄적인 체크리스트 검증

## 커스터마이징

### 새 에이전트 추가

`.agents/`에 적절한 frontmatter와 시스템 프롬프트를 포함한 새 `.md` 파일을 생성하세요.

### 새 스킬 추가

1. 디렉토리 생성: `.agents/skills/{skill-name}/`
2. frontmatter와 내용을 포함한 `SKILL.md` 생성
3. 선택 사항 추가: `references/`, `scripts/`, `assets/`

### 워크플로우 수정

[skillful-orchestrator.md](.agents/skillful-orchestrator.md)에서 오케스트레이터의 워크플로우 단계를 편집하세요.

## 문제 해결

### 세션 문제
- **문제**: 이전 세션 데이터가 간섭함
- **해결**: 오케스트레이터가 매 새 요청마다 `./temp/skillful-session/`을 자동으로 정리합니다.

### 컨텍스트 파일 누락
- **문제**: Jenny/Will이 Anthropic 문서와 대조하여 검증할 수 없음
- **해결**: 4개의 파일이 모두 `./context/` 디렉토리에 있는지 확인하세요.

### 권한 오류
- **문제**: 에이전트가 파일을 쓸 수 없음
- **해결**: frontmatter에서 `permissionMode: acceptEdits`를 확인하세요.

## 예시

### 예시 1: 교육 시스템
```
User: "교육자를 위한 에이전트 시스템을 만들어줘"

Output: ./outputs/educator-system-start-prompt.md
- 4 Agents: orchestrator, lesson-planner, exam-creator, parent-communicator
- 3 Skills: education-curriculum, assessment-design, communication-templates
```

### 예시 2: 코드 리뷰 시스템
```
User: "빨리 진행해줘. 코드 리뷰 자동화 시스템이 필요해"

Output: ./outputs/code-review-automation-start-prompt.md
- 3 Agents: orchestrator, code-analyzer, security-checker
- 2 Skills: code-quality-rules, security-best-practices
```

## 기여하기

이 시스템을 개선하려면:

1. `.agents/skills/`에 더 많은 예제 스킬 추가
2. `quality-checklist/SKILL.md`의 품질 체크리스트 강화
3. `./context/`에 더 많은 컨텍스트 문서 추가
4. 오케스트레이터의 에러 처리 개선

## 참고 자료

- [Anthropic Skills Guide](./context/anthropic-skills-guide.md)
- [Anthropic Subagents Guide](./context/anthropic-subagents-guide.md)
- [Example: MCP Builder](./context/example-skills-mcp-builder-SKILL.md)
- [Example: Skill Creator](./context/example-skills-skill-creator-SKILL.md)

---

**Version**: 1.0
**Created**: 2026-01-17
**Status**: ✅ Production Ready
**author**: [Yz](mailto:houarnu166@gmail.com)
**contributor**: 해당 시스템은 [필로소피 AI](https://www.youtube.com/@%ED%95%84%EB%A1%9C%EC%86%8C%ED%94%BC)의 유튜브 영상과 함께 제공된 프롬프트와 시스템에 영향을 받아 제작됐습니다.    This system was developed based on the prompts and system provided alongside the YouTube video from [Philosophy AI](https://www.youtube.com/@%ED%95%84%EB%A1%9C%EC%86%8C%ED%94%BC).
