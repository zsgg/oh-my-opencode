---
description: 프로바이더(github-copilot, anthropic, openai, google)의 사용 가능한 모델 목록을 opencode 소스에서 찾음
allowed_arguments: github-copilot, anthropic, openai, google
---

## 작업
프로바이더의 전체 모델 카탈로그 찾기: $ARGUMENTS

## 지원 프로바이더
- `github-copilot`: 무료, 다양한 모델 접근 가능
- `anthropic`: claude-opus, claude-sonnet, claude-haiku
- `openai`: gpt-4o, gpt-5, o1, o3 등
- `google`: gemini-2.5, gemini-3 등

**주의**: 위 4개 프로바이더만 검색함. 다른 프로바이더는 지원하지 않음.

## 검색 전략

### Step 1: opencode 소스가 로컬에 있는지 확인
```bash
ls -la /tmp/opencode-src 2>/dev/null || echo "not found"
```

없으면 클론:
```bash
gh repo clone sst/opencode /tmp/opencode-src -- --depth 1
```

### Step 2: 모델 정의 파일 찾기
공식 모델 카탈로그 위치:
```
/tmp/opencode-src/packages/opencode/test/tool/fixtures/models-api.json
```

프로바이더 섹션 검색:
```bash
grep -A 500 '"$ARGUMENTS"' /tmp/opencode-src/packages/opencode/test/tool/fixtures/models-api.json | head -600
```

### Step 3: 프로바이더 구현 코드도 확인
```bash
grep -r "$ARGUMENTS" /tmp/opencode-src/packages/opencode/src/provider/ --include="*.ts"
grep -r "$ARGUMENTS" /tmp/opencode-src/packages/opencode/src/plugin/ --include="*.ts"
```

### Step 4: oh-my-opencode 매핑 확인
```bash
grep -r "$ARGUMENTS" /Users/Shared/Code-Personal/oh-my-opencode/src --include="*.ts" | head -50
```

## 예상 출력
마크다운 테이블로 반환:
| model id | name | attachment | status |
|----------|------|------------|--------|

**중요**: 출력 시 모델명은 소스코드에 있는 원본 그대로 사용할 것.
- `gpt-5.2` (O) / `GPT-5.2` (X)
- `claude-opus-4.5` (O) / `Claude Opus 4.5` (X)
- `gemini-3-flash-preview` (O) / `Gemini 3 Flash Preview` (X)

포함 내용:
- 모든 active 모델
- deprecated 모델 (표시)
- 이미지/오디오/비디오 입력 지원 여부

## 사용 예시
```
/find-model-catalog github-copilot
/find-model-catalog anthropic
/find-model-catalog openai
/find-model-catalog google
```
