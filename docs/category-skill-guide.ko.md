# Category & Skill 시스템 가이드

이 문서는 Oh-My-OpenCode의 확장성 핵심인 **Category**와 **Skill** 시스템에 대한 종합 가이드를 제공함.

## 1. 개요

모든 작업을 단일 AI 에이전트에게 위임하는 대신, 작업의 성격에 맞춰진 **전문가**를 호출하는 것이 훨씬 더 효율적임.

- **Category**: "이것은 어떤 종류의 작업인가?" (모델, temperature, 프롬프트 마인드셋을 결정)
- **Skill**: "어떤 도구와 지식이 필요한가?" (전문 지식, MCP 도구, 워크플로우를 주입)

이 두 개념을 결합하면 `sisyphus_task`를 통해 최적의 에이전트를 생성할 수 있음.

---

## 2. Category 시스템

Category는 특정 도메인에 최적화된 에이전트 설정 프리셋임.

### 사용 가능한 내장 Category

| Category | 최적 모델 | 특징 | 사용 사례 |
|----------|---------------|-----------------|-----------|
| `visual-engineering` | `gemini-3-pro` | 높은 창의성 (Temp 0.7) | Frontend, UI/UX, 애니메이션, 스타일링 |
| `ultrabrain` | `gpt-5.2` | 최대 논리적 추론 (Temp 0.1) | 아키텍처 설계, 복잡한 비즈니스 로직, 디버깅 |
| `artistry` | `gemini-3-pro` | 예술적 (Temp 0.9) | 창의적 아이디어 발상, 디자인 컨셉, 스토리텔링 |
| `quick` | `claude-haiku` | 빠름 (Temp 0.3) | 간단한 작업, 리팩토링, 스크립트 작성 |
| `writing` | `gemini-3-flash` | 자연스러운 흐름 (Temp 0.5) | 문서화, 기술 블로그, README 작성 |
| `most-capable` | `claude-opus` | 고성능 (Temp 0.1) | 극도로 어려운 복잡한 작업 |

### 사용법

`sisyphus_task` 도구를 호출할 때 `category` 파라미터를 지정함.

```typescript
sisyphus_task(
  category="visual-engineering",
  prompt="대시보드 페이지에 반응형 차트 컴포넌트 추가"
)
```

### Sisyphus-Junior (위임된 실행자)

Category를 사용하면 **Sisyphus-Junior**라는 특별한 에이전트가 작업을 수행함.
- **특징**: 다른 에이전트에게 작업을 **재위임할 수 없음**.
- **목적**: 무한 위임 루프를 방지하고 할당된 작업에 집중하도록 보장함.

---

## 3. Skill 시스템

Skill은 특정 도메인에 대한 **전문 지식(Context)**과 **도구(MCP)**를 에이전트에 주입하는 메커니즘임.

### 내장 Skill

1. **`git-master`**
   - **기능**: Git 전문가. 커밋 스타일 감지, 원자적 커밋 분할, rebase 전략 수립.
   - **MCP**: 없음 (Git 명령어 사용)
   - **용도**: 커밋, 히스토리 검색, 브랜치 관리에 필수.

2. **`playwright`**
   - **기능**: 브라우저 자동화. 웹 페이지 테스팅, 스크린샷, 스크래핑.
   - **MCP**: `@playwright/mcp` (자동 실행)
   - **용도**: 구현 후 UI 검증, E2E 테스트 작성용.

3. **`frontend-ui-ux`**
   - **기능**: 디자이너 마인드셋 주입. 색상, 타이포그래피, 모션 가이드라인.
   - **용도**: 단순 구현을 넘어선 심미적 UI 작업용.

### 사용법

원하는 skill 이름을 `skills` 배열에 추가함.

```typescript
sisyphus_task(
  category="quick",
  skills=["git-master"],
  prompt="현재 변경사항 커밋. 커밋 메시지 스타일 준수."
)
```

### Skill 커스터마이징 (SKILL.md)

프로젝트 루트의 `.opencode/skills/` 또는 홈 디렉토리의 `~/.claude/skills/`에 직접 커스텀 skill을 추가할 수 있음.

**예시: `.opencode/skills/my-skill/SKILL.md`**

