# sync-models-config

oh-my-opencode 소스코드의 최신 에이전트 및 카테고리 설정을 분석하여 사용자 설정 파일(`$HOME/.config/opencode/oh-my-opencode.json`)을 동기화함.

## 목적

- 소스코드의 **fallback chain 기본값**이 사용자 환경에서 지원되지 않는 provider일 수 있음
- 이를 방지하기 위해 모든 에이전트/카테고리의 모델을 **명시적으로 설정 파일에 작성**
- 설정 파일에 명시된 모델이 **소스코드의 fallback chain보다 우선**됨

## 핵심 원칙 (CRITICAL)

**1. 모든 에이전트와 카테고리는 JSON에 명시적으로 작성되어야 함.**

- ❌ 암시적 기본값 의존 금지 (fallback chain에 미지원 provider 포함 가능)
- ❌ "기존 설정 유지" 금지 (변환 규칙 적용 필수)
- ✅ 소스코드에서 **매번 새로 탐색**하여 최신 목록/값 확인
- ✅ temperature/variant가 소스에 명시된 경우만 JSON에 작성
- ✅ **`agents`와 `categories` 내부 키는 알파벳 A→Z 순으로 정렬**

**2. 설정 파일 경로**

- **반드시** `$HOME/.config/opencode/oh-my-opencode.json`에 저장
- 프로젝트별 설정이 아닌 사용자 전역 설정

## 모델 변환 규칙

사용 가능한 모델 공급자는 **codex**, **github-copilot**, **claude**로 제한하며, 소스코드의 모델명을 아래 규칙에 따라 변환함.

| 원본 모델 패턴 | 변환 후 모델 | 공급자 |
|----------------|-------------|--------|
| `openai/gpt-*` | `openai/gpt-5.2` | codex |
| `openai/gpt-*-codex` | `openai/gpt-5.2` | codex |
| `opencode/*` (모든 opencode 모델) | `anthropic/claude-sonnet-4-5` | claude |
| `zai-coding-plan/*` (모든 zai 모델) | `anthropic/claude-sonnet-4-5` | claude |
| `google/gemini-*-pro-preview` | `github-copilot/gemini-3-pro-preview` | github-copilot |
| `google/gemini-*-flash-preview` | `github-copilot/gemini-3-flash-preview` | github-copilot |
| `google/gemini-*-flash` | `github-copilot/gemini-3-flash-preview` | github-copilot |
| `anthropic/claude-haiku-*` | `anthropic/claude-sonnet-4-5` | claude |
| `anthropic/claude-sonnet-*` | `anthropic/claude-sonnet-4-5` | claude |
| `anthropic/claude-opus-*` | `anthropic/claude-opus-4-5` | claude |

**중요**: 미지원 provider의 모델은 모두 안전한 모델로 변환

## 실행 지침

실행자는 매번 소스코드를 **새로 탐색(Fresh Discovery)**하여 현재 상태를 파악해야 함.

**소스코드가 자주 변경되므로 절대 이전 분석 결과나 이 프롬프트의 예시를 재사용하지 말 것.**

### Step 1: 소스코드 탐색 (FRESH DISCOVERY)

**1. 에이전트/카테고리 목록 확인:**

```
src/config/schema.ts 파일을 읽고:
- OverridableAgentNameSchema → JSON에 포함할 에이전트 목록
- BuiltinCategoryNameSchema → JSON에 포함할 카테고리 목록
```

**2. 에이전트 기본 모델 확인:**

```
src/shared/model-requirements.ts 파일을 읽고:
- AGENT_MODEL_REQUIREMENTS 객체에서 각 에이전트별 fallback chain 확인
- fallback chain의 첫 번째 항목이 기본값
- variant 필드가 있으면 기록
```

**3. 에이전트별 temperature 확인:**

```
src/agents/ 폴더의 각 에이전트 파일을 읽고:
- temperature 값이 명시된 경우 기록
- 명시되지 않은 경우 생략
```

**4. 카테고리 기본 모델 확인:**

