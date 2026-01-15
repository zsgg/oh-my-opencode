# sync-models-config

oh-my-opencode 소스코드의 최신 에이전트 및 카테고리 설정을 분석하여 사용자 설정 파일(`$HOME/.config/opencode/oh-my-opencode.json`)을 동기화함.

## 목적
- 소스코드에 정의된 최신 에이전트 및 카테고리 목록을 사용자 설정에 실시간 반영
- 지원 중단된(Deprecated) 설정을 제거하고 누락된 설정을 보충
- 하드코딩된 모델명을 유효한 공급자 모델로 변환하여 동기화

## 핵심 원칙 (CRITICAL)

**모든 에이전트와 카테고리는 JSON에 명시적으로 작성되어야 함.**

- ❌ 암시적 기본값 의존 금지
- ❌ "기존 설정 유지" 금지 (변환 규칙 적용 필수)
- ✅ 소스코드에서 발견된 모든 항목을 JSON에 명시적으로 작성
- ✅ temperature가 소스에 없으면 JSON에서도 생략 (명시된 것만 작성)
- ✅ **`agents`와 `categories` 내부 키는 알파벳 A→Z 순으로 정렬**

## 모델 변환 규칙

사용 가능한 모델 공급자는 **codex**, **antigravity**, **claude**로 제한하며, 소스코드의 모델명을 아래 규칙에 따라 변환함.

| 원본 모델 패턴 | 변환 후 모델 | 공급자 |
|----------------|-------------|--------|
| `openai/gpt-*` | `openai/gpt-5.2` | codex |
| `opencode/*` | `google/antigravity-gemini-3-flash` | antigravity |
| `google/gemini-*-pro-preview` | `google/antigravity-gemini-3-pro-high` | antigravity |
| `google/gemini-*-flash-preview` | `google/antigravity-gemini-3-flash` | antigravity |
| `google/gemini-*-flash` | `google/antigravity-gemini-3-flash` | antigravity |
| `anthropic/claude-haiku-*` | `anthropic/claude-sonnet-4-5` | claude |
| `anthropic/claude-sonnet-*` | `anthropic/claude-sonnet-4-5` | claude |
| `anthropic/claude-opus-*` | `anthropic/claude-opus-4-5` | claude |

## 실행 지침

실행자는 매번 소스코드를 **새로 탐색(Fresh Discovery)**하여 현재 상태를 파악해야 함.

### Step 1: 소스코드 탐색

1. **스키마에서 에이전트/카테고리 목록 확인**:
   - `src/config/schema.ts`의 `OverridableAgentNameSchema` → 에이전트 이름 목록
   - `src/config/schema.ts`의 `BuiltinCategoryNameSchema` → 카테고리 이름 목록

2. **각 에이전트별 DEFAULT_MODEL과 temperature 탐색**:
   - `src/agents/*.ts` 파일들을 개별적으로 읽어서 `DEFAULT_MODEL`과 `temperature` 확인
   - 주요 파일: `sisyphus.ts`, `sisyphus-junior.ts`, `orchestrator-sisyphus.ts`, `oracle.ts`, `librarian.ts`, `explore.ts`, `frontend-ui-ux-engineer.ts`, `document-writer.ts`, `multimodal-looker.ts`, `metis.ts`, `momus.ts`

3. **카테고리별 기본 설정 탐색**:
   - `src/tools/sisyphus-task/constants.ts`의 `DEFAULT_CATEGORIES` 객체 전체 확인

### Step 2: 기존 설정 파일 읽기

JSON 저장 전에 **기존 설정 파일을 먼저 읽어서** 이후 비교에 사용:

```
기존 에이전트 목록: [...]
기존 카테고리 목록: [...]
```

### Step 3: 데이터 수집 및 변환

소스코드에서 수집한 정보에 모델 변환 규칙을 적용하여 정리.

### Step 4: JSON 생성 및 저장

**모든 에이전트와 카테고리를 명시적으로 포함하여 JSON 작성.**

