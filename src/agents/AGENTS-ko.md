# 에이전트 지식 베이스 (AGENTS KNOWLEDGE BASE)

## 개요 (OVERVIEW)

다중 모델 오케스트레이션을 위한 AI 에이전트 정의. 7개의 전문화된 에이전트: Sisyphus (오케스트레이터), oracle (읽기 전용 컨설팅), librarian (리서치), explore (grep), frontend-ui-ux-engineer, document-writer, multimodal-looker.

## 구조 (STRUCTURE)

```
agents/
├── orchestrator-sisyphus.ts # 오케스트레이터 에이전트 (1484줄) - 복잡한 위임
├── sisyphus.ts              # 메인 Sisyphus 프롬프트 (641줄)
├── sisyphus-junior.ts       # 위임된 작업을 위한 Junior 변형
├── oracle.ts                # 전략적 어드바이저 (GPT-5.2)
├── librarian.ts             # 멀티 레포지토리 리서치 (Claude Sonnet 4.5)
├── explore.ts               # 빠른 코드베이스 grep (Grok Code)
├── frontend-ui-ux-engineer.ts  # UI 생성 (Gemini 3 Pro)
├── document-writer.ts       # 기술 문서 작성 (Gemini 3 Pro)
├── multimodal-looker.ts     # PDF/이미지 분석 (Gemini 3 Flash)
├── prometheus-prompt.ts     # 계획 에이전트 프롬프트 (982줄)
├── metis.ts                 # Plan Consultant 에이전트 (404줄)
├── momus.ts                 # Plan Reviewer 에이전트 (404줄)
├── build-prompt.ts          # 공유 빌드 에이전트 프롬프트
├── plan-prompt.ts           # 공유 계획 에이전트 프롬프트
├── types.ts                 # AgentModelConfig 인터페이스
├── utils.ts                 # createBuiltinAgents(), getAgentName()
└── index.ts                 # builtinAgents 내보내기(export)
```

## 에이전트 모델 (AGENT MODELS)

| 에이전트 | 기본 모델 | 폴백(Fallback) | 목적 |
|-------|---------------|----------|---------|
| Sisyphus | anthropic/claude-opus-4-5 | - | 확장된 사고(Extended Thinking) 기능을 갖춘 기본 오케스트레이터 |
| oracle | openai/gpt-5.2 | - | 읽기 전용 컨설팅. 고수준 디버깅, 아키텍처 |
| librarian | opencode/glm-4.7-free | - | 문서 조회, 오픈소스 리서치, GitHub 예시 검색 |
| explore | opencode/grok-code | google/gemini-3-flash, anthropic/claude-haiku-4-5 | 빠른 문맥 기반 grep 검색 |
| frontend-ui-ux-engineer | google/gemini-3-pro-preview | - | UI/UX 코드 생성 |
| document-writer | google/gemini-3-pro-preview | - | 기술 문서 작성 |
| multimodal-looker | google/gemini-3-flash | - | PDF 및 이미지 분석 |

## 에이전트 추가 방법 (HOW TO ADD AN AGENT)

1. `src/agents/my-agent.ts` 파일을 생성:
   ```typescript
   import type { AgentConfig } from "@opencode-ai/sdk"

   export const myAgent: AgentConfig = {
     model: "provider/model-name",
     temperature: 0.1,
     system: "Agent system prompt...",
     tools: { include: ["tool1", "tool2"] },  // 또는 exclude: [...]
   }
   ```
2. `src/agents/index.ts`의 `builtinAgents`에 추가
3. 새로운 설정 옵션을 추가하는 경우 `types.ts`를 업데이트

## 에이전트 설정 옵션 (AGENT CONFIG OPTIONS)

| 옵션 | 타입 | 설명 |
|--------|------|-------------|
| model | string | 모델 식별자 (provider/model-name) |
| temperature | number | 0.0-1.0 범위, 일관성을 위해 대부분 0.1 사용 |
| system | string | 시스템 프롬프트 (멀티라인 템플릿 리터럴 가능) |
| tools | object | `{ include: [...] }` 또는 `{ exclude: [...] }` |
| top_p | number | (선택 사항) Nucleus 샘플링 값 |
| maxTokens | number | (선택 사항) 최대 출력 토큰 수 |

## 모델 폴백 로직 (MODEL FALLBACK LOGIC)

`utils.ts`의 `createBuiltinAgents()` 함수가 모델 폴백을 처리함:

1. 사용자 설정의 오버라이드 확인 (`agents.{name}.model`)
2. 설치 프로그램(Installer) 설정 확인 (claude max20, gemini antigravity 등)
3. 기본 모델(Default model) 사용

**explore 에이전트의 폴백 순서:**
- gemini antigravity 활성화 시 → `google/gemini-3-flash`
- claude max20 활성화 시 → `anthropic/claude-haiku-4-5`
- 기본값 → `opencode/grok-code` (무료)

## 안티패턴 (ANTI-PATTERNS - AGENTS)

- **높은 Temperature**: 코드 관련 에이전트에는 0.3보다 높은 값을 사용하지 마세요.
- **광범위한 도구 접근**: 제한 없는 접근보다는 명시적인 `include`를 사용하는 것을 권장함.
- **거대한 단일 프롬프트**: 프롬프트를 특정 목적에 집중시키고, 복잡한 작업은 전문화된 에이전트에게 위임하세요.
- **폴백 설정 누락**: 요율 제한(Rate limit)이 있는 모델을 사용할 경우, 무료 또는 저렴한 폴백 모델 설정을 고려하세요.

## 공유 프롬프트 (SHARED PROMPTS)

- **build-prompt.ts**: 빌드 에이전트를 위한 기본 프롬프트 (OpenCode 기본 설정 + Sisyphus 변형 모델용)
- **plan-prompt.ts**: 계획 에이전트를 위한 기본 프롬프트 (레거시)
- **prometheus-prompt.ts**: Prometheus (Planner) 에이전트를 위한 시스템 프롬프트
- **metis.ts**: 계획 전 분석을 위한 Metis (Plan Consultant) 에이전트

`src/index.ts`에서 Builder-Sisyphus 및 Prometheus (Planner) 변형을 생성할 때 사용됨.