```markdown
---
name: my-skill
description: My special custom skill
mcp:
  my-mcp:
    command: npx
    args: ["-y", "my-mcp-server"]
---

# My Skill Prompt

이 내용이 에이전트의 시스템 프롬프트에 주입됨.
...
```

---

## 4. 조합 전략 (Combo)

Category와 Skill을 조합하여 강력한 전문 에이전트를 만들 수 있음.

### 🎨 The Designer (UI 구현)
- **Category**: `visual-engineering`
- **Skills**: `["frontend-ui-ux", "playwright"]`
- **효과**: 심미적인 UI를 구현하고 브라우저에서 직접 렌더링 결과를 검증함.

### 🏗️ The Architect (설계 검토)
- **Category**: `ultrabrain`
- **Skills**: `[]` (순수 추론)
- **효과**: GPT-5.2의 논리적 추론을 활용하여 시스템 아키텍처를 심층 분석함.

### ⚡ The Maintainer (빠른 수정)
- **Category**: `quick`
- **Skills**: `["git-master"]`
- **효과**: 비용 효율적인 모델을 사용하여 코드를 빠르게 수정하고 깔끔한 커밋을 생성함.

---

## 5. sisyphus_task 프롬프트 가이드

위임할 때 **명확하고 구체적인** 프롬프트가 필수임. 다음 7가지 요소를 포함해야 함:

1. **TASK**: 무엇을 해야 하는가? (단일 목표)
2. **EXPECTED OUTCOME**: 결과물이 무엇인가?
3. **REQUIRED SKILLS**: 어떤 skill을 사용해야 하는가?
4. **REQUIRED TOOLS**: 어떤 도구를 반드시 사용해야 하는가? (화이트리스트)
5. **MUST DO**: 반드시 해야 하는 것 (제약사항)
6. **MUST NOT DO**: 절대 하면 안 되는 것
7. **CONTEXT**: 파일 경로, 기존 패턴, 참고 자료

**나쁜 예시**:
> "이거 고쳐"

**좋은 예시**:
> **TASK**: `LoginButton.tsx`의 모바일 레이아웃 깨짐 문제 해결
> **CONTEXT**: `src/components/LoginButton.tsx`, Tailwind CSS 사용 중
> **MUST DO**: `md:` breakpoint에서 flex-direction 변경
> **MUST NOT DO**: 기존 데스크톱 레이아웃 수정 금지
> **EXPECTED**: 모바일에서 버튼들이 세로로 정렬됨

---

## 6. 설정 가이드 (oh-my-opencode.json)

`oh-my-opencode.json`에서 category를 세밀하게 조정할 수 있음.

### Category 설정 스키마 (CategoryConfig)

| 필드 | 타입 | 설명 |
|-------|------|-------------|
| `model` | string | 사용할 AI 모델 ID (예: `anthropic/claude-opus-4-5`) |
| `temperature` | number | 창의성 수준 (0.0 ~ 2.0). 낮을수록 결정론적임. |
| `prompt_append` | string | 이 category가 선택되었을 때 시스템 프롬프트에 추가할 내용 |
| `thinking` | object | Thinking 모델 설정 (`{ type: "enabled", budgetTokens: 16000 }`) |
| `tools` | object | 도구 사용 제어 (비활성화: `{ "tool_name": false }`) |
| `maxTokens` | number | 최대 응답 토큰 수 |

### 설정 예시

```jsonc
{
  "categories": {
    // 1. 새로운 커스텀 category 정의
    "korean-writer": {
      "model": "google/gemini-3-flash-preview",
      "temperature": 0.5,
      "prompt_append": "당신은 한국어 기술 작가입니다. 친근하고 명확한 어조를 유지하세요."
    },

    // 2. 기존 category 재정의 (모델 변경)
    "visual-engineering": {
      "model": "openai/gpt-5.2", // 모델 변경 가능
      "temperature": 0.8
    },

    // 3. thinking 모델 설정 및 도구 제한
    "deep-reasoning": {
      "model": "anthropic/claude-opus-4-5",
      "thinking": {
        "type": "enabled",
        "budgetTokens": 32000
      },
      "tools": {
        "websearch_web_search_exa": false // 웹 검색 비활성화
      }
    }
  },

  // Skill 비활성화
  "disabled_skills": ["playwright"]
}
```