**정렬 규칙: `agents`와 `categories` 내부 키는 알파벳 A→Z 순으로 정렬.**

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/master/assets/oh-my-opencode.schema.json",
  "google_auth": false,
  "agents": {
    "document-writer": { "model": "..." },
    "explore": { "model": "...", "temperature": 0.1 },
    "frontend-ui-ux-engineer": { "model": "..." },
    "librarian": { "model": "...", "temperature": 0.1 },
    "Metis (Plan Consultant)": { "model": "...", "temperature": 0.3 },
    "Momus (Plan Reviewer)": { "model": "...", "temperature": 0.1 },
    "multimodal-looker": { "model": "...", "temperature": 0.1 },
    "oracle": { "model": "...", "temperature": 0.1 },
    "orchestrator-sisyphus": { "model": "...", "temperature": 0.1 },
    "Sisyphus": { "model": "..." },
    "Sisyphus-Junior": { "model": "...", "temperature": 0.1 }
  },
  "categories": {
    "artistry": { "model": "...", "temperature": 0.9 },
    "general": { "model": "...", "temperature": 0.3 },
    "most-capable": { "model": "...", "temperature": 0.1 },
    "quick": { "model": "...", "temperature": 0.3 },
    "ultrabrain": { "model": "...", "temperature": 0.1 },
    "visual-engineering": { "model": "...", "temperature": 0.7 },
    "writing": { "model": "...", "temperature": 0.5 }
  }
}
```

### Step 5: 검증 (MANDATORY)

저장 후 반드시 검증:

1. **에이전트 개수 확인**: 스키마의 `OverridableAgentNameSchema` 항목 수 ≤ JSON의 `agents` 키 수
2. **카테고리 개수 확인**: 스키마의 `BuiltinCategoryNameSchema` 항목 수 = JSON의 `categories` 키 수
3. **모델 형식 확인**: 모든 모델이 `codex`, `antigravity`, `claude` 공급자 형식인지 확인
4. **정렬 확인**: `agents`와 `categories` 내부 키가 A→Z 순으로 정렬되어 있는지 확인

검증 실패 시 Step 2부터 재수행.

### Step 6: 변경사항 보고 (MANDATORY)

**소스코드의 DEFAULT 값과 변환 후 값을 비교하여 변경사항을 보고.**

**변경의 기준**: 소스코드의 DEFAULT_MODEL이 `a`인데, 변환 규칙에 의해 `b`로 변환되었을 때 = "변경됨"

표를 사용하지 말고 블릿 형식으로 작성.

```markdown
## 동기화 결과

### 변경됨 (모델 변환 적용됨)

소스코드 DEFAULT 값이 변환 규칙에 의해 다른 모델로 변환된 항목:

**에이전트:**
- `에이전트명`
  - model: `소스코드DEFAULT` → `변환후모델`

**카테고리:**
- `카테고리명`
  - model: `소스코드DEFAULT` → `변환후모델`

### 유지됨 (변환 없음)

소스코드 DEFAULT 값이 그대로 사용된 항목:

**에이전트:**
- `에이전트명` - model: `anthropic/claude-opus-4-5` (변환 불필요)

**카테고리:**
- `카테고리명` - model: `anthropic/claude-sonnet-4-5` (변환 불필요)
```

**변경됨 판단 기준:**
- 소스코드 DEFAULT_MODEL ≠ 변환 후 모델 → **변경됨**
- 소스코드 DEFAULT_MODEL = 변환 후 모델 → **유지됨**

**예시:**
- `opencode/grok-code` → `google/antigravity-gemini-3-flash` = **변경됨**
- `anthropic/claude-opus-4-5` → `anthropic/claude-opus-4-5` = **유지됨**

## 보존 규칙

- `$schema`, `google_auth` 등 `agents`, `categories` 외의 필드는 기존 값 유지
- 기존 설정 파일이 없으면 기본 템플릿으로 생성

## 검색 참조 명령어

```bash
# 에이전트 및 카테고리 스키마 확인
grep -A 30 "OverridableAgentNameSchema" src/config/schema.ts
grep -A 15 "BuiltinCategoryNameSchema" src/config/schema.ts

# 에이전트별 기본 설정 탐색 (개별 파일 읽기 권장)
grep -rE "const DEFAULT_MODEL|temperature:" src/agents/

# 카테고리별 기본 설정 탐색
grep -A 60 "DEFAULT_CATEGORIES" src/tools/sisyphus-task/constants.ts
```

## 제외 대상 에이전트

다음 에이전트들은 시스템 내부용이므로 사용자 설정에서 제외 가능:
- `build`, `plan` - OpenCode 기본 에이전트 (동적 설정)
- `OpenCode-Builder`, `Prometheus (Planner)` - 시스템 관리 에이전트

단, 스키마에 포함되어 있으므로 사용자가 원하면 override 가능.
