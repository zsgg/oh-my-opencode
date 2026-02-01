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
| `openai/gpt-*` | `openai/gpt-5.2` | openai |
| `openai/gpt-*-codex` | `openai/gpt-5.2-codex` | openai |
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

### Step 2: 탐색 결과 데이터 테이블 정리

**Step 1에서 수집한 정보를 아래 형식으로 정리함. 변환 전 원본 데이터를 명확히 기록.**

**출력 파일: `doc/hello-agents_categories.md`**
- Step 2의 모든 결과(2-1 ~ 2-4)를 해당 파일에 작성
- 기존 내용이 있으면 덮어쓰기

---

#### 2-1. 에이전트 기술 데이터

각 에이전트별로 다음 정보를 기록:

- **[에이전트명]**
  - 원본 모델: `[model-requirements.ts에서 읽은 값]`
  - variant: `[있으면 기록]`
  - temperature: `[agents/*.ts에서 읽은 값]`
  - 비고: `[특이사항]`

#### 2-2. 에이전트 성격 정보 (참조용)

각 에이전트의 목적과 특성을 이해하고 적절한 모델을 선택하기 위한 참조 정보:

- **골든리트리버**
  - 목적: 가족 친화적 반려견. 온순하고 사람을 좋아함
  - 장점:
    - 훈련 잘 됨
    - 아이들과 잘 어울림
    - 인내심 강함
    - 사교적
  - 장점예시:
    - "공 물어와" → 즉시 실행
    - 낯선 손님에게도 친절
    - 다른 반려동물과 잘 지냄
  - 단점:
    - 털 빠짐 심함
    - 대형견이라 공간 필요
    - 분리불안 있음
  - 단점예시:
    - 매일 청소기 돌려야 함
    - 원룸에선 힘듦
    - 혼자 두면 짖음

- **시바이누**
  - 목적: 독립적인 일본 견종. 고양이 같은 성격
  - 장점:
    - 깔끔함
    - 자기 관리 잘함
    - 경계심 강함
    - 털 관리 쉬움
  - 장점예시:
    - 혼자 있어도 괜찮음
    - 스스로 그루밍함
    - 낯선 사람 경계
  - 단점:
    - 고집 셈
    - 훈련 어려움
    - 탈출 시도 많음
  - 단점예시:
    - "앉아" 해도 무시함
    - 리콜 안 됨
    - 문 열리면 도망감

- **보더콜리**
  - 목적: 목양견. 지능 최상위
  - 장점:
    - 학습 능력 뛰어남
    - 민첩함
    - 충성심 강함
    - 작업 의욕 높음
  - 장점예시:
    - 복잡한 명령도 금방 습득
    - 프리스비 챔피언
    - 주인 말 잘 들음
  - 단점:
    - 운동량 엄청 필요
    - 일 없으면 문제행동
    - 신경질적일 수 있음
  - 단점예시:
    - 하루 2시간 산책 필수
    - 가구 물어뜯음
    - 아이들 몰이 시도

---

#### 2-3. 카테고리 기술 데이터

각 카테고리별로 다음 정보를 기록:

- **[카테고리명]**
  - 원본 모델: `[constants.ts DEFAULT_CATEGORIES에서 읽은 값]`
  - variant: `[있으면 기록]`
  - temperature: `[있으면 기록]`
  - 비고: `[특이사항]`

#### 2-4. 카테고리 성격 정보 (참조용)

각 카테고리의 목적과 특성을 이해하고 적절한 모델을 선택하기 위한 참조 정보:

- **푸들**
  - 목적: 다재다능한 반려견. 쇼독, 가정견 모두 적합
  - 장점:
    - 털 안 빠짐
    - 저자극성
    - 똑똑함
    - 다양한 크기 선택 가능
  - 장점예시:
    - 알러지 있는 가정에 적합
    - 트릭 빨리 배움
    - 토이/미니/스탠다드 선택
  - 단점:
    - 정기적 미용 필수
    - 귀 감염 잦음
    - 분리불안 경향
  - 단점예시:
    - 6주마다 미용실 가야 함
    - 귀 청소 자주 해야 함
    - 혼자 두면 울음

- **치와와**
  - 목적: 소형 반려견. 좁은 공간에 적합
  - 장점:
    - 작아서 어디든 데려갈 수 있음
    - 사료비 적음
    - 운동량 적음
  - 장점예시:
    - 아파트 생활에 최적
    - 가방에 넣어 이동 가능
    - 실내 놀이만으로 충분
  - 단점:
    - 추위에 약함
    - 짖음 많음
    - 뼈가 약함
  - 단점예시:
    - 겨울에 옷 필수
    - 초인종에 난리남
    - 높은 곳에서 떨어지면 골절