```
src/shared/model-requirements.ts 파일을 읽고:
- CATEGORY_MODEL_REQUIREMENTS 객체에서 각 카테고리별 fallback chain 확인

src/tools/delegate-task/constants.ts 파일을 읽고:
- DEFAULT_CATEGORIES 객체에서 variant, temperature 값 확인
```

### Step 2: 기존 설정 파일 읽기

```bash
cat $HOME/.config/opencode/oh-my-opencode.json
```

### Step 3: 데이터 수집 및 변환

소스코드에서 수집한 정보에 모델 변환 규칙을 적용:

```
원본: fallback chain 첫 번째 항목의 provider/model
  ↓ 변환 규칙 적용
결과: 사용자 환경에서 지원되는 provider/model
```

### Step 4: JSON 생성 및 저장

**모든 에이전트와 카테고리를 명시적으로 포함하여 JSON 작성.**

**정렬 규칙: `agents`와 `categories` 내부 키는 알파벳 A→Z 순으로 정렬.**

**경로: `$HOME/.config/opencode/oh-my-opencode.json`**

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/master/assets/oh-my-opencode.schema.json",
  "agents": {
    "[스키마에서 읽은 에이전트명]": { "model": "[변환된모델]" },
    "[스키마에서 읽은 에이전트명]": { "model": "[변환된모델]", "temperature": [소스에서읽은값] }
  },
  "categories": {
    "[스키마에서 읽은 카테고리명]": { "model": "[변환된모델]" },
    "[스키마에서 읽은 카테고리명]": { "model": "[변환된모델]", "variant": "[소스에서읽은값]" }
  }
}
```

**제외 대상 (JSON에 포함하지 않음):**
- `build`, `plan` - OpenCode 기본 에이전트 (시스템 관리)
- `OpenCode-Builder` - 시스템 내부용
- 스키마에 정의되지 않은 에이전트/카테고리

### Step 5: 검증 (MANDATORY)

저장 후 반드시 검증:

1. **에이전트 개수 확인**: 스키마의 `OverridableAgentNameSchema` 항목 중 제외 대상 제외한 수 = JSON의 `agents` 키 수
2. **카테고리 개수 확인**: 스키마의 `BuiltinCategoryNameSchema` 항목 수 = JSON의 `categories` 키 수
3. **모델 형식 확인**: 모든 모델이 변환 규칙에 따른 형식인지 확인
4. **미지원 provider 없음 확인**: 변환 규칙 테이블의 "원본 모델 패턴"에 해당하는 provider 모델이 없어야 함
5. **정렬 확인**: `agents`와 `categories` 내부 키가 A→Z 순으로 정렬되어 있는지 확인

검증 실패 시 Step 1부터 재수행.

### Step 6: 변경사항 보고 (MANDATORY)

```markdown
## 동기화 결과

### 변경됨 (모델 변환 적용됨)

소스코드 fallback chain 첫 번째 값이 변환 규칙에 의해 다른 모델로 변환된 항목:

**에이전트:**
- `[에이전트명]`
  - model: `[원본모델]` → `[변환후모델]`

**카테고리:**
- `[카테고리명]`
  - model: `[원본모델]` → `[변환후모델]`

### 유지됨 (변환 없음)

소스코드 fallback chain 값이 그대로 사용된 항목:

**에이전트:**
- `[에이전트명]` - model: `[모델]` (변환 불필요)
```

## 보존 규칙

- `$schema` 등 `agents`, `categories` 외의 필드는 기존 값 유지
- 기존 설정 파일이 없으면 기본 템플릿으로 생성

## 참조 파일 (반드시 읽어야 함)

| 파일 | 확인 대상 |
|------|-----------|
| `src/config/schema.ts` | 에이전트/카테고리 스키마 (목록) |
| `src/shared/model-requirements.ts` | fallback chain (기본 모델/variant) |
| `src/tools/delegate-task/constants.ts` | 카테고리 기본값 (temperature, variant) |
| `src/agents/*.ts` | 각 에이전트별 temperature |

## 변환 예시 (참고용)

```
[에이전트/카테고리]:
  fallbackChain[0]: { providers: ["미지원provider"], model: "원본모델" }
  → 변환 규칙 적용
  → JSON: { "model": "변환된모델", "temperature": [있으면포함] }
```
