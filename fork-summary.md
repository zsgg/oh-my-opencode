## Upstream 동기화 요약 (135개 커밋)

이번 동기화로 **v3.0.0-beta.11 → v3.0.0-beta.12** 업데이트가 반영됨. 핵심 변경사항을 쉽게 정리해줄게.

---

### 1. 대규모 리네이밍: `orchestrator-sisyphus` → `Atlas`

**가장 큰 변화.** 오케스트레이터 에이전트 이름이 바뀜.

| 이전 | 이후 |
|------|------|
| `orchestrator-sisyphus` | `Atlas` |
| `sisyphus-orchestrator` | `atlas` |

- 기존 설정 사용자를 위한 migration mapping 추가됨
- 관련 hook, schema, plugin references 전부 업데이트됨

---

### 2. Model Fallback System 완전 재설계

**모델 선택이 훨씬 똑똑해짐.**

- **3단계 resolution**: agent → category → provider 순서로 fallback
- **Fuzzy matching**: 모델 이름 대소문자 안 가려도 됨
- **Provider priority**: 어떤 provider 우선 사용할지 설정 가능
- **Native cross-fallback**: OpenAI 분리, provider간 자동 전환
- `doctor` 명령어에 model resolution 체크 추가

**설정 예시 변화:**
```json
// 이제 providerConcurrency에 0 넣으면 해당 provider 비활성화 가능
"providerConcurrency": {
  "anthropic": 2,
  "openai": 0  // openai 사용 안함
}
```

---

### 3. Background Agent 대폭 개선

**병렬 작업이 훨씬 부드러워짐.**

| 기능 | 설명 |
|------|------|
| **Non-blocking launch()** | agent 실행이 메인 스레드 안 막음 |
| **Per-key queue** | 키별로 독립적인 작업 큐 |
| **Pending status** | 대기 중인 작업 상태 표시 |
| **queuedAt 필드** | 언제 큐에 들어갔는지 추적 |
| **Pending 취소 지원** | 대기 중인 작업도 취소 가능 |
| **Premature completion 방지** | agent가 아직 돌아가는데 완료 처리되는 버그 수정 |

**UI 개선:**
- toast와 background_output에 pending/queued 상태 보여줌

---

### 4. 에이전트 정리

**중복/불필요한 agent 삭제됨:**

| 삭제됨 | 대체 |
|--------|------|
| `frontend-ui-ux-engineer` agent | `visual` category 사용 |
| `document-writer` agent | `writing` category 사용 |
| `createSisyphusJuniorAgent` 함수 | 사용 안함 |

---

### 5. Category System 재구조화

**편향 없는 모델 선택을 위해 개편됨:**

- `is_unstable_agent` 옵션 추가 (불안정한 agent는 자동으로 background 모드)
- `description` 필드 추가 (category 설명)
- category built-in model이 inherited model보다 우선함
- category model catalog와 default models 추가

---

### 6. 환경 변수 & 경로 수정

- `OPENCODE_CONFIG_DIR` 환경변수 전체 경로에서 반영됨
- `skills` 디렉토리 경로 수정
- `config.data.model` 접근 패턴 수정

---

### 7. CLI 개선

| 변경 | 내용 |
|------|------|
| `--chatgpt` → `--openai` | 잘못된 옵션명 수정 |
| ChatGPT subscription check 제거 | installer에서 삭제 |
| Session creation retry | 세션 생성 실패 시 재시도 |
| doctor check | model resolution 검사 추가 |

---

### 8. Hooks 개선

- keyword detector patterns 확장
- prometheus-md-only 패턴 개선
- delegate-task-retry naming consistency 수정
- 여러 hook들 minor fixes

---

### 9. 문서 & CI

**문서:**
- Model Selection System 문서 추가
- features.md 구조 재정리 (agents, skills, commands, hooks)
- **ohmyopencode.com 사칭 사이트 경고 추가** (중요!)
- 언어별 README 설치 가이드 동기화

**CI:**
- platform publish 별도 workflow 분리
- OIDC token expiration 방지를 위한 병렬 publish
- republish mode 추가

---

### 10. 기타 버그 수정들

- Sisyphus/Prometheus에 question tool 활성화
- zod v4 record schema key type 추가
- optional 필드들에 null check 추가
- 오타 수정 (IDEALY→IDEALLY, EXPLICITELY→EXPLICITLY)
- README 깨진 링크 수정

---

## 한 줄 요약

> **orchestrator가 Atlas로 이름 바뀌고, 모델 선택이 3단계 fallback으로 똑똑해지고, background agent가 non-blocking queue 시스템으로 개선됨. 불필요한 agent들 정리되고, category system 재정비됨.**

기존 설정은 migration mapping 덕분에 자동으로 호환됨. 별도 수정 필요 없음.