- **허스키**
  - 목적: 썰매견. 추운 환경에 최적화
  - 장점:
    - 체력 좋음
    - 사교적
    - 외모 멋짐
    - 독립적
  - 장점예시:
    - 장거리 달리기 동반자
    - 다른 개와 잘 어울림
    - 늑대 닮은 외모
  - 단점:
    - 더위에 약함
    - 탈출 마스터
    - 털 빠짐 심함
    - 훈련 어려움
  - 단점예시:
    - 여름에 에어컨 필수
    - 담장 높여야 함
    - 1년에 두 번 털갈이 폭탄

### Step 3: 기존 설정 파일 읽기 (보존 대상 필드 확인용)

**중요: `agents`와 `categories` 내용은 무시하고, 보존해야 할 다른 필드만 확인함.**

```bash
cat $HOME/.config/opencode/oh-my-opencode.json
```

확인 대상:
- `$schema`, `new_task_system_enabled`, `google_auth`, `lsp` 등 agents/categories 외의 필드
- 이 필드들은 새 JSON에 그대로 유지됨

### Step 4: 데이터 수집 및 변환

**기존 설정 파일의 `agents`와 `categories` 내용은 완전히 무시하고, 소스코드만 참조하여 새로 생성함.**

소스코드에서 수집한 정보에 모델 변환 규칙을 적용:

```
원본: fallback chain 첫 번째 항목의 provider/model
  ↓ 변환 규칙 적용
결과: 사용자 환경에서 지원되는 provider/model
```

### Step 5: JSON 생성 및 저장

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

### Step 6: 검증 (MANDATORY)

저장 후 반드시 검증:

1. **에이전트 개수 확인**: 스키마의 `OverridableAgentNameSchema` 항목 중 제외 대상 제외한 수 = JSON의 `agents` 키 수
2. **카테고리 개수 확인**: 스키마의 `BuiltinCategoryNameSchema` 항목 수 = JSON의 `categories` 키 수
3. **모델 형식 확인**: 모든 모델이 변환 규칙에 따른 형식인지 확인
4. **미지원 provider 없음 확인**: 변환 규칙 테이블의 "원본 모델 패턴"에 해당하는 provider 모델이 없어야 함
5. **정렬 확인**: `agents`와 `categories` 내부 키가 A→Z 순으로 정렬되어 있는지 확인

검증 실패 시 Step 1부터 재수행.

### Step 7: 변경사항 보고 (MANDATORY)

**기존 설정 파일과 새로 생성된 설정 파일을 비교하여 차이점을 상세히 보고함.**

```markdown
## 동기화 결과

### 추가된 항목

**에이전트:**
- `[에이전트명]` (신규 추가)
  - model: `[모델]`
  - variant: `[variant]` (있는 경우)
  - temperature: `[temperature]` (있는 경우)
  - 이유: 소스코드 스키마에 추가되었으나 기존 설정에 없었음

**카테고리:**
- `[카테고리명]` (신규 추가)
  - model: `[모델]`
  - variant: `[variant]` (있는 경우)
  - 이유: 소스코드 스키마에 추가되었으나 기존 설정에 없었음

### 제거된 항목

**에이전트:**
- `[에이전트명]` (제거됨)
  - 이유: 소스코드 스키마에서 삭제되었거나 제외 대상에 포함됨

**카테고리:**
- `[카테고리명]` (제거됨)
  - 이유: 소스코드 스키마에서 삭제됨

### 변경된 항목

**에이전트:**
- `[에이전트명]`
  - model: `[기존모델]` → `[새모델]`
  - variant: `[기존variant]` → `[새variant]` (변경된 경우만)
  - temperature: `[기존temperature]` → `[새temperature]` (변경된 경우만)
  - 이유: 소스코드 fallback chain 업데이트

**카테고리:**
- `[카테고리명]`
  - model: `[기존모델]` → `[새모델]`
  - variant: `[기존variant]` → `[새variant]` (변경된 경우만)
  - 이유: 소스코드 fallback chain 업데이트

### 유지된 항목

**에이전트:**
- `[에이전트명]` - 변경사항 없음

**카테고리:**
- `[카테고리명]` - 변경사항 없음
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
