# Agent Documentation Translation Guide

에이전트 문서 한글 번역 작업 가이드입니다.

## 개요

`src/agents/` 디렉토리의 에이전트 설정(AgentConfig)에서 `description`과 `prompt` 필드를 한글로 번역하여 `<name>.ko.md` 파일로 생성합니다.

## 대상 파일

| 소스 파일 | 번역 파일 |
|-----------|-----------|
| `sisyphus.ts` | `sisyphus.ko.md` |
| `oracle.ts` | `oracle.ko.md` |
| `librarian.ts` | `librarian.ko.md` |
| `explore.ts` | `explore.ko.md` |
| `frontend-ui-ux-engineer.ts` | `frontend-ui-ux-engineer.ko.md` |
| `document-writer.ts` | `document-writer.ko.md` |
| `multimodal-looker.ts` | `multimodal-looker.ko.md` |
| `build-prompt.ts` | `build-prompt.ko.md` |
| `plan-prompt.ts` | `plan-prompt.ko.md` |

추가로 `AGENTS.md` → `AGENTS-ko.md` 번역도 포함됩니다.

## 번역 파일 형식

각 `.ko.md` 파일은 다음 구조를 따릅니다:

```markdown
# [Agent Name] ([한글 이름])

## 설명 (Description)

[description 필드의 한글 번역]

## 프롬프트 (Prompt)

[prompt 필드의 한글 번역 - 마크다운 형식 유지]
```

## 번역 규칙

### 반드시 번역할 것
- 설명 텍스트
- 프롬프트 내 지시사항
- 마크다운 테이블의 설명 컬럼
- 주석 및 가이드라인

### 영문 유지할 것
- 코드 스니펫
- 파일 경로 (`src/agents/`, `/tmp/repo` 등)
- 변수명 및 함수명
- 명령어 구문 (`git log`, `grep`, `gh repo clone` 등)
- 기술 용어 (LSP, API, grep, git, AST, MCP 등)
- 테이블의 기술적 값 (모델명, 도구명 등)

### 번역 스타일
- 자연스러운 한국어 표현 사용 (기계 번역체 금지)
- 전문적이고 기술적인 톤 유지
- 원문의 의미와 기술적 정확성 보존
- 원문에 없는 내용 추가 금지

## 실행 방법

### 방법 1: document-writer 에이전트 사용

```
@document-writer src/agents 의 AgentConfig description과 prompt를 한글로 번역하여 <name>.ko.md 파일로 생성해줘.
```

### 방법 2: 수동 번역

1. 각 `.ts` 파일에서 `description`과 `prompt` 필드 추출
2. 위 형식에 맞춰 `.ko.md` 파일 생성
3. 번역 규칙에 따라 번역 수행

## 검증 체크리스트

- [ ] 모든 대상 파일이 생성되었는가?
- [ ] 파일 형식이 일관적인가?
- [ ] 코드/경로/명령어가 영문으로 유지되었는가?
- [ ] 한국어 표현이 자연스러운가?
- [ ] 기술적 정확성이 유지되었는가?
- [ ] 마크다운 테이블/코드블록 형식이 올바른가?

## 파일 위치

- 소스: `src/agents/*.ts`
- 번역: `src/agents/*.ko.md`
- 지식베이스: `src/agents/AGENTS.md` → `src/agents/AGENTS-ko.md`
